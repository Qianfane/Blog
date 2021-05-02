## 剑指 Offer 11. 旋转数组的最小数字（js实现）
 **题目：** 
把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。输入一个递增排序的数组的一个旋转，输出旋转数组的最小元素。例如，数组 `[3,4,5,1,2]` 为 `[1,2,3,4,5]` 的一个旋转，该数组的最小值为1。

 **示例 1：** 

```
输入：[3,4,5,1,2]
输出：1
```

**示例 2：**

```
输入：[2,2,2,0,1]
输出：0
```

##### 编码

```js
/**
 * [3,4,5,1,2]
 * 采用二分查找法
 * 
 * 1. 准备头尾双指针 index1、index2，取最中间的那个数 numbers[indexMid]
 * 2. 如果 numbers[indexMid] >= numbers[index2]，说明 numbers[indexMid] 在第一个有序数组里面，那么最小数在 numbers[indexMid] 后面，那么 index1 要设置为 indexMid
 * 3. 如果 numbers[indexMid] <= numbers[index2]，说明 numbers[indexMid] 在第二个有序数组里面，那么最小数是 numbers[indexMid] 或在其前面，那么 index2 要设置为 indexMid
 * 4. 重复23，直到 index1、index2 相邻为止，此时最小数为 numbers[index2]
 * 
 * 特殊用例
 * 当旋转数组为原来数组时，第一个数字即为最小数字
 * 当 numbers[indexMid] === numbers[index1] === numbers[index2] 时，我们不清楚 numbers[indexMid] 位于第一个有序数组还是第二个有序数组里面，那么就要采用顺序查找
 */
var minArray = function(numbers) {
  var index1 = 0
  var index2 = numbers.length - 1
  var indexMid = Math.floor((index1 + index2) / 2)

  if (numbers.length === 1) return numbers[0]
  // 当旋转数组为原来数组时
  if (numbers[index1] < numbers[index2]) return numbers[index1]
  // 当三指针指向的数字都相等时
  if (numbers[index1] === numbers[index2] && numbers[index1] === numbers[indexMid]) {
    // 顺序查找
    for (var i = 1; i < numbers.length; i++) {
      if (numbers[i] < numbers[i-1]) {
        return numbers[i]
      }
    }
  }

  while (index2 > index1) {
    if (index2 - index1 === 1) {
      return numbers[index2]
    }
    if (numbers[indexMid] > numbers[index2]) {
      index1 = indexMid
      if (index2 - index1 === 1) return numbers[index2]
      indexMid = Math.floor((index1 + index2) / 2)
    } else if (numbers[indexMid] <= numbers[index2]) {
      index2 = indexMid
      if (index2 - index1 === 1) return numbers[index2]
      indexMid = Math.floor((index1 + index2) / 2)
    }
  } 
};
```