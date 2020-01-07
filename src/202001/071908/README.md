# Dart 算法：两数相加

> 难度：中等  
> 来源：力扣（LeetCode）  
> 链接：https://leetcode-cn.com/problems/add-two-numbers/ 


## 题目描述

给出两个 **非空** 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 **逆序** 的方式存储的，并且它们的每个节点只能存储 **一位** 数字。

如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。

您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

示例：

```
输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807
```


## 题解：初等数学

### 思路

我们使用变量来跟踪进位，并从包含最低有效位的表头开始模拟逐位相加的过程。

<img src="./res/001.svg" width="480"/>

* *对两数相加方法的可视化：`342 + 465 = 807` 。*
* *每个节点都包含一个数字，并且数字按位逆序存储。*

### 算法

就像你在纸上计算两个数字的和那样，我们首先从最低有效位（列表 `l1` 和 `l2` 的表头）开始相加。由于每位数字都应当处于 `0 .. 9` 的范围内，所以在我们计算两个数字的和时可能会出现 "溢出"，比如 `5 + 7 = 12` 。在这种情况下，我们会将当前位的数值设置为 `2`，并将进位 `carry = 1` 带入下一次迭代。

进位 `carry` 必定是 `0` 或 `1`，这是因为两个数字相加可能出现的最大和为 `9 + 9 + 1 = 19`（考虑到进位）。

### 特别情况

| list1 | list2 | 说明
| - | - | -
| [0, 1] | [0, 1, 2] | 当一个列表比另一个列表长时
| [] | [0, 1] | 当一个列表为空时（即出现空列表）
| [9, 9] | [1] | 求和运算最后可能出现额外的进位（这一点很容易被遗忘）

### 示例

### 自定义 "链表节点" 实现类

> Dart 的链表使用比较特殊，我们需要先自定义一个 `LinkedListEntry` 类。

```dart
class Entry<T> extends LinkedListEntry<Entry<T>> {
  final T value;

  Entry(this.value);

  // 定义一个工具函数，方便我们快捷生成链表
  static LinkedList<Entry<T>> toList<T>(Iterable<T> values) {
    var list = LinkedList<Entry<T>>();
    if (values == null) {
      return list;
    }
    for (var value in values) {
      list.add(Entry(value));
    }
    return list;
  }

  @override
  String toString() {
    return 'Entry{value: $value}';
  }
}
```

### 算法核心代码

```dart
LinkedList<Entry<int>> addTwoNumbers(
    LinkedList<Entry<int>> list1, 
    LinkedList<Entry<int>> list2,
) {
  var head = Entry.toList([0]).first;
  var p = list1.length > 0 ? list1.first : null;    // 1
  var q = list2.length > 0 ? list2.first : null;
  var current = head;                               // 2
  int carry = 0;                                    // 3
  while (p != null || q != null) {                  // 4
    int x = (p != null) ? p.value : 0;
    int y = (q != null) ? q.value : 0;
    int sum = carry + x + y;
    carry = sum ~/ 10;
    current.insertAfter(Entry(sum % 10));
    current = current.next;
    if (p != null) p = p.next;
    if (q != null) q = q.next;
  }
  if (carry > 0) {                                  // 5
    current.insertAfter(Entry(carry));
  }
  var result = head.list;                           
  result.remove(head);                              // 6
  return result;
}
```

1. 将 `p` 和 `q` 分别初始化为列表 `list1` 和 `list2` 的头部，注意 `first` 不存在的情况。
2. 将当前节点初始化为返回列表的哑节点 (dummy-node)。
3. 将进位 `carry` 初始化为 `0`。
4. 遍历列表 `list1` 和 `list2` 直至到达它们的尾端。
   * 将 `x` 设为节点 `p` 的值，如果 `p` 已经到达 `list1` 的末尾，则将其值设置为 `0`。 
   * 将 `y` 设为节点 `q` 的值，如果 `q` 已经到达 `list2` 的末尾，则将其值设置为 `0`。 
   * 设定 `sum = x + y + carry`，更新进位的值 `carry = sum ~/ 10`。
   * 创建一个数值为 `sum % 10` 的新节点，并将其设置为当前节点的下一个节点，然后将当前节点前进到下一个节点。
   * 同时，将 `p` 和 `q` 前进到下一个节点。
5. 检查 `carry = 1` 是否成立，如果成立，则向返回列表追加一个含有数字 `1` 的新节点。
6. 删除哑节点，然后返回新的链表。

> 提示：
> * 在 Dart 中，`/` 表示除法，比如 `15 / 10 = 1.5`。
> * 而 `~/` 表示取整，比如 `15 ~/ 10 = 1`。
> 
> 注意：
> * 这里我们使用了哑节点来简化代码。
> * 如果没有哑节点，则必须编写额外的条件语句来初始化表头的值。


### 验证代码

```dart
/*

测试用例:

  (2 → 4 → 3) + ( 5 → 6 → 4) = 7 → 0 → 8
  (5 → 6 → 2) + ( 6 → 7 → 3) = 1 → 4 → 6
  (5 → 6    ) + ( 6 → 7 → 3) = 1 → 4 → 4
  (5 → 6    ) + ( 6 → 7    ) = 1 → 4 → 1
  (         ) + ( 6 → 7    ) = 6 → 7

*/

void main() {
  print(addTwoNumbers(Entry.toList([2, 4, 3]), Entry.toList([5, 6, 4])));
  print(addTwoNumbers(Entry.toList([5, 6, 2]), Entry.toList([6, 7, 3])));
  print(addTwoNumbers(Entry.toList([5, 6   ]), Entry.toList([6, 7, 3])));
  print(addTwoNumbers(Entry.toList([5, 6   ]), Entry.toList([6, 7   ])));
  print(addTwoNumbers(Entry.toList([       ]), Entry.toList([6, 7   ])));   
}
```

日志输出：

```dart
(Entry{value: 7}, Entry{value: 0}, Entry{value: 8})
(Entry{value: 1}, Entry{value: 4}, Entry{value: 6})
(Entry{value: 1}, Entry{value: 4}, Entry{value: 4})
(Entry{value: 1}, Entry{value: 4}, Entry{value: 1})
(Entry{value: 6}, Entry{value: 7})
```


## 复杂度分析

* 时间复杂度：`O(max(m, n))`，假设 m 和 n 分别表示 `list1` 和 `list2` 的长度，上面的算法最多重复 `max(m, n)` 次。
* 空间复杂度：`O(max(m,n))`，新列表的长度最多为 `max(m, n) + 1` 。


## 拓展

如果链表中的数字不是按逆序存储的呢？例如：

```
(3 → 4 → 2) + ( 4 → 6 → 5) = 8 → 0 → 7
```