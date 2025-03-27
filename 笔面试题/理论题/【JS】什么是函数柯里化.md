

柯里化（*currying*）又称部分求值。一个柯里化的函数首先会接受一些参数，接受了这些参数之后，该函数并不会立即求值，而是继续返回另外一个函数，刚才传入的参数在函数形成的闭包中被保存起来。待到函数被真正需要求值的时候，之前传入的所有参数都会被一次性用于求值。

举个例子，就是把原本：

`function(arg1,arg2)` 变成 `function(arg1)(arg2)`

`function(arg1,arg2,arg3)` 变成 `function(arg1)(arg2)(arg3)`

`function(arg1,arg2,arg3,arg4)` 变成 `function(arg1)(arg2)(arg3)(arg4)`

总而言之，就是将：

`function(arg1,arg2,…,argn)` 变成 `function(arg1)(arg2)…(argn)`