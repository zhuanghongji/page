# Dart 算法：两数之和

> 难度：简单  
> 来源：力扣（LeetCode）  
> 链接：https://leetcode-cn.com/problems/two-sum  


## 题目描述

给定一个整数数组 `nums` 和一个目标值 `target`，请你在该数组中找出和为目标值的那 **两个** 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

示例：

```
给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```


## 题解

### 方法一：暴力法

暴力法很简单，直接通过下标遍历 `nums` 数组中的每元素 `x` 并查找出另一个值为 `target - x` 的目标元素的下标，得到两个下标后通过数组返回即可。

代码如下：

```dart
List<int> solution1(List<int> nums, int target) {
  for (var i = 0; i < nums.length; i++) {
    for (var j = i + 1; j < nums.length; j++) {
      if (nums[j] == target - nums[i]) {
        return [i, j];
      }
    }
  }
  return [];
}
```

复杂度分析：
* 时间复杂度 `O(n2)`：对于每个元素，我们试图遍历数组，这部分耗费 `O(n)` 的时间；然后又通过遍历数组的其余部分来寻找对应的目标元素，这部分也耗费 `O(n)` 的时间。所以总的来说，复杂度为 `O(n2)`。
* 空间复杂度 `O(1)`。


### 方法二：两遍哈希表

时间复杂度为 `O(n2)` 表明上述 "暴力法" 是不可取的，为了对时间复杂度优化，我们需要找到一种更有效的方法来检查 "数组中是否存在目标元素"。如果存在，找到它的索引即可。

我们经常会听到 "以空间换时间" 这个概念，通过该概念我们就可以将查找时间从 `O(n)` 降到 `O(1)`。那这个 "空间" 怎么搞呢？哈希表。哈希表支持以近似恒定的时间进行快速查找，只要我们仔细的挑选哈希函数，那在哈希表中进行查找的用时会被摊销为 `O(1)`。

> **知识扩展：**
> * `Hash` : 一般译为 "散列" (或直接音译为 "哈希")，是把任意长度的输入通过散列算法变换为固定长度的输出，该输出称为散列值。
> * `哈希函数` ：若结构中存在和关键字 `K` 相等的记录，则该记录必定在 `f(K)` 的存储位置上，不需比较便可直接取得该记录，这个对应关系 `f` 称为 Hash 函数 (散列函数)。
> * `哈希表` ：是可根据键值 (Key value) 直接进行访问的数据结构，它通过把键值映射到表中一个位置来访问记录，以加快查找的速度。这个映射关系就是我们所说的散列函数，存放记录的数组叫做散列表。

在本方法中，我们需要做两次迭代：
* 第一次迭代：将每个元素的值和它对应的索引添加到哈希表中。
* 第二次迭代：检查每个元素对应的目标元素 ( `target - nums[i]` ) 是否在该哈希表中。注意，本题目要求目标元素不能是 `nums[i]` 本身。

代码如下：

```dart
List<int> solution2(List<int> nums, int target) {
  var map = HashMap();
  for (var i = 0; i < nums.length; i++) {
    map[nums[i]] = i;
  }
  for (var i = 0; i < nums.length; i++) {
    var complement = target - nums[i];
    if (map.containsKey(complement) && map[complement] != i) {
      return [i, map[complement]];
    }
  }
  return [];
}
```

复杂度分析：
* 时间复杂度 `O(n)`：我们把包含有 n 个元素的列表遍历了两遍，得益于哈希表的应用使得查找目标元素的时间缩短至 `O(1)`，所以总的时间复杂度为 `O(n)`。
* 控件复杂度 `O(n)`：所需的额外空间取决于哈希表中存储的元素数量，该表中存储了 n 个元素。


### 方法三：一遍哈希表

如果你仔细看方法二中的代码会发现，我们一次遍历也是能满足需求的：
* 在进行迭代并将元素插入到哈希表中的同时，顺带检查表中是否已经存在当前元素所对应的目标元素。
* 如果它存在，那说明我们已经找到了对应解，返回即可。

代码如下：

```dart
List<int> solution3(List<int> nums, int target) {
  var map = HashMap();
  for (var i = 0; i < nums.length; i++) {
    var complement = target - nums[i];
    if (map.containsKey(complement)) {
      return [map[complement], i];
    }
    map[nums[i]] = i;
  }
  return [];
}
```

复杂度分析：
* 时间复杂度 `O(n)`：我们只遍历了一次含 n 个元素的列表，在哈希表中进行的每次查找只花费 `O(1)` 的事件。
* 空间复杂度 `O(n)`：所需的额外空间取决于哈希表中存储的元素数量，该表最多需要存储 n 个元素。


### 小结

让我们写一个 `main` 函数来验证上面的 solution 代码是否都符合预期：

```dart
import 'dart:collection';

void main() {
  List<int> numbers = [2, 7, 11, 15];
  int target = 9;

  // expect: [0, 1]
  print(solution1(numbers, target));
  print(solution2(numbers, target));
  print(solution3(numbers, target));
}
```

日志输出：

```
[0, 1]
[0, 1]
[0, 1]
```


## 总结


**`map.containsKey(complement)` 这个复杂度一定为 `O(1)` 吗？**