## 算法题目

### 1 单调栈

> #### 栈
>
> 特征：`LIFO`(Last In, First Out - 后进先出)
>
> 操作：
>
> 1. 进栈 -` push` - 将元素放置到栈顶
> 2. 退栈 - `pop` - 将栈顶元素弹出
> 3. 栈顶 - `top` - 得到栈顶元素的值
> 4. 是否空栈 - `isEmpty` - 判断栈内是否有元素
> 5. 时间复杂度：
>    1. 进栈 O(1)
>    2. 出栈 O(1)
>
> #### 单调栈
>
> 在栈的基础上加了约束：栈中的元素是单调递增或者单调递减的。如果出栈的元素是单调增的，那就是单调递增栈，如果出栈的元素是单调减的，那就是单调递减栈。
>
> eg. 【1,2,3,4】（左侧为栈底）出栈 4,3,2,1 是一个单调递减栈
>
> **如果压栈之后仍然可以保持单调性，那么直接压。否则先弹出栈的元素，直到压入之后可以保持单调性。 被弹出的元素都是大于当前元素的，并且由于栈是单调减的，因此在其之后小于其本身的最近的就是当前元素了**

鉴于上述，如果遇到题目需要找**在其之后第一个小于其本身的位置**的题目，就可是考虑使用单调栈。

```swift
class Solution {
    func monostoneStack(arr:[Int]) -> [Int] {
        var stack: [Int] = []
        /// 表示在其之后第一个小于其本身的位置，results[i] 表示 arr[i] 之后第一个小于 arr[i]的位置
        /// results[i] 为 -1 表示这样的位置不存在
        var results: [Int] = Array(repeating: -1, count: arr.count)
        
        for i in 0..<arr.count {
            print(i)
            while (stack.count>0) && (arr[i]>arr[stack.last!]) {
                let peek = stack.removeLast()
                results[peek] = i - peek
            }
            stack.append(i)
        }
        
        return results
    }
}
/*
时间复杂度：由于 arr 的元素最多只会入栈，出栈一次，因此时间复杂度仍然是 O(N)，其中 N 为数组长度。
空间复杂度：由于使用了栈，并且栈的长度最大是和 arr 长度一致，因此空间复杂度是 O(N)，其中 N 为数组长度。
*/
```

> 题目：给定 n 个非负整数表示每个宽度为1的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。
>
> <img src="../../assets/image-20220107152146504.png" alt="image-20220107152146504" style="zoom:80%;" />
>
> 上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）。示例：
>
> 输入: [0,1,0,2,1,0,1,3,2,1,2,1]
>
> 输出: 6
>
> 思路：如图所示要求收集的水，用下雨后的体积 V2 - 下雨前的体积 V1 即可。柱子单位是 1 所以：
>
> ```swift
> // V = V2 - V1
> for i in 0..<heightArr1.count {
>     V += (heightArr2[i] - heightArr1[i]) * 1
> }
> ```
>
> 已知 heightArr1 ==> [0,1,0,2,1,0,1,3,2,1,2,1]
>
> 据上图可得： heightArr2 ==> [0,1,1,2,2,2,2,3,2,2,2,1]
>
> 上述，伪代码中的 heightArr2[i] 该如何计算？？？
>
> 问题的关键就是求**该位置左右两边柱子较小者**
>
> - Left[i+1] < Right[i] ，那么存水的高度由 i 的左侧最大值决定
> - Left[i+1] >= Right[i] ，那么存水的高度有 i 的右侧最大值决定

```swift
/**
 * left/right，分别指向头尾。当指针的位置的雨水量已经计算完成，我们移动到下个位置用同样的方法计算
 * 
 */

class Solution {
    func calPonding(heightArr:[Int]) -> Int {
        let n = heightArr.count
        var result = 0
        var leftMax = 0, rightMax = 0
        var left = 0, right = n - 1
        
        while left < right {
            // 短板在 heightArr[left]，否则短板在 heightArr[right]
            if heightArr[left] < heightArr[right] {
                // 则当前柱子积水面积为(leftMax - heightArr[left])，否则无法积水
                if heightArr[left] < leftMax {
                   result += leftMax - heightArr[left] 
                } else {
                    leftMax = heightArr[left]
                }
                left += 1
            } else {
                // 则当前柱子积水面积为(rightMax - heightArr[right])，否则无法积水
                if heightArr[right] < rightMax {
                    result += rightMax - heightArr[right]
                } else {
                    rightMax = heightArr[right]
                }
                right -= 1
            }
        }
        
        return result
    }
}
// 时间复杂度：O(N)
// 空间复杂度：O(1)
```
