在`obj.method()`这种调用形式下，方法中的this指向obj；`func()`这种调用形式中，在非strict模式下this指向`window`，strict模式下this指向`undefined`。


apply 
func.apply(obj, []) 等价于 obj.func([])
call
func.call(obj, arg1, arg2..) 等价于 obj.func(arg1, arg2..)