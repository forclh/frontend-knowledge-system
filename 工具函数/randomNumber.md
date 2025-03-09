```js
/**
 * 生成一个在指定范围内的随机整数。
 * @param {number} min - 随机数的最小值（包含）。
 * @param {number} max - 随机数的最大值（包含）。
 * @returns {number} - 返回一个在 [min, max] 范围内的随机整数。
 */
function randomNumber(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}

```