```js
/**
 * 创建一个防抖函数，该函数会在最后一次调用后延迟指定的时间执行。
 * @param {Function} fn - 需要防抖处理的函数。
 * @param {number} delay - 延迟时间，单位为毫秒。
 * @returns {Function} - 返回一个新的防抖函数。
 */
function debounce(fn, delay) {
  let timer; // 定时器ID
  return function (...args) {
    clearTimeout(timer); // 清除之前的定时器
    timer = setTimeout(() => fn.apply(this, args), delay); // 设置新的定时器
  };
}

```