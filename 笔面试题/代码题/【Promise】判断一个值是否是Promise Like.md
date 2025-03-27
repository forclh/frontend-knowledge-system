只要符合[Promise A+](https://promisesaplus.com/)规范的值即为Promise Like

```js
/**
 * 判断一个值是否是PromiseLike
 */
function isPromiseLike(value) {
  return (
    value !== null &&
    (typeof value === "object" || typeof value === "function") &&
    typeof value.then === "function"
  );
}

```

为什么需要判断 `value !== null` ?

在判断 `value !== null` 的主要原因与 JavaScript 的 `typeof` 运算符特性有关：

1. **`typeof null` 的历史遗留问题**  
   JavaScript 中 `typeof null` 会返回 `"object"`（历史遗留行为），若不加 `value !== null` 的判断，当传入 `null` 时：
```javascript
typeof null === "object" → true
value.then → 触发 TypeError（试图访问 null 的属性）
```

2. **防御性代码的必要性**  
   该检查可过滤掉 `null` 这种特殊值，避免后续访问 `value.then` 时出现 `Cannot read property 'then' of null` 的运行时错误。

3. **符合 Promise A+ 规范**  
   Promise Like 对象的核心特征是拥有 `.then()` 方法，而 `null` 显然不可能具备这一特性，提前排除可提升判断准确性。

**总结**：`value !== null` 是必要的类型安全防护，避免因 JavaScript 类型系统缺陷导致的误判和运行时错误。