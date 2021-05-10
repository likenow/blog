## 每日一题 Follow

题目来源于 [只有熟练掌握基础的数据结构与算法，才能对复杂问题迎刃有余。](https://github.com/azl397985856/leetcode)

> 计划，通过每日一题来加强对数据结构和算法的掌握。

### 题目

1.本次题目来自牛客网分享的一次华为面试题：

```wiki
终端产品在进行一项噪音监测实验。若将空实验室平面图视作一个 nm 的二维矩阵（左上角为 [0,0]）。
工作人员在实验室内设置了若干噪音源，并以 [噪音源所在行，噪音源所在列，噪音值] 的形式记录于二维数组 noise 中。
噪音沿相邻八个方向传播，在传播过程中，噪音值（单位为分贝）逐级递减 1 分贝，直至分贝削弱至 1
（即噪音源覆盖区域边缘噪音分贝为 1）；
 若同一格被多个噪音源的噪音覆盖，监测结果不叠加，仅保留较大的噪音值
 （噪音源所在格也可能被其他噪音源的噪声传播所覆盖）。
 在所有噪音源开启且持续传播情况稳定后，请监测每格噪音分贝数并返回他们的总和。
注意：
    除噪音源以外的所有格初始值为 0 分贝； 不考虑墙面反射。示例 1：
    输入：n = 5, m = 6, noise = [[3,4,3],[1,1,4]]输出：63

作者：我会个锤子的算法
链接：https://www.nowcoder.com/discuss/643910?source_id=discuss_experience_nctrack&channel=-1
来源：牛客网
```



分析：

```wiki
例子是5行6列，计数是从 0 开始，输入两个噪音点`[3,4,3]`和`[1,1,4]`

1. 向8个方向传播（向四周扩散）
2. 传播过程中，噪音值逐级递减 1 至 1 分贝
3. 同一格被多个音源的噪音覆盖，不叠加保留较大者


    0  |  1  |  2  |  3  |   4   |   5  |

0 | 3     3     3     2      1       0

1 | 3     4     3     2      1       1

2 | 3     3     3     2      2       2

3 | 2     2     2     2      3       2

4 | 1     1     1     2      2       2

=> sum: 63
```



---

代码：

```swift
struct NoisePoint {
    var x = 0
    var y = 0
    var value = 0
    init(x: Int, y: Int, value: Int) {
        self.x = x
        self.y = y
        self.value = value
    }
}

enum SpreadError: Error {
    case inValidInput                 // 无效输入
}

class Solution {
    func sumOfArray(array: [Int]) -> Int {
        var result = 0
        for num in array {
            result += num
        }
        return result
    }
    /// 对噪音点在平面空间传播
    func spreadNoise(matrix: inout [[Int]], row: Int, column: Int, point: NoisePoint) throws {
        // 噪音点填充到矩阵
        matrix[point.x][point.y] = point.value
        // 用于存放噪音点
        var points = [NoisePoint]()
        // 将首个点存入
        points.append(point)
        
        while points.count > 0 {
            let pt = points.popLast()!
            
            // 同一格被多个音源的噪音覆盖，不叠加保留较大者
            if matrix[pt.x][pt.y] < pt.value {
                matrix[pt.x][pt.y] = pt.value
            }
            /* 以噪音点为中心向周围传播
               1. 传播过程中，噪音值逐级递减 1 至 1 分贝（传播过程中会有新的噪音点）
             */
            if pt.x > 0, pt.y > 0, pt.x < row, pt.y < column, pt.value > 1 {
                points.append(NoisePoint(x: pt.x-1, y: pt.y, value: pt.value-1))
                points.append(NoisePoint(x: pt.x+1, y: pt.y, value: pt.value-1))
                points.append(NoisePoint(x: pt.x, y: pt.y+1, value: pt.value-1))
                points.append(NoisePoint(x: pt.x, y: pt.y-1, value: pt.value-1))
                points.append(NoisePoint(x: pt.x-1, y: pt.y-1, value: pt.value-1))
                points.append(NoisePoint(x: pt.x-1, y: pt.y+1, value: pt.value-1))
                points.append(NoisePoint(x: pt.x+1, y: pt.y+1, value: pt.value-1))
                points.append(NoisePoint(x: pt.x+1, y: pt.y-1, value: pt.value-1))
            }
        }
    }
    /// 测试
    func test(_ x: Int, _ y: Int, noisePoints: [NoisePoint]) throws -> Int {
        guard noisePoints.count > 0, x > 0, y > 0 else {
            throw SpreadError.inValidInput
        }
        // x行y列的矩阵（最终的结果矩阵）
        var matrix = [[Int]](repeating: [Int](repeating: 0, count: y), count: x)
        
        for point in noisePoints {
            try spreadNoise(matrix: &matrix, row: x-1, column: y-1, point: point)
        }

        var result = 0
        for a in matrix {
            result += sumOfArray(array: a)
        }
        return result
    }

}

let inputs: [NoisePoint] = [NoisePoint(x: 3, y: 4, value: 3), NoisePoint(x: 1, y: 1, value: 4)]
let result = try Solution().test(5, 6, noisePoints: inputs)
print(result)
```



---

---

2.题目描述：交换比特（bit）。对一个整数每两位交换一次，并返回交换后的数。

```swift
class Solution {
    func swap_bit(x: Int) -> Int {
        // Mask A - a, in binary, is 1010
        let maskA = 0xAA
        // Mask 5 -  5, in binary, is 0101
        let mask5 = 0x55;
        let r1 = (x & maskA) >> 1
        let r2 = (x & mask5) << 1
        let result = r1 | r2
        return result
    }

    func testSwapBit(x: Int) {
        print(swap_bit(x: x))
    }
}

Solution().testSwapBit(x: 7)
```



上述，涉及到一个知识点叫：**位屏蔽**。

Stack Overflow 上的一个问题：[What are 0xaa and 0x55 doing?](https://stackoverflow.com/questions/43923906/what-are-0xaa-and-0x55-doing)

```wiki
     First, we'll look at (x & 0xaaaaaaaa). If you break 0xaaaaaaaa down to the bit level, you end up with 1010 1010 1010 1010 1010 1010 1010 1010 (as a, in binary, is 1010). So (x & 0xaaaaaaaa) is saying, return only every even-placed 1 in x. This is called bit masking. Then, you right shift it by one place - this is how you make the even numbers switch place (so now the second bit occupies the place of the first bit, and the fourth the third, etc).

     You do the same thing with (x & 0x55555555) - if you break it down to the bit level, you end up with 0101 0101 0101 0101 0101 0101 0101 0101 (as 5, in binary, is 0101). This masks all the even-placed bits in x, and gives you all the odd-placed bits. Then, you shift all bits left by 1. Finally, you use the or (|) operator to combine the two bit-sequences, and that's your answer.

     Example: Let's take 2456086205. We convert that into binary and get 1001 0010 0110 0100 1110 0110 1011 1101. Now, we do (x & 0xaaaaaaaa), and get

     1001 0010 0110 0100 1110 0110 1011 1101 & 1010 1010 1010 1010 1010 1010 1010 1010,

     which equals 1000 0010 0010 0000 1010 0010 1010 1000. Shift this to the right and you get 0100 0001 0001 0000 0101 0001 0101 0100.

     Now, do (x & 0x55555555), and get

     1001 0010 0110 0100 1110 0110 1011 1101 & 0101 0101 0101 0101 0101 0101 0101 0101,

     which equals 0001 0000 0100 0100 0100 0100 0001 0101. Shift this to the left and you get 0010 0000 1000 1000 1000 1000 0010 1010.

     Finally, we do 0100 0001 0001 0000 0101 0001 0101 0100 | 0010 0000 1000 1000 1000 1000 0010 1010. We then get 0110 0001 1001 1000 1101 1001 0111 1110, which, as you can see, is the the solution!
```



---

---
