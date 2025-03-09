```js
/**
 * 移除数组中的重复元素，并返回一个只包含唯一元素的新数组。
 * 该函数利用Set对象来过滤掉重复元素，因为Set只允许存储唯一值。
 * 使用展开运算符(...)将Set转换回数组。
 *
 * @param {Array} arr - 原始数组，可能包含重复元素。
 * @returns {Array} - 一个只包含原始数组中唯一元素的新数组。
 */
function uniqueArray(arr) {
  return [...new Set(arr)];
}

```