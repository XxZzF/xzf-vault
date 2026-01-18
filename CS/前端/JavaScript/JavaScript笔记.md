在`obj.method()`这种调用形式下，方法中的this指向obj；`func()`这种调用形式中，在非strict模式下this指向`window`，strict模式下this指向`undefined`。


apply 
func.apply(obj, []) 等价于 obj.func([])
call
func.call(obj, arg1, arg2..) 等价于 obj.func(arg1, arg2..)


箭头函数修复了`this`的指向，`this`总是指向词法作用域
```JavaScript
let obj = {
    birth: 1990,
    getAge: function () {
        let b = this.birth; // 1990
        let fn = () => new Date().getFullYear() - this.birth; // this指向obj对象
        //let fn = function() {
	    //    new Date().getFullYear() - this.birth; // this指向window对象
	    //}
        return fn();
    }
};
obj.getAge(); // 25
```


```JavaScript
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    const random = Math.random();
    if (random > 0.5) {
      resolve(`成功！随机数：${random}`);
    } else {
      reject(new Error(`失败！随机数：${random}`));
    }
  }, 1000);
});

// 处理结果
promise
  .then((result) => {
    console.log('成功回调：', result); // 状态为 fulfilled 时执行
    return '继续传递的数据'; // 可以返回新值，供下一个 then 使用
  })
  .then((data) => {
    console.log('第二个 then：', data); // 接收上一个 then 返回的值
  })
  .catch((error) => {
    console.error('失败回调：', error.message); // 状态为 rejected 时执行
  })
  .finally(() => {
    console.log('无论成功失败，都会执行'); // 最终执行（如关闭加载动画）
  });

```