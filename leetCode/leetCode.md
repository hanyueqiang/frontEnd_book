## 数组

### 两数之和(1)

给定一个整数数组 nums 和一个目标数组 target,请在该数组中找出和为目标值的两个整数，并返回他们的数组下标;
可以假设每种输入只对应一个答案，但是，数组中同一个元素不能使用两遍
示例:

```js
nums = [2, 7, 11, 15], target = 9,
因为nums[0] + nums[1] = 9
返回[0, 1];
```

答案解析：
使用对象字面量存储变量进行查找

```js
// 时间复杂度O(n^2) 不推荐
function sumsTwo(arr, target) {
  const len = arr.length;
  let i = 0;
  while (i < len) {
    let current = target - arr[i];
    for (let j = i + 1; j < len; j++) {
      if (arr[j] === current) {
        return [i, j];
      }
    }
    i++;
  }
  return [];
}

/**
 * 时间复杂度O(n)
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
var twoSum = function(nums, target) {
  const map = {};
  const length = nums.length;
  let i = 0;
  while (i < length) {
    const current = target - nums[i];
    if (map[current] > -1) {
      return [map[current], i];
    }
    map[nums[i]] = i;
    i++;
  }
  return [];
};
```

### 寻找两个正序数组的中位数（4）

给定两个大小为 m 和 n 的正序（从小到大）数组  nums1 和  nums2。

请你找出这两个正序数组的中位数，并且要求算法的时间复杂度为  O(log(m + n))。

你可以假设  nums1  和  nums2  不会同时为空。

```js
nums1 = [1, 3]
nums2 = [2]
则中位数是 2.0

nums1 = [1, 2]
nums2 = [3, 4]
则中位数是 (2 + 3)/2 = 2.5
```

解析：

```js
```

## 字符串

### 无重复字符的最长子串(3)

给定一个字符串，找出其中不含有重复字符的最长子串长度

```js
输入: "abcabcbb";
输出: 3;

摄入: "bbbbb";
输出: 1;
因为无重复字符的最长子串是 "b"，所以其长度为 1

输入: "pwwkew";
输出: 3;
最长子串是 "wke"，所以其长度为 3。
```

答案解析：
利用数组移动滑块解决此题

```js
/**
 * @param {string} s
 * @return {number}
 */
var lengthOfLongestSubstring = function(s) {
  const length = s.length;
  const arr = [];
  let max = 0;
  for (let i = 0; i < length; i++) {
    const index = arr.indexOf(s[i]);
    if (index > -1) {
      arr.splice(0, index + 1);
    }
    arr.push(s[i]);
    max = Math.max(max, arr.length);
  }
  return max;
};
```

### 一个矩阵数组中寻找 x 值

其中每个数组内有序排列，数组与数组间上下有序排列，寻找是否存在 x 值，返回 true/false

```js
const arr = [[1, 4, 8], [9, 11, 13], [15, 18, 21]];

// 例如 x= 9 ，输出 true
function getX(arr, x) {
  if (arr.length === 1) {
    return arr[0].includes(x);
  }
  const len = arr.length;
  const index = Math.floor(len / 2);
  const left = arr.slice(0, index);
  const right = arr.slice(index);
  const leftLast = left[left.length - 1];
  if (leftLast[leftLast.length - 1] < x) {
    return getX(right, x);
  } else if (leftLast[leftLast.length - 1] > x) {
    return getX(left, x);
  } else if (leftLast[leftLast.length - 1] === x) {
    return true;
  }
}

console.log(getX(arr, 19)); // true
```

## 算法，链表反转

## 洗牌算法

## 一个无限长有序可重复数组 N，查 X 最后出现的位置

## 随机生成给定长度的字符串

#### 字符创连续最多字符
