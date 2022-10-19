---
title: let与const与var整理
# data:
# updata: 2022-10-18 18:47
categories: 
    - JavaScript
date: 2022-10-17 21:15:39
tags: 
    - es6
toc: true # 是否开启文章章节目录导航
thumbnail: ./themes/pure/source/images/avatar.jpg # 显示「最近文章」缩略图
---
# Promise知识点

## 概念

`Promise` 是一个 `ECMAScript 6` 提供的**类**，目的是更加优雅地书写**复杂**的**异步**任务。

**Safari 9 和 Edge 13 及以下不兼容**

## 使用 

**`语法`**：

```js
// 构造 Promise 对象
new Promise(function (resolve, reject) {
    // 要做的事情...
});
```

在 `Promise` 中只有**一个参数**，该参数是一个**函数**。

这个函数在构造之后会直接被**异步运行**，所以我们称之为**起始函数**。该函数可以传入两个参数 `resolve` 和 `reject`。

`resolve` 和 `reject` 都是函数，前者是**运行成功**时调用的函数，后者是**运行出错**时调用的函数。

在 `Promise` 类中还有三个方法：

-  **`.then()`**：正常执行的序列（是异步执行的）
-  **`.catch()`**：异常处理的序列
-  **`.finally()`**：最后一定会执行的序列。

我们还可以在` then()`方法中，使用 `throw `来自定义错误，从而提交到 `catch `方法中输出定义的错误。

**`注意`**：

- `resolve 和 reject` 的作用域只有起始函数，在其他序列当中不能使用；
- `resolve 和 reject `并不能够使起始函数停止运行，别忘了 `return`。

```js
<script>
    new Promise(function (resolve, reject) {
        console.log("我是：" + 111)
        // 调用 resolve 传入参数 222
        resolve(222)
    }).then(function (value) {
        console.log("我是：", value)
        // 内部使用 reutrn 返回值，传给下一个 then 方法当参数
        return 333
    }).then(function (value) {
        console.log("我是：", value)
        throw "我定义一个错误"
    }).catch(function (err) {
        console.log(err)
    }).finally(function () {
        console.log("我是都会执行的fanilly方法")
    })
</script>
/* 输出：
	我是：111
	我是： 222
	我是： 333
	我定义一个错误
	我是都会执行的fanilly方法
*/
```

```js
<script>
    new Promise(function (resolve, reject) {
        let a = 4
        let b = 2
        // 错误直接调用 reject 去 catch 输出错误
        if (b == 0) reject("除数不能为0")
        // 这种方法也可以
        // if (b == 0) throw ("除数不能为0")
        // 条件成立则调用 resolve 并传入参数
        else resolve(a / b)
    }).then(function (value) {
        console.log(value) // 输出：2
    }).catch(function (err) {
        console.log(err)
    })
</script>
```

## 异步函数

异步函数（`async function`）是 ECMAScript 2017 (ECMA-262) 标准的规范，几乎被所有浏览器所支持，除了 Internet Explorer。

在异步函数中可以使用 `await` 指令，而在其后**必须**跟着一个 `Promise` 类，当 `Promise` 运行时异步函数就会暂停，直至它运行结束后才会继续运行。

其实异步函数实际上原理与 `Promise` 原生 API 的机制是**一模一样**的，只不过更便于程序员阅读。

当我们需要对**异常进行处理**的时候可以使用 `try-catch`。

```js
<script>
    async function asyncFunc() {
        try {
            await new Promise(function (resolve, reject) {
                console.log("我是正确的") // 执行 resolve
                throw "我是故意出错的" // 执行 reject
                // 这种写法也可以
                // reject("我是故意出错的")
            })
        } catch (err) {
            console.log(err)
        }
    }
    asyncFunc()
</script>
/* 输出：
	我是正确的
	我是故意出错的
*/
```

## 注意

1. `resolve 和 reject` 的作用域只有**起始函数**，在其他序列当中不能使用；
2. `resolve 和 reject `并不能够使起始函数停止运行，别忘了 `return`；
3. `then、catch 和 finally `序列顺序可以颠倒，但一般使用 `then-catch-finally` 的顺序编写程序；
4. `then` 块默认会**向下顺序**执行，`return` 是不能中断的，可以通过 `throw` 来跳转至 `catch` 实现中断。