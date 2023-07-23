#  Ajax学习笔记

## 链接

[axios.js文件地址]: https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js



## URL 查询参数

查询参数的语法 ？

* 在 url 网址后面用?拼接格式：http://xxxx.com/xxx/xxx?参数名1=值1&参数名2=值2

* 参数名一般是后端规定的，值前端看情况传递即可

* axios 如何携带查询参数?

  * 使用 params 选项即可

    ```js
    axios({
      url: '目标资源地址',
      params: {
        参数名: 值
      }
    }).then(result => {
      // 对服务器返回的数据做后续处理
    })
    ```


  eg:

  ```
  axios({
        url: 'http://hmajax.itheima.net/api/city',
        // 查询参数
        params: {
          pname: '辽宁省'
        }
      }).then(result => {
        console.log(result.data.list)
        document.querySelector('p').innerHTML = result.data.list.join('<br>')
      })
  ```

  

## 常用请求方法

| 请求方法 | 操作             |
| -------- | ---------------- |
| GET      | 获取数据         |
| POST     | 提交数据         |
| PUT      | 修改数据（全部） |
| DELETE   | 删除数据         |
| PATCH    | 修改数据（部分） |

- method：请求的方法，get可以省略（不区分大小写）
- data：提交数据
- url：请求的url网址

```
axiox({
	url:'',
	method:'post',
	data:{
		 
	}
})
```



## 请求报文

- 请求行：请求方法，URL，协议
- 请求头：以键值对的格式携带的附加信息，比如：Content-Type
- 空行：分隔请求头，空行之后是发送给服务器的资源
- 请求体：发送的资源



## 响应报文

- 响应行：协议，http响应状态码，状态信息
- 响应头：以键值对的格式携带的附加信息，比如：Content-Type
- 空行：分隔响应头，空行之后的是服务器返回的资源
- 响应体：返回的结果



### 响应状态码

| 状态码 | 说明       |
| ------ | ---------- |
| 1xx    | 信息       |
| 2xx    | 成功       |
| 3xx    | 重定向消息 |
| 4xx    | 客户端错误 |
| 5xx    | 服务端错误 |



## 参数

**Body 参数 (application/json)：**data包裹的参数，是Body 参数 (application/json)

**查询参数：**  params包裹的参数，是query参数，参数最后会拼接到url字符串后面，变成？参数名=参数&参数2=参数





## XMLHttpRequest

- 创建XMLhttpRequest对象
- 配置请求方法和请求的地址
- 监听loadend事件，接受响应结果
- 发送请求

```
const xhr=new XMLhttpRequest()
xhr.open('请求方法','请求url地址')
xhr.addEventListener('loadend',()=>{
	console.log(xhr.response)
})
xhr.send()
```



**如若请求带查询参数**

基于URLSearchParams转换查询参数字符串

```
params={
	city:'',
	province:''
}
const paramsObj=new URLSearchParams(params)
const queryString=paramsObj.toString()
```

**如若请求带application/json类型参数，可以设置请求头**

```
const xhr=new XMLhttpRequest()
xhr.open('请求方法','请求url地址')
xhr.addEventListener('loadend',()=>{
	console.log(xhr.response)
})
设置请求头-告诉服务器内容类型（JSON字符串）
xhr.setRequestHeader('Context-Type','application/json')
const userObj={
	username:'',
	password:''
}
const userStr=JSON.stringify(userObj)
//设置请求体，发起请求
xhr.send(userStr)
```



## promise

使用promise管理异步任务

- 创建promise对象

  - ```
    const p=new promise((resolve,reject)=>{
    	setTimeout(()=>{
    		resolve('nihaoya')
    	},1000)
    })
    p.then(result=>{
    	console.log(result)  //nihaoya
    })
    ```

  - resolve中的值会传递出去被then接受



**promise对象有三种状态**

- 待定：pending
- 已兑现：fulfilled
- 已拒绝：rejected

new promise()之后，会立即执行其中的回调函数，异步任务会后移，执行异步任务后状态会变



### promise链式调用

```
let p=new Promise((resolve,reject)=>{
    setTimeout(()=>{
      resolve(1)
    },1000)
})
p.then(result=>{
    console.log(result)
    return 2
}).then(result=>{
  console.log(result)
})
```



## 用XMLhttpRequest和promise简易封装axios

支持查询参数和请求体数据

```
function myAxios(config){
	return new Promise((resolve,reject)=>{
		const xhr=new XMLhttpRequest()
		if(config.params){
			const urlObj=new URLSearchParams(config.params)
			const urlstr=urlObj.toString()
			config.url+=`?${urlstr}`
		}
		xhr.open(config.method||'get',config.url)
		xhr.addEventListener('loadend',()=>{
			if (xhr.status>=200&&xhr.status<300){
				resolve(JSON.parse(xhr.response))
			}else{
				reject(new Error(xhr.response))
			}
		})
		if(config.data){
			xhr.setRequestHeader('Content-Type','application/json')
			jsonStr=JSON.stringify(config.data)
			xhr.send(jsonStr)
		}else{
			xhr.send()
		}
	
	})
}
```



## 同步代码

逐步执行，原地等待结果结束后，才继续向下执行

## 异步代码

调用后耗时，不阻塞代码执行，将来完成以后触发回调函数

- setTimeout/setInterval
- 事件
- ajax



## async await

**用来简化promise链式调用**

在async函数中，使用await关键字取代then函数，等待获取promise对象成功状态的结果值

将异步代码同步执行



## 事件循环

同步任务进栈出栈，异步任务先进入宿主环境（浏览器），再进入任务队列，在调用栈空闲的时候，执行任务队列中的任务

<img src="C:\Users\a\AppData\Roaming\Typora\typora-user-images\image-20230706155327282.png" alt="image-20230706155327282" style="zoom:50%;" />

任务队列分为宏任务和微任务

* 宏任务：由浏览器环境执行的异步代码
* 微任务：由 JS 引擎环境执行的异步代码

宏任务和微任务具体划分：

![image-20230706160227460](C:\Users\a\AppData\Roaming\Typora\typora-user-images\image-20230706160227460.png)

先执行微任务，再执行宏任务
