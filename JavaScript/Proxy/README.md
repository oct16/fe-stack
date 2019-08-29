## Proxy

Proxy 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。Proxy 这个词的原意是代理，用在这里表示由它来“代理”某些操作，可以译为“代理器”。
我们可以这样认为,Proxy 是 Object.defineProperty 的全方位 **_加强版_**

```javascript
var obj = new Proxy(
    {},
    {
        get: function(target, key, receiver) {
            console.log(`getting ${key}!`)
            return Reflect.get(target, key, receiver)
        },
        set: function(target, key, value, receiver) {
            console.log(`setting ${key}!`)
            return Reflect.set(target, key, value, receiver)
        }
    }
)
```

### Proxy 与 Object.defineProperty 区别

-   Proxy 可以直接监听对象而非属性
-   Proxy 可以直接监听数组的变化
-   Proxy 有多达 13 种拦截方法,不限于 apply、ownKeys、deleteProperty、has 等等是 Object.defineProperty 不具备的
-   Proxy 返回的是一个新对象
-   Proxy 劣势是兼容性问题，无法用 polyfill 磨平

### DOC

-   http://es6.ruanyifeng.com/#docs/proxy
