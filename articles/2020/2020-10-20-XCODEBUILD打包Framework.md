## XCODEBUILD 打包Framework

<img src="../../assets/image-20201209143223140.png" alt="image-20201209143223140" style="zoom:50%;" />

上图是在 Terminal 输入: `man xcodebuild` 看到的手册。`xcodebuild --help` 可以获得更详细的帮助：

<img src="../../assets/image-20201209143551395.png" alt="image-20201209143551395" style="zoom:50%;" />

### XCODEBUILD

> xcodebuild builds one or more targets contained in an Xcode project, or builds a scheme contained in an Xcode workspace or Xcode project.

```sh
xcodebuild \
[-project name.xcodeproj] \
[[-target targetname] ... | -alltargets] [-configuration configurationname] [-sdk [sdkfullpath | sdkname]] \
[action ...] \
[buildsetting=value ...] \
[-userdefault=value ...] \
```

xcodebuild 的 Options 可以自行查阅手册这里不再赘述。其中我想说的打包framework常用的 action 其实就只有：

- `build` , *Build the target in the build root (SYMROOT).  This is the default action, and is used if no action is given.*
- `clean` , *Remove build products and intermediate files from the build root (SYMROOT).*

### 应用

#### 打包脚本

```shell
#!/bin/sh
set -e

# 打印函数
print_warn() {
    echo "\033[0;33m"$1"\033[0m"
}
print_error() {
    echo "\033[0;31m"$1"\033[0m"
}

print_success() {
    echo "\033[0;32m"$1"\033[0m"
}

# 插件安装判断
has_install() {
#https://stackoverflow.com/questions/592620/how-can-i-check-if-a-program-exists-from-a-bash-script/
    if type $1 >/dev/null 2>&1;
    then
        return 0
    else
        return 1
    fi
}

# 定义常量
Target_Name=${PROJECT_NAME}
FRAMEWORK_NAME=${Product_Name}.framework
CONFIGURATION=Release
INSTALL_DIR=${SRCROOT}/Products
INSTALL_FILE=${INSTALL_DIR}/${FRAMEWORK_NAME}
# 定义一个临时真机路径来保存打包的文件，适配New Build System
IPHONE_DIR=${INSTALL_DIR}/${CONFIGURATION}-iphoneos

# 模拟器
SIMULATOR_DIR=${WRK_DIR}/${CONFIGURATION}-iphonesimulator/${FRAMEWORK_NAME}
IPHONE_SIMULATOR_DIR=${INSTALL_DIR}/${CONFIGURATION}-iphonesimulator

# 是否使用新的build系统 
_UseModernBuildSystem=NO

# 先清理旧文件
if [ -d "${INSTALL_DIR}" ]
then
rm -rf "${INSTALL_DIR}"
fi
mkdir -p "${INSTALL_DIR}"


# 编译函数
build_cmd() {
    if [[ $# -ne 2 ]]; then
        print_error "参数错误"
        exit 1
    fi
    currentARCHS="arm64 armv7"
    if [[ $1 == "iphonesimulator" ]];then
        currentARCHS="i386 x86_64"
    fi
    
    xcodebuild \
    -target "${Target_Name}" \
    -configuration "${CONFIGURATION}" \
    -sdk "${1}" \
    -UseModernBuildSystem=${_UseModernBuildSystem} \
    CONFIGURATION_BUILD_DIR="${2}" \
    BITCODE_GENERATION_MODE=bitcode \
    ENABLE_BITCODE=YES \
    ARCHS="${currentARCHS}" \
    clean build
}

iphone_sdk=iphoneos
simulator_sdk=iphonesimulator

print_warn "开始打包真机SDK..."

# 用来格式化（美化）xcodebuild命令行工具输出日志的一个小工具
xc_name="xcpretty"

result=1
if has_install ${xc_name}; then
    build_cmd "${iphone_sdk}" "${IPHONE_DIR}" | xcpretty && result=${PIPESTATUS[0]}
else
    print_warn "请安装${xc_name}命令：sudo gem install ${xc_name}"
    build_cmd "${iphone_sdk}" "${IPHONE_DIR}"
    result=$?
fi

if [[ result -ne 0 ]]; then
    print_error "打包真机sdk失败"
    exit ${result}
fi

# 拷贝 Headers、info.plist Modules 等内容，在 lipo 合成的时候使用
cp -r "${IPHONE_DIR}/${FRAMEWORK_NAME}" "${INSTALL_FILE}"

print_warn "开始打包模拟器SDK..."
if has_install ${xc_name}; then
    build_cmd "${simulator_sdk}" "${IPHONE_SIMULATOR_DIR}" | xcpretty && result=${PIPESTATUS[0]}
else
    build_cmd "${simulator_sdk}" "${IPHONE_SIMULATOR_DIR}"
    result=$?
fi

if [[ result -ne 0 ]]; then
    print_error "打包模拟器sdk失败"
    exit ${result}
fi

# 合并真机和模拟器生成通用库
lipo -create "${IPHONE_DIR}/${FRAMEWORK_NAME}/${Product_Name}" "${IPHONE_SIMULATOR_DIR}/${FRAMEWORK_NAME}/${Product_Name}" -output "${INSTALL_FILE}/${Product_Name}"

rm -r "${IPHONE_DIR}" "${IPHONE_SIMULATOR_DIR}"

if [[ $1 == "silent" ]];then
    exit 0
fi
# 打包成功打开文件夹
open "${SRCROOT}/Products"
```

##### 注意点：

- 先 `clean` 再 `build`
- 拷贝 `cp -r` 这步不能漏
- `lipo` 创建通用库


---

2020.12.30
> 问题描述：打包出来的 Framework 集成到工程以后，build 报 `xxx.h file note found` 错误！
> 先后检查了 `Header Search Paths` 和 `User Header Search Paths` 都没有什么问题。
> 最后在 `Framework Search Paths` 中增加了 `xx/Pods/**` 然后编译通过了。



<font color=red>2021.08.06</font>

### 一个lib包含了很多的架构，会打到最后的包里么？

不会，如果lib中有`armv7, armv7s, arm64, i386`架构，而 `target architecture` 选择了

`armv7s, arm64`，

那么只会从lib中`link`指定的这两个架构的二进制代码，其他架构下的代码不会`link`到最终可执行文件中；反过来，一个lib需要在模拟器环境中正常link，也得包含i386架构的指令。



<font color=red>2021.08.25</font>

### 在framework生成的脚本后添加如下脚本用于校验：最低运行iOS版本、bitcode、framework是否支持所有架构(armv7,arm64,i386,x86_64)

```bash
#!/bin/sh

library_name="$1"
bitcode_count=`otool -l $library_name | grep __LLVM | wc -l | xargs`
version_string=`otool -l $library_name | grep version | head -n 1 | xargs`
archs_string=`lipo -info $library_name | sed "s/armv7f//g" | sed "s/armv7s//g" | sed "s/armv7s//g" | sed "s/armv7k//g" | sed "s/armv7m//g" | sed "s/armv7em//g" | sed "s/arm64v8//g" | sed "s/x86_64h//g"`
if [[ "$version_string" != "version 8.0" ]] && [[ "$version_string" != "version 7."* ]] && [[ "$version_string" != "version 6."* ]]; then
echo "最小系统版本号不对 $version_string"
exit -1
fi
if [ "$bitcode_count" == "0" ]; then
echo "bitcode OFF" # 当前不对bitcode做出要求
fi
if [[ "$archs_string" != *"armv7"* ]] || [[ "$archs_string" != *"arm64"* ]] || [[ "$archs_string" != *"i386"* ]] || [[ "$archs_string" != *"x86_64"* ]]; then
echo "缺少部分平台 $archs_string"
exit -1
fi
```

