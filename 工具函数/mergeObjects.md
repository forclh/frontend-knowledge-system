```js
/**
 * 合并多个对象 into 一个新对象
 * 该函数接受多个对象参数，将它们的属性合并到一个新的对象中
 * 使用了ES6的扩展运算符和Object.assign方法来实现对象的合并
 *
 * @param {...Object} objects - 需要被合并的对象们
 * @returns {Object} - 合并了所有输入对象属性的新对象
 */
function mergeObjects(...objects) {
  return Object.assign({}, ...objects);
}

```