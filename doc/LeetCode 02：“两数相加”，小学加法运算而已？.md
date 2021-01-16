# LeetCode 02：“两数相加”，小学加法运算而已?

一道小学加法题，竟然在LeetCode上被标记为“中等”难度，有些人“流下了没有技术的眼泪”，有些人“一顿操作猛如虎，一看击败百分五……”。今天我们来看看LeetCode的第二道题“两数相加”。


### “两数相加”

先来看题目描述，对应官方链接：https://leetcode-cn.com/problems/add-two-numbers

> 给你两个非空的链表，表示两个非负的整数。它们每位数字都是按照逆序的方式存储的，并且每个节点只能存储一位数字。请你将两个数相加，并以相同形式返回一个表示和的链表。你可以假设除了数字0之外，这两个数都不会以0开头。

![image](http://www.choupangxia.com/wp-content/uploads/2021/01/lc-02-01.jpg)

链表节点的数据结构如下：


```
public class ListNode {
   int val;
   ListNode next;
   ListNode() {}
   ListNode(int val) { this.val = val; }
   ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 }
```
### 题目说明

题目描述相对来说比较绕，我们可以直接理解为两个多位的整数相加，只不过整数的每一位都是通过链表进行存储。比如，整数342，通过链表存储正常来说应该是3->4->2，但是计算时，往往需要从低位开始计算，逢十进一，所以题目中直接将整数表示为2->4->3，这样反而不用将链表顺序进行反转了，直接相加就可以了。

![image](http://www.choupangxia.com/wp-content/uploads/2021/01/lc-02-02.jpg)

需要注意的是如果两个链表的长度不同，则可以认为长度短的链表的后面有若干个 0 ，链表遍历结束，则如果进位值大于0，则还需要在结果链表中附加一个值为1的节点。

### 方法一：模拟

上面已经提到，链表是逆序的，因此直接对应数字相加即可。基本操作遍历两个列表，逐位计算它们的和，并与当前位置的进位值相加。

比如，两个链表对应位的数字分别为n1和n2，进位为carry（通常为0和1），则它们的和为(n1 + n2 + carry)，对应位上数字变为(n1 + n2 + carry)%10，新的进位为(n1 + n2 + carry)/10。

如果两个链表长度不一样，长度短的链表后续对应位上值均为0即可。如果遍历结束之后，carray大于0（也就是等于1），则在结构链表后面新增一个节点，

实现代码如下：

```
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode head = null, tail = null;
        int carry = 0;
        while (l1 != null || l2 != null) {
            int n1 = l1 != null ? l1.val : 0;
            int n2 = l2 != null ? l2.val : 0;
            int sum = n1 + n2 + carry;
            if (head == null) {
                head = tail = new ListNode(sum % 10);
            } else {
                tail.next = new ListNode(sum % 10);
                tail = tail.next;
            }
            carry = sum / 10;
            if (l1 != null) {
                l1 = l1.next;
            }
            if (l2 != null) {
                l2 = l2.next;
            }
        }
        if (carry > 0) {
            tail.next = new ListNode(carry);
        }
        return head;
    }
}
```
上述方法时间复杂度的计算与链表的长度有关，比如两个链表的长度分别为m和n，则遍历的次数为max(m,n)，也就m和n中取最大值，所以时间复杂度为O(n)。

由于要对链表的每一位进行计算存储，并且最后如果有进位，还要多加一位，因此最长链表为max(m,n)+1，所以空间复杂度为O(n)；

通过思路分析，写出上面的代码还是比较容易的。但这个题目是否可以考虑用递归的形式来解决呢？我们来看看方法二。

### 方法二：递归

第一种方法很简单，按照正常的思维逻辑来就可以了。但评论区有这样一句话“不用递归没有灵魂。尽管多数时候，递归不见得更有效率。”那么我们就来看看用递归的形式如何实现。

```
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        return add(l1,l2,0);
    }
    public ListNode add(ListNode l1, ListNode l2, int carry){
        if(l1 == null && l2 == null && carry == 0) return null;
        int x = l1==null ? 0 : l1.val;
        int y = l2==null ? 0 : l2.val;
        int sum = x + y + carry;
        ListNode n = new ListNode(sum % 10);
        n.next = add(l1==null ? null : l1.next,
                     l2==null ? null : l2.next,
                     sum/10);
        return n; 
    }
}
```
上述代码的基本迭代逻辑循环如下：

![image](http://www.choupangxia.com/wp-content/uploads/2021/01/lc-02-03.jpg)

通过上图我们可以推演一下递归调用的时间复杂度。针对递归调用的时间复杂度计算，本质上要看：递归的次数✖️每次递归中的操作次数。那么，上述方法递归了几次呢？递归的次数也是与两个链表最长的那个的长度有关，最后可能会因为进位多算一次，因此递归次数为n或n+1，而内部计算并不随n的变化而变化，执行次数为常数。因此整体时间复杂度为n*1 = O(n)。

空间复杂度依旧与结果链表的长度有关，因此依旧为O(n)。

### 小结

就算法本身而言是比较简单的，理清思路，逐渐添加判断即可获得解法。重点在于大家是否能够想到通过递归算法来进行解答。本道题递归算法并没有让时间复杂度降低，而在某些情况下通过递归算法能将时间复杂度从O(n)降低到O(logn)，这将是很大性能提升。比如，求x的n次方，大家可以尝试一下。
