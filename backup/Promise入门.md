# Promise入门
Promise是ES6规范中的一门新技术，它是一个构造函数，用来封装一个异步操作并且可以获取其成功/失败的结果值
### 创建Promise实例对象
```javascript
const p=new Promise((resolve,reject)=>{
     resolve("Success"); // 异步操作成功时调用
     reject("Error"); // 失败时调用
})
```

- Pending未决定的
- Resolved	/ Fullfiled 成功
- Rejected 失败

Resolve调用后可以将promise对象的状态设置为成功
Reject 调用后可以将promise对象的状态设置为失败

- Pending=>resolved/ fullfiled  
- Pending=>rejected

只有这两种情况，且一个promise对象只能改变一次
Resolve和reject函数可以传参数，之后在then的两个回调函数里面可以接收到
### 指定回调函数
调用then方法
then有两个参数并且都是函数，第一个是promise对象成功的回调，第二个是失败回调
```javascript
p.then((value)=>{
        console.log(value)
},(reason)=>{
        console.log(reason)
})
```
### API

**Promise(excutor){}**
Excutor函数:执行器(resolve,reject)=>{}
resolve函数：内部定义成功时就调用 value=>{}
reject函数：内部定义失败时就调用 reason=>{}
说明：excutor会在Promise内部立即同步调用，异步操作会在执行器中执行

**Promise.prototype.then**
实例对象.then(成功回调，失败回调) 
说明：返回一个新的promise对象
返回的promisei结果由then()指定的回调函数执行的结果决定

**Promise.prototype.catch**
失败回调函数
p.catch(reason=>{...}) 

**promise.resolve**
```javascript
let p1=promise.resolve(233)
Let p2=promise.resolve(new Promise((Resolve,Reject)=>{
Resolve(‘OK’)
}))
//如果传入的参数为非promise类型对象，则返回的结果为成功promise对象
//如果传入的参数为promise对象，则参数的结果决定了resolve的结果
```

**promise.reject**
```javascript
let p1=promise.reject(233);
//返回的结果永远是失败的，即使参数是一个promise实例对象并且成功
```

**promise.all**
参数是包括了n个promise的数组
说明：返回一个新的promise，只有数组中所有的promise都成功才成功，只要有一个失败就直接失败
```javascript
let p1=new promise((resolve,reject)=>{resolve(“OK”)})
let p2=promise.resolve(“Success”)
let p3=promise=resolve(“yes”)
const result=promise.all([p1,p2,p3])
```

**promise.race**
参数是包括了n个promise的数组
说明：返回一个新的promise，第一个完成的promise的结果就是最终的结果状态
```javascript
const result=promise.race([p1,p2,p3])
//由p1决定结果
```

**promise状态**
除了resolve和reject可以修改promise状态
还可以用throw（抛出）
```javascript
throw 'error';
```

**多个回调函数**
一个promise指定多个失败/成功回调，当promise改为对应状态时都会调用

**执行顺序**
正常情况是先指定回调再改变状态，但也可以先改变状态再指定回调
先改变状态再指定回调：

- 在执行中直接调用resolve或者reject
- 延迟更长时间才调用then

先指定回调再改变状态：

- 当实例对象中是一个异步任务的时候

**串联多个任务**
链式调用：
then返回一个新的promise，可以开成then的链式调用
通过then的链式调用串联多个同步/异步任务
```javascript
 let p =new Promise((resolve,reject)=>{
        setTimeout(()=>{	
            resolve("OKK")
        },1000)
       })
p.then(value=>{
    return new Promise((resolve,reject)=>{
        resolve("success")
    })
}).then(value=>{
    console.log(value) // success
}).then(value=>{
    console.log(value) // undefined
})
```

**异常穿透**
当使用promise的then链式调用时候，可以在最后指定失败的回调
前面任何操作出了异常，都会传到最后失败的回调中处理
```javascript
 let p =new Promise((resolve,reject)=>{
        setTimeout(()=>{	
            resolve("OKK")
        },1000)
       })
p.then(value=>{
    return new Promise((resolve,reject)=>{
        resolve("success")
    })
}).then(value=>{
    console.log(value) // success
}).then(value=>{
    console.log(value) // undefined
}).catch((reason)=>{ 
console.log(reason);
})
```
在最后加.catch(reason=>{...})，使用then也行

**中断promise链**
当使用promise链式调用时，在中间中断，不在调用后面的回调函数
方法：在回调函数中返回一个pendding状态的promise
```javascript
return new promise(()=>{})
```

**Async**
函数的返回值为promise对象
Promise对象的结果由async函数执行的返回值决定

- 如果返回值是一个非promise类型的数据，结果就是成功的
- 如果返回值是一个promise对象，resolve就成功，reject就失败
- 如果是抛出异常(throw)，结果是一个失败的promise对象

```javascript
async function main(){
     return new Promise((resolve,reject)=>{
          resolve()
})
}
Let result =main()
//它和then方法返回规则是一样的
```

**await**
await右侧的表达式一般为promise对象，但也可以是其他值

- 如果表达式是promise对象，await返回的是promise成功的值
- 如果表达式是其他的值，直接将此值作为await的返回值
注意：
await必须写在async函数中，但async函数中可以没有await
如果await的promise失败了，就会抛出异常，需要同过try...catch捕获处理
```javascript
 try {
            let result = await new Promise((resolve,reject)=>{
                reject()
             })              
} catch (error) {
       reject(error)
}
```

**async和await结合实践**
```javascript
try{
     let data1=await _promise对象_
     let data2=await _promise对象_
     let data3=await _promise对象_
}catch(error){
     console.log(error)
}
```

**事件循环机制**
async函数在抛出返回值时，会根据返回值类型开启不同数目的微任务

- return结果值：非thenable、非promise（不等待）
- return结果值：thenable（等待 1个then的时间）
- return结果值：promise（等待 2个then的时间）

await右值类型区别

- 接非 thenable 类型，会立即向微任务队列添加一个微任务then，但不需等待
- 
- 接 thenable 类型，需要等待一个 then 的时间之后执行
- 
- 接Promise类型(有确定的返回值)，会立即向微任务队列添加一个微任务then，但不需等待
- 
- TC 39 对await 后面是 promise 的情况如何处理进行了一次修改，移除了额外的两个微任务，在早期版本，依然会等待两个 then 的时间
