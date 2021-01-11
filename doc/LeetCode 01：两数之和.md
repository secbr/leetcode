在LeetCode的第一题下面，有这样一句评论“有人相爱，有人夜里开车看海，有人leetcode第一题都做不出来。”看到这条评论，你是得意的笑呢，还是苦涩的笑？

LeetCode第一题为“两数之和”，难度为“简单”，如果这样一个简单的题，没做不出来，的确有些心酸。这就像学一门编程语言时，打印“Hello World”的程序都没写不出来的感觉是一样的，凄凉。

下面就来一起看看这道题。

### “两数之和”

题名称为“两数之和”，题目详情如下，对应官方链接：https://leetcode-cn.com/problems/two-sum/

![两数之和](http://www.choupangxia.com/wp-content/uploads/2021/01/twosum-1.png)

### 题面分析

题目用大白话来说就是：有一个整数数组，从中找出两个数，这两个数满足它们的和等于指定的数。

![两数之和](http://www.choupangxia.com/wp-content/uploads/2021/01/twosum-2.png)

然后就是一些限制性条件，比如：“只会有一个答案”，那么一旦找到一组数据，直接返回就OK了。

其中“数组中同一元素不能使用两遍”这个限制条件有一定的歧义，迷惑了很多人，我在第一次做题的时候就很困惑：循环两次算是“使用两遍”吗？

结合官方给出的双层for循环方法，仔细分析之后，才明白这里的“使用两遍”并不是读取或遍历两次，而是指计算和时不能使用两次。比如，数组内容为[2，3，4]，指定目标值为6，那么返回的结果应该是[0,2]，也就是2+4=6，而不是3 + 3 = 6。这才是题意中的使用两遍的本意。

下面看具体的解答方案。

### 方案一：暴力枚举

我们先从最简单，能直观看到的解题方案：双层循环，也就是暴力枚举。

基本思路就是：遍历数组中的每一行数据x，然后拿目标数值target减去x（即target - x），然后再次遍历数组寻找值为target - x的数据项。

![两数之和](http://www.choupangxia.com/wp-content/uploads/2021/01/twosum-3.png)

在计算过程中，很显然当循环执行到9-2=7之后，7已经存在于数组当中，不需要再继续计算匹配了。此循环过程，还可以进一步优化：第一层循环之后，x之前的元素已经匹配过了，因此不需要再次匹配，只需匹配x之后的元素即可。

实现代码如下：


```
// 暴力遍历
public int[] twoSum(int[] nums, int target) {
    int len = nums.length;
    for (int i = 0; i < len - 1; i++) {
        for (int j = i + 1; j < len; j++) {
            if (nums[i] + nums[j] == target) {
                return new int[]{i, j};
            }
        }
    }
    return new int[0];
}
```

注意上面第二层for循环中j取值为i+1，就是上面所说的x之前的元素不再匹配，直接匹配后面的元素。

计算上述代码的时间复杂度，直接看最内层（第二层循环）的时间频度，随着第一层循环的执行，第二层的时间频度依次为n-1，n-2，n-3，n-4…… 2，1，是一个等差数列，因此整体的时间复杂度计算公式为 n(n-1 + 1)/2 = n^2/2。得到时间复杂度为： O(n^2)。

由于在该算法中定义的内部变量只有len，它是固定值，不会随着nums数组的变化而变化，也就是常数，因此空间复杂度为1，记作 O(1)。

### 方案二：哈希表

说到哈希表，它是在算法中经常会用到的一种数据存储结构，可以根据key轻易的获得对应的value值。甚至可以说，每当遇到一个新算法时，都要优先考虑一下能否通过哈希表的形式来解决。

使用哈希表进行存储会引起空间复杂度的变化。哈希表存储的数据可能会随着问题规模n的增加而增加，因此，空间复杂度很可能会由原来的O(1)变为O(n)，这种变换也就是通常所说的拿空间换时间。

暴力枚举算法中，我们看到导致算法复杂度较高的原因是查找target-x的时间复杂度较高，因此，可采用上面提到的哈希表来实现快速查找元素的目的。将原来查找的时间复杂度会从O(n)降低为近似O(1)。为什么说是近似O(1)呢？因为一旦出现哈希冲突，查找用时可能会退化到 O(n)。但只要仔细地挑选哈希函数，在哈希表中进行查找的用时应当被摊销为 O(1)。

在本题中，就可以拿空间换时间，先创建一个哈希表，对于每一个 x，首先查询哈希表中是否存在target - x，如果不存在则将 x 插入到哈希表中，如果存在则返回key对应的坐标和当前元素的坐标。


![两数之和](http://www.choupangxia.com/wp-content/uploads/2021/01/twosum-4.png)

整体来看，该算法就是：一个外层for循环，一个内层哈希表的快速查找。示例代码如下：

```
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<Integer, Integer>();
        for (int i = 0; i < nums.length; ++i) {
            if (map.containsKey(target - nums[i])) {
                return new int[]{map.get(target - nums[i]), i};
            }
            map.put(nums[i], i);
        }
        return new int[0];
    }
}
```
我们知道，哈希表查询的时间复杂度为O(1)，外层循环时间复杂度为O(n)，因此该算法的时间复杂度为 O(n) 。关于空间复杂度，随着问题规模n的变化，哈希表所占的空间也会随着变化，因此，空间复杂度 O(n)。


### 方法三 排序 + 双指针

除了上述两种算法之外，还有一种解决方案的思路：先将nums元素排序，再使用两个指针，从前后两个值开始逐步逼近target。具体实现代码如下：

```
public int[] twoSum2(int[] nums, int target) {

    // 转化成 List, 以方便排序
    List<Integer> numList = new ArrayList<>(nums.length);
    // 保留原始 坐标位置
    Map<String, Integer> location = new HashMap<>(nums.length);
    for (int i = 0; i < nums.length; i++) {
        numList.add(nums[i]);
        if (location.containsKey(nums[i] + "")) {
            location.put(nums[i] + "AGAIN", i);
        } else {
            location.put(nums[i] + "", i);
        }
    }

    Collections.sort(numList);
    int preIdx = 0;
    int sufIdx = numList.size() - 1;
    int[] result = new int[2];
    int tmpPre,tmpSuf;
    while (preIdx < sufIdx) {
        tmpPre = numList.get(preIdx);
        tmpSuf = numList.get(sufIdx);
        int tmpRes =  tmpPre + tmpSuf;
        // 两数之和 大于 target，后面的坐标前移
        if (tmpRes > target) {
            sufIdx -= 1;
        } else if (tmpRes < target) {
            // 两数之和 小于 target , 前面的坐标后移
            preIdx += 1;
        } else {
            if (location.containsKey(tmpPre + "AGAIN")) {
                result[0] = location.get(tmpPre + "AGAIN");
                location.remove(tmpPre + "AGAIN");
            } else {
                result[0] = location.get(tmpPre + "");
            }
            if (location.containsKey(tmpSuf + "AGAIN")) {
                result[1] = location.get(tmpSuf + "AGAIN");
                location.remove(tmpSuf + "AGAIN");
            } else {
                result[1] = location.get(tmpSuf + "");
            }
            if (tmpPre == tmpSuf) {
                // 前后值一致时，交换两者的顺序
                int tmpCpm = result[0];
                result[0] = result[1];
                result[1] = tmpCpm;
            }
            return result;
        }
    }
}
```
上述算法时间复杂度 O(nlogn) , 第一个for循环的时间复杂度为O(n)， 排序的时间复杂度最优为 O(nlogn), while循环的时间复杂度为 O(n)， 取最大范围的则为O(nlogn)。

空间复杂度 O(n) , 原始坐标location， 排序对象numList 都可以视为 O(n)， 则相加为O(2n) ，忽略常系数则为 O(n)。

关于此种算法并没有第二种方法有优势，而且操作流程相对繁琐一些，用来参考，拓展思路即可。

### 小结

你可能很久之前已经做过“两数之和”这道题，但阅读完本篇文章之后，是否产生了新的思考？希望这文章能够让你从另外一个层面来学习“两数之和”这道题，而不是仅仅刷了一道算法题。

关注本人公众号”程序新视界“，获得更多相关知识。

![程序新视界](https://www.choupangxia.com/wp-content/uploads/2019/07/weixin.jpg)