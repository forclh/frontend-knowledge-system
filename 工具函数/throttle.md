```js
/**
 * 创建一个节流函数，该函数会在指定的时间间隔内最多执行一次。
 * @param {Function} fn - 需要节流处理的函数。
 * @param {number} limit - 时间间隔，单位为毫秒。
 * @returns {Function} - 返回一个新的节流函数。
 */
function throttle(fn, limit) {
  let lastFunc; // 存储定时器ID
  let lastRan; // 存储上一次执行的时间戳

  return function () {
    const context = this; // 保存当前上下文
    const args = arguments; // 保存传入的参数

    if (!lastRan) {
      fn.apply(context, args); // 立即执行函数
      lastRan = Date.now(); // 更新上一次执行的时间戳
    } else {
      clearTimeout(lastFunc); // 清除之前的定时器
      lastFunc = setTimeout(function () {
        if (Date.now() - lastRan >= limit) {
          fn.apply(context, args); // 执行函数
          lastRan = Date.now(); // 更新上一次执行的时间戳
        }
      }, limit - (Date.now() - lastRan)); // 设置新的定时器
    }
  };
}

```