箭头函数修复了`this`的指向，`this`总是指向词法作用域
```JavaScript
let obj = {
    birth: 1990,
    getAge: function () {
        let b = this.birth; // 1990
        let fn = () => new Date().getFullYear() - this.birth; // this指向obj对象
        return fn();
    }
};
obj.getAge(); // 25
```
