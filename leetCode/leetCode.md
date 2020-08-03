## 前端 LeetCode 刷题

#### 两数之和

给定一个整数数组 nums 和一个目标数组 target,请在该数组中找出和为目标值的两个整数，并返回他们的数组下标;
可以假设每种输入只对应一个答案，但是，数组中同一个元素不能使用两遍
示例:

```js
nums = [2, 7, 11, 15], target = 9,
因为nums[0] + nums[1] = 9
返回[0, 1];
```

答案解析：
使用 while 循环

```js
// 时间复杂度O(n^2)
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

// 时间复杂度O(n)
function sumsTwo1(arr, target) {
  const maps = {};
  const len = arr.length;
  let i = 0;
  while (i < len) {
    let current = target - arr[i];
    if (maps[current] > -1) {
      return [maps[current], i];
    }
    maps[arr[i]] = i;
    i++;
  }
  return [];
}
```

#### 无重复字符的最长子串

给定一个字符串，找出其中不含有重复字符的最长子串长度

```js
输入: "abcabcbb";
输出: 3;
摄入: "bbbbb";
输出: 1;
输入: "pwwkew";
输出: 3;
```
