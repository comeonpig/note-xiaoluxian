# ⭐ DAY01-同步和异步

***写在前面：本笔记是小鹿线训练营上课笔记，本人上课的一个记录，如果有不足或者写错的地方，欢迎指正***



#### 一、 同步和异步

JavaScript是一门**单线程语言**，即一次只能完成一个任务，若有多个任务要执行，**必须排队**按照队列来执行。

**弊端：**单线程模式**效率低下**，如果发生浏览器**无响应（假死）**，往往是因为某一段JavaScript代码长时间执行，导致整个页面卡在这个地方，其他任务无法执行。

**同步：**任务顺次执行；

**异步：**每个任务都有***回调函数***，前一个任务结束后，不是执行后一个任务，而是执行回调函数。后一个任务则是不等前一个函数结束就执行。所以执行顺序与任务排列顺序是不一致的，耗时很长的任务大多用以“异步”执行。

##### 1.1 传统方案：

**回调函数：** 异步编程的基本方法。**函数A作为参数传递到函数B中，并且这个函数B执行函数A。**这是一种实现，不是异步模式的特有实现。回调函数同样可以运用到同步（阻塞）的情况中。

```js
function A(callback) {
    callback();
    console.log('我是主函数');
}

function B() {
    setTimeout("console.log('我是回调函数')",3000);
}

A(B);

//输出结果
我是主函数
我是回调函数
```



**普通的callback：**

1. 模拟异步行为：A => B => C
2. 问题：不利于代码的阅读和维护，各部分高度耦合，流程会很混乱。

##### 1.2 工具方案

###### 1.2.1 Promise: 用于处理异步操作的，异步处理成功就执行成功的操作，异步处理失败就捕获错误或者停止后续操作。

一般形式：

```js
new Promise(
    function(resolve,reject){
        if(/*success*/){
            //...执行代码
            resolve();
        } else { /*fail*/
            //...执行代码
            reject();
        }
    }
);

//如果执行成功，调用resolve()置为fulfilled，失败调用reject()置为rejected。
//promise有三个状态：pending,fulfilled,rejected。
//状态转化：pending => fulfilled , pending => rejected.
```

**Promise 对象的方法（api）：**
**1）Promise.prototype.then(callback)**
    Promise对象含有then方法，then()调用后返回一个Promise对象。且then()可以接收两个函数，一个是处理成功的函数，一个是处理错误结果的函数。

**2）Promise.prototype.catch(callback)**

​    Promise对象含有catch方法，catch()调用后返回一个Promise对象，它主要用于捕获异步操作时出现的异常。因此我们经常省略then()方法的第二个参数，把错误控制权转交给其后面的catch()函数。

```js
var promise3 = new Promise(function(resolve,reject){
    setTimeout(function(){
        reject('reject');
    },2000);
})

promise3.then(function(data){
    console.log("这里是fulfilled状态");//不会执行。
    //...
}).catch(function(err){
    console.log(err);
});
```

**3）Promise.all()**

Promise.all()接收一个参数，他必须是可以迭代的，比如数组。

它通常用来处理一些并发的异步操作，即他们的结果互不干扰，但是又需要异步执行。最终只有两种状态，成功或者失败。

它的状态受参数内各个值的状态影响，只有里面的状态全部变为fulfilled之后，他才会变成fulfilled，否则变成rejected。

**4）Promise.race()**

它与Promise.all()类似。不同的是它的状态变化不是全部受参数内的状态影响，一旦参数内有一个值的状态发生改变，那么这个Promise的状态就是改变的状态，就跟race的字面意思一样，谁跑的快谁赢。

**5）Promise.resolve()**

Promise.resolve()接收一个参数值，可以是普通的值，具有then()方法的对象和Promise实例。正常情况下，它返回一个promise对象，状态为fulfilled。但是，当解析发生错误时，返回的Promise对象将会置为rejected状态。

**6）Promise.reject()**

跟Promise.resolve()正好相反，它接收一个参数值reason，即发生异常的原因。此时返回的Promise对象将会被置为rejected状态。

***Promise的哪些东西是异步的：then，all...***

***Promise 的问题：在控制异步代码执行顺序方面，需要封装。（不好维护，不利于阅读）***

***比如用：async/await 来解决这个问题。***

***怪异问题：return Promise.resolve()；会产生两次then（两个微任务）***

###### 1.2.2 generator 和 async/await

1. generator 可以控制异步代码的执行顺序

   问题：写起来太麻烦。

2. **async/await （ES7新增）可以控制异步代码的执行顺序**

   **格式**：async 放置在一个函数之前，这个函数总是**返回一个promise**，如果代码中有return<非promise>语句，JavaScript会自动把返回的这个value值包装成promise的resolved值。

   ```js
   async function fn1(){
       return 1;
   }
   fn().then(alert);//弹出1
   ```

   ​	**关键词 await** 可以让JavaScript进行**等待**，直到一个promise执行并返回它的结果，JavaScript才会继续往下执行。

   **优势：**对比 Promise 和 generator ，更加简洁、方便、代码维护性更高

#### 总结：异步的发展：先有 Promise ，再有 generator，再有async/await

