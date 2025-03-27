
```js
function deepClone(obj) {
    if (obj === null || typeof obj !== "object") return obj;
    let cloned = Array.isArray(obj) ? [] : {};
    for (let key in obj) {
        if (obj.hasOwnProperty(key)) {
            cloned[key] = deepClone(obj[key]);
        }
    }
    return cloned;
}
```
