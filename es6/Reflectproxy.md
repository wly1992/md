## Reflect 与 Proxy <!-- {docsify-ignore} -->

Proxy 与 Reflect 是 ES6 为了操作对象引入的 API 。

Proxy 可以对目标对象的读取、函数调用等操作进行拦截，然后进行操作处理。它不直接操作对象，而是像代理模式，通过对象的代理对象进行操作，在进行这些操作时，可以添加一些需要的额外操作。

Reflect 可以用于获取目标对象的行为，它与 Object 类似，但是更易读，为操作对象提供了一种更优雅的方式。它的方法与 Proxy 是对应的。

### 基本用法

#### Proxy

一个 Proxy 对象由两个部分组成： target 、 handler 。在通过 Proxy 构造函数生成实例对象时，需要提供这两个参数。 target 即目标对象， handler 是一个对象，声明了代理 target 的指定行为。

```
let target = {
    name: 'Tom',
    age: 24
}
let handler = {
    get: function(target, key) {
        console.log('getting '+key);
        return target[key]; // 不是target.key
    },
    set: function(target, key, value) {
        console.log('setting '+key);
        target[key] = value;
    }
}
let proxy = new Proxy(target, handler)
proxy.name     // 实际执行 handler.get
proxy.age = 25 // 实际执行 handler.set
// getting name
// setting age
// 25
 
// target 可以为空对象
let targetEpt = {}
let proxyEpt = new Proxy(targetEpt, handler)
// 调用 get 方法，此时目标对象为空，没有 name 属性
proxyEpt.name // getting name
// 调用 set 方法，向目标对象中添加了 name 属性
proxyEpt.name = 'Tom'
// setting name
// "Tom"
// 再次调用 get ，此时已经存在 name 属性
proxyEpt.name
// getting name
// "Tom"
 
// 通过构造函数新建实例时其实是对目标对象进行了浅拷贝，因此目标对象与代理对象会互相
// 影响
targetEpt)
// {name: "Tom"}
 
// handler 对象也可以为空，相当于不设置拦截操作，直接访问目标对象
let targetEmpty = {}
let proxyEmpty = new Proxy(targetEmpty,{})
proxyEmpty.name = "Tom"
targetEmpty) // {name: "Tom"}
```
### 实例方法

> `get(target, propKey, receiver)`

用于 target 对象上 propKey 的读取操作。

```
let exam ={
    name: "Tom",
    age: 24
}
let proxy = new Proxy(exam, {
  get(target, propKey, receiver) {
    console.log('Getting ' + propKey);
    return target[propKey];
  }
})
proxy.name 
// Getting name
// "Tom"
```

get() 方法可以继承。

```
let proxy = new Proxy({}, {
  get(target, propKey, receiver) {
      // 实现私有属性读取保护
      if(propKey[0] === '_'){
          throw new Erro(`Invalid attempt to get private     "${propKey}"`);
      }
      console.log('Getting ' + propKey);
      return target[propKey];
  }
});
 
let obj = Object.create(proxy);
obj.name
// Getting name
```

> `set(target, propKey, value, receiver)`

用于拦截 target 对象上的 propKey 的赋值操作。如果目标对象自身的某个属性，不可写且不可配置，那么set方法将不起作用。

```
let validator = {
    set: function(obj, prop, value) {
        if (prop === 'age') {
            if (!Number.isInteger(value)) {
                throw new TypeError('The age is not an integer');
            }
            if (value > 200) {
                throw new RangeError('The age seems invalid');
            }
        }
        // 对于满足条件的 age 属性以及其他属性，直接保存
        obj[prop] = value;
    }
};
let proxy= new Proxy({}, validator)
proxy.age = 100;
proxy.age           // 100
proxy.age = 'oppps' // 报错
proxy.age = 300     // 报错
```

第四个参数 receiver 表示原始操作行为所在对象，一般是 Proxy 实例本身。

```
const handler = {
    set: function(obj, prop, value, receiver) {
        obj[prop] = receiver;
    }
};
const proxy = new Proxy({}, handler);
proxy.name= 'Tom';
proxy.name=== proxy // true
 
const exam = {}
Object.setPrototypeOf(exam, proxy)
exam.name = "Tom"
exam.name === exam // true
```

> 注意，严格模式下，set代理如果没有返回true，就会报错。