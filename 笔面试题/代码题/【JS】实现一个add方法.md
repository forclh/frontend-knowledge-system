
题目描述：

实现一个 *add* 方法，使计算结果能够满足如下预期：

```js
add(1)(2)(3) = 6;
add(1, 2, 3)(4) = 10;
add(1)(2)(3)(4)(5) = 15;
```
## 解答

要完成上面的需求，我们就可以使用柯里化函数：

```js
function add() {
    // 第一次执行时，定义一个数组专门用来存储所有的参数
    var _args = Array.prototype.slice.call(arguments);

    // 在内部声明一个函数，利用闭包的特性保存 _args 并收集所有的参数值
    var _adder = function () {
        _args.push(...arguments);
        return _adder;
    };

    // 这个是最后输出的时候被调用的，return 后面如果是函数体，
    // 为了输出函数体字符串会自动调用 toString 方法
    // 利用 toString 隐式转换的特性，当最后执行时隐式转换，并计算最终的值返回
    _adder.toString = function () {
        return _args.reduce(function (a, b) {
            return a + b;
        });
    }

    // 这个 return 是第一次调用的时候返回上面的函数体，
    // 这样后面所有的括号再执行的时候就是执行 _adder 函数体
    return _adder;
}
console.log(add(1)(2)(3).toString()); // 6
console.log(add(1, 2, 3)(4).toString()); // 10
console.log(add(1)(2)(3)(4)(5).toString()); // 15
console.log(add(2, 6)(1).toString()); // 9
```

