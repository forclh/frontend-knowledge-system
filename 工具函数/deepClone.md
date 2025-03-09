```js
/**
 * 深度克隆一个对象或数组
 *
 * @param {object|array} obj - 需要克隆的对象或数组
 * @returns {object|array} - 克隆后的对象或数组
 */
function deepClone(obj) {
  // 如果obj为null或不是对象，则直接返回obj
  if (obj === null || typeof obj !== "object") return obj;

  // 初始化克隆对象，如果是数组则为新数组，否则为新对象
  let clone = Array.isArray(obj) ? [] : {};

  // 遍历obj的所有属性
  for (let key in obj) {
    // 确保只克隆对象自身的属性，而不是原型链上的属性
    if (obj.hasOwnProperty(key)) {
      // 递归调用deepClone，深度克隆每个属性值
      clone[key] = deepClone(obj[key]);
    }
  }

  // 返回深度克隆后的对象或数组
  return clone;
}

```