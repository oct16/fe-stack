## Reflect

> Reflect 对象与 Proxy 对象一样，也是 ES6 为了操作对象而提供的新 API。Reflect 对象的设计目的有这样几个。

-   将 Object 对象的一些明显属于语言内部的方法（比如 Object.defineProperty），放到 Reflect 对象上。现阶段，某些方法同时在 Object 和 Reflect 对象上部署，未来的新方法将只部署在 Reflect 对象上。也就是说，**_从 Reflect 对象上可以拿到语言内部的方法_**。

-   **_修改某些 Object 方法的返回结果，让其变得更合理_**。比如，Object.defineProperty(obj, name, desc)在无法定义属性时，会抛出一个错误，而 Reflect.defineProperty(obj, name, desc)则会返回 false, 因此可以不需要写 try catch

-   **_让 Object 操作都变成函数行为_**。某些 Object 操作是命令式，比如 name in obj 和 delete obj[name]，而 Reflect.has(obj, name)和 Reflect.deleteProperty(obj, name)让它们变成了函数行为。

```js
// 老写法
"assign" in Object // true
// 新写法
Reflect.has(Object, "assign") // true
```

-   **_Reflect 对象的方法与 Proxy 对象的方法一一对应_**，只要是 Proxy 对象的方法，就能在 Reflect 对象上找到对应的方法。这就让 Proxy 对象可以方便地调用对应的 Reflect 方法，完成默认行为，作为修改行为的基础。也就是说，不管 Proxy 怎么修改默认行为，你总可以在 Reflect 上获取默认行为。

##### 例子一

```js
Proxy(target, {
    set: function(target, name, value, receiver) {
        var success = Reflect.set(target, name, value, receiver)
        if (success) {
            console.log("property " + name + " on " + target + " set to " + value)
        }
        return success
    }
})
```

##### 例子二

```js
var loggedObj = new Proxy(obj, {
    get(target, name) {
        console.log("get", target, name)
        return Reflect.get(target, name)
    },
    deleteProperty(target, name) {
        console.log("delete" + name)
        return Reflect.deleteProperty(target, name)
    },
    has(target, name) {
        console.log("has" + name)
        return Reflect.has(target, name)
    }
})
```

##### 例子三

```js
// 老写法
Function.prototype.apply.call(Math.floor, undefined, [1.75]) // 1

// 新写法
Reflect.apply(Math.floor, undefined, [1.75]) // 1
```

### Referer

-   http://es6.ruanyifeng.com/?search=reflect&x=0&y=0#docs/reflect
