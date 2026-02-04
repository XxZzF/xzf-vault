函数参数的默认值必须是不可变对象


**函数参数定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数。**
```python
def f1(a, b, c=0, *args, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'args =', args, 'kw =', kw)

def f2(a, b, c=0, *, d, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'd =', d, 'kw =', kw)
```
最神奇的是通过一个tuple和dict，你也可以调用上述函数：
```python
>>> args = (1, 2, 3, 4)
>>> kw = {'d': 99, 'x': '#'}
>>> f1(*args, **kw)
a = 1 b = 2 c = 3 args = (4,) kw = {'d': 99, 'x': '#'}
>>> args = (1, 2, 3)
>>> kw = {'d': 88, 'x': '#'}
>>> f2(*args, **kw)
a = 1 b = 2 c = 3 d = 88 kw = {'x': '#'}
```
所以，对于任意函数，都可以通过类似`func(*args, **kw)`的形式调用它，无论它的参数是如何定义的。


从后向前遍历字符串：`for i in range(len(str) - 1, -1, -1)`


如果要对`list`实现类似Java那样的下标循环怎么办？Python内置的`enumerate`函数可以把一个`list`变成索引-元素对，这样就可以在`for`循环中同时迭代索引和元素本身：
```python
>>> for i, value in enumerate(['A', 'B', 'C']):
...     print(i, value)
...
0 A
1 B
2 C
```


python循环可以跟else语句，else语句在循环没有被break中断时执行


直接作用于`for`循环的对象统称为可迭代对象：`Iterable`
可以被`next()`函数调用并不断返回下一个值的对象称为迭代器：`Iterator`
`Iterator`对象表示的是一个数据流，我们不能提前知道数据流的长度
```python
>>> isinstance((x for x in range(10)), Iterator)
True
>>> isinstance([], Iterator)
False
>>> isinstance({}, Iterator)
False
>>> isinstance('abc', Iterator)
False
>>> isinstance(range(0, 10), Iterable)
True
>>> isinstance(range(0, 10), Iterator)
False
```
`list`、`dict`、`str`虽然是`Iterable`，却不是`Iterator`


在一个列表生成式中，`for`前面的`if ... else`是表达式，而`for`后面的`if`是过滤条件，不能带`else`