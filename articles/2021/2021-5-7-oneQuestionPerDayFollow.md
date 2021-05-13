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

3.[尼姆游戏](https://zh.wikipedia.org/wiki/%E5%B0%BC%E5%A7%86%E6%B8%B8%E6%88%8F)

> 尼姆游戏，是一种两人玩儿的回合制数学战略游戏。游戏者轮流从几排棋子（或者任何道具）中选择一排，再由这一排中取走一个或者多个，依规则不同，拿走最后一个的可能是输家，也有可能是赢家。当指定相应数量时，一堆这样的棋子称作一个**尼姆堆**。古代就有许多尼姆游戏的变体。最早欧洲有关尼姆游戏的参考资料是在16世纪，目前使用的名称是由[哈佛大学](https://zh.wikipedia.org/wiki/哈佛大学)的Charles L. Bouton命名，他也在1901年提出了此游戏的完整理论，不过没有说明名称的由来。

题目描述：有两堆球，一堆8个 一堆10个， 每人每次只能从某一堆上至少拿走一个上不封顶，拿走最后一个的输，两个人轮流拿 ，你先拿怎么赢？ 

```wiki
读完题干，如果你仔细看了上述维基百科，我们可以吧两堆球，理解为两排。

胜利的策略就是在取走球后，使尼姆和为0。

题目描述的是 misère 版（拿到最后一个棋子的输），而且是一个特别简单的情况，只有两排。
必胜的策略：在个数较多的那牌拿走部分球，使两者数量相同。接下来对手不论怎么下，都继续使另一排的数量相同，最后即可胜利。
```





```swift
enum GameType: String {
    case NORMAL = "Normal"
    case MISERE = "Misere"
}

/**
 在normal版本（拿到最后一个棋子的赢）中，胜利的策略就是在取走棋子后，使尼姆和为0。只要取走棋子前，尼姆和不为0，一定有办法取走部分棋子使尼姆和为0。另一个游戏者无论怎么拿，取走棋子后尼姆和都不会为0。以此策略，只要在取棋子时照策略进行，一定会胜利。要找到要拿走的棋子，可以令X是原来各排棋子数的尼姆和，游戏策略是要分别计算各排棋子数和X的尼姆和，找到尼姆和比该排棋子数少的那一排，接下来就要取走这一排的棋子，使该排棋子数等于尼姆和。以上例中，原来各排棋子数的尼姆和是X = 3 ⊕ 4 ⊕ 5 = 2。A=3、B=4、C=5且X=2，因此得到

 A ⊕ X = 3 ⊕ 2 = 1 [因为 (011) ⊕ (010) = 001 ]
 B ⊕ X = 4 ⊕ 2 = 6
 C ⊕ X = 5 ⊕ 2 = 7
 因此下一步是取走A排的棋子，使其数量变1（拿走二个棋子）。

 有一个特别简单的例子，是只剩二排的情形，其策略是在个数较多的那牌拿走部分棋子，使两者数量相同。接下来对手不论怎么下，都继续使二排的数量相同，最后即可胜利。

 若是玩misère版本。前面的策略都一样，只到只剩一排的棋子超过一个（二个或二个以上）时才有不同。此时的策略都是针对超过一个棋子的那排棋子取子，使留下来的每一排都只有一个棋子。接下来玩的人只能从这几排中选一排拿走。取子可能是那排全部取完，或是只剩一个，视游戏版本而定，在玩misère版本（拿到最后一个棋子的输）时，要使留下来的排数是单数（因此对方会拿到最后一个棋子），在玩normal版本游戏时，要使留下来的排数是偶数。（因此自己会拿到最后一个棋子）。
 */
class Solution {
    /// 支持两种游戏模式：normal and misere。计算输入排，下一步该移除哪一排的多少数量
    func nim(heaps: [Int], type: String) -> (Int, Int) {
        print("game type \(type)", heaps)
        // 判断当前 nim 游戏类型
        let isMisere = (type == GameType.MISERE.rawValue)
        // 游戏是否进入到后半程
        let isNearEnd = self.isNearEndGame(heaps: heaps)
        // 如果游戏进入后半程，misere 与 normal 不同
        if isMisere, isNearEnd {
            // 计算剩余非 0 的排
            let left = self.movesLeft(heaps: heaps)
            // 排数是否为单数
            let isOdd = (left % 2) == 1
            
            // 针对超过一个棋子的那排棋子取子，使留下来的每一排都只有一个棋子 ps.要使留下来的排数是单数
            let max = self.maxOfHeap(heaps: heaps)
            let maxValue = max.1
            let maxIndex = max.0
            if maxValue == 1, isOdd {
                print("U will lose, No matter how you try :(")
                return (0,0)
            }
            return (maxIndex, maxValue)
        }
        // 尼姆和
        let nimSum = xorHeap(heaps: heaps)
        // 胜利的策略就是在取走棋子后，使尼姆和为0。所以，另一个游戏者无论怎么拿，取走棋子后尼姆和都不会为0。
        if nimSum == 0 {
            print("U will lose, No matter how you try :(")
            return (0,0)
        }
        // 找到要拿走的棋子
        var willRemoveSize = 1
        for (i, v) in heaps.enumerated() {
            // 要分别计算各排棋子数和nimSum 的尼姆和
            willRemoveSize = v ^ nimSum
            // 找到尼姆和比该排棋子数少的那一排，接下来就要取走这一排的棋子，使该排棋子数等于尼姆和。
            if willRemoveSize < v {
                let amountToRemove = v - willRemoveSize
              	// ps.这里找到一种赢的方式就返回了，此处可以计数，算出（游戏前半程）赢的方式数目
                return (i, amountToRemove)
            }
        }
        return (0,0)
    }
    /// Misere 只到只剩一排的棋子超过一个（二个或二个以上）时才有不同
    func isNearEndGame(heaps: [Int]) -> Bool {
        var result = 0
        for v in heaps {
            if v > 1 {
                result += 1
            }
        }
        
        return result <= 1
    }
    /// 剩余非 0 的排
    func movesLeft(heaps: [Int]) -> Int {
        var result = 0
        for v in heaps {
            if v > 0 {
                result += 1
            }
        }
        return result
    }
    /// 找到最大值及所在的排
    func maxOfHeap(heaps: [Int]) -> (Int, Int) {
        var result = 0
        var index = 0
        for (i,v) in heaps.enumerated() {
            if v > result {
                result = v
                index = i
            }
        }
        
        return (index,result)
    }
    /// heaps中的元素异或运算
    func xorHeap(heaps: [Int]) -> Int {
        var result = heaps[0]
        for i in 1..<heaps.count {
            result ^= heaps[i]
        }
        print("nimSum =",result)
        
        return result
    }
}

print("next step", Solution().nim(heaps: [3,4,5], type: GameType.MISERE.rawValue))
//        print("next step", Solution().nim(heaps: [1,4,5], type: GameType.MISERE.rawValue))
        print("next step", Solution().nim(heaps: [1,4,3], type: GameType.MISERE.rawValue))
//        print("next step", Solution().nim(heaps: [1,2,3], type: GameType.MISERE.rawValue))
        print("next step", Solution().nim(heaps: [1,2,2], type: GameType.MISERE.rawValue))
//        print("next step", Solution().nim(heaps: [0,2,2], type: GameType.MISERE.rawValue))
        print("next step", Solution().nim(heaps: [0,2,1], type: GameType.MISERE.rawValue))
//        print("next step", Solution().nim(heaps: [0,0,1], type: GameType.MISERE.rawValue))

/**
 * 打印
 game type Misere [3, 4, 5]
 nimSum = 2
 next step (0, 2)

 game type Misere [1, 4, 3]
 nimSum = 6
 next step (1, 2)

 game type Misere [1, 2, 2]
 nimSum = 1
 next step (0, 1)

 game type Misere [0, 2, 1]
 next step (1, 2)

*/
```



```python
import functools

MISERE = 'misere'
NORMAL = 'normal'

def nim(heaps, game_type):
    """Computes next move for Nim, for both game types normal and misere.

    if there is a winning move:
        return tuple(heap_index, amount_to_remove)
    else:
        return "You will lose :("

    - mid-game scenarios are the same for both game types

    >>> print(nim([1, 2, 3], MISERE))
    misere [1, 2, 3] You will lose :(
    >>> print(nim([1, 2, 3], NORMAL))
    normal [1, 2, 3] You will lose :(
    >>> print(nim([1, 2, 4], MISERE))
    misere [1, 2, 4] (2, 1)
    >>> print(nim([1, 2, 4], NORMAL))
    normal [1, 2, 4] (2, 1)

    - endgame scenarios change depending upon game type

    >>> print(nim([1], MISERE))
    misere [1] You will lose :(
    >>> print(nim([1], NORMAL))
    normal [1] (0, 1)
    >>> print(nim([1, 1], MISERE))
    misere [1, 1] (0, 1)
    >>> print(nim([1, 1], NORMAL))
    normal [1, 1] You will lose :(
    >>> print(nim([1, 5], MISERE))
    misere [1, 5] (1, 5)
    >>> print(nim([1, 5], NORMAL))
    normal [1, 5] (1, 4)
    """

    print(game_type, heaps)

    is_misere = game_type == MISERE

    is_near_endgame = False
    count_non_0_1 = sum(1 for x in heaps if x > 1)
    is_near_endgame = (count_non_0_1 <= 1)

    # nim sum will give the correct end-game move for normal play but
    # misere requires the last move be forced onto the opponent
    if is_misere and is_near_endgame:
        # 列表推导式 list comprehension，是在一组字符串或者一组对象上执行一条相同操作的简洁写法！
        # eg. heaps = [0,2,1] => sum([1,1]) => 2
        # eg. sum(x * x for x in range(10))  => sum([1*1, 2*2, 3*3, ...])
        moves_left = sum(1 for x in heaps if x > 0)
        is_odd = (moves_left % 2 == 1)
        sizeof_max = max(heaps)
        index_of_max = heaps.index(sizeof_max)

        if sizeof_max == 1 and is_odd:
            return "You will lose :("

        # reduce the game to an odd number of 1's
        return index_of_max, sizeof_max - int(is_odd)

    nim_sum = functools.reduce(lambda x, y: x ^ y, heaps)
    if nim_sum == 0:
        return "You will lose :("

    # Calc which move to make
    for index, heap in enumerate(heaps):
        target_size = heap ^ nim_sum
        if target_size < heap:
            amount_to_remove = heap - target_size
            return index, amount_to_remove



# print(nim([3,4,5,6], MISERE))
# print(nim([3,0,5,6], MISERE))
# print(nim([1,2,2], MISERE))
# print(nim([0,2,1], MISERE))

if __name__ == "__main__":
    import doctest
    doctest.testmod()
```



题外话：

> ```python
> sum(...)
>     sum(iterable[, start]) -> value
> 
>     Return the sum of an iterable or sequence of numbers (NOT strings)
>     plus the value of 'start' (which defaults to 0).  When the sequence is
>     empty, return start.
> ```
>
> 
>
> 比如:[[1],[2,3],[4,5,6]]，将其转成[1, 2, 3, 4, 5, 6].
>
> 这个时候使用Python自带的sum函数会很简单。