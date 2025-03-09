#JS笔面试

[详细文章](https://codeburst.io/the-simple-rules-to-this-in-javascript-35d97f31bde3)
1. 在调用函数时使用`new`关键字，函数内的`this`是一个全新的对象。
2. 如果`apply`、`call`或`bind`方法用于调用、创建一个函数，函数内的 this 就是作为参数传入这些方法的对象。
3. 当函数作为对象里的方法被调用时，函数内的`this`是调用该函数的对象。比如当`obj.method()`被调用时，函数内的 this 将绑定到`obj`对象。
4. 如果调用函数不符合上述规则，那么`this`的值指向全局对象（global object）。浏览器环境下`this`的值指向`window`对象，但是在严格模式下(`'use strict'`)，`this`的值为`undefined`。
5. 如果符合上述多个规则，则较高的规则（1 号最高，4 号最低）将决定`this`的值。
6. 如果该函数是 ES2015 中的箭头函数，将忽略上面的所有规则，`this`被设置为它被创建时的上下文。