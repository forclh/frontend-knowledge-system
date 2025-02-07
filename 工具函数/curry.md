
```js
function curry() {
    var fn = arguments[0]; // 获取要执行的函数
    var args = [].slice.call(arguments, 1); // 获取传递的参数，构成一个参数数组
    // 如果传递的参数已经等于执行函数所需的参数数量
    if (args.length === fn.length) {
        return fn.apply(this, args)
    }
    // 参数不够向外界返回的函数
    function _curry(){
        // 推入之前判断
        // 将新接收到的参数推入到参数数组中
        args.push(...arguments);
        if(args.length === fn.length){
            return fn.apply(this, args)
        }
        return _curry;
    }
    return _curry;
}
```
