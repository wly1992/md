<!--
 * @Autor: Wanglinyu
 * @Date: 2021-08-09 10:50:32
 * @LastEditors: Wanglinyu
 * @LastEditTime: 2021-08-20 14:24:55
 * @Description: 
 * @FilePath: \md\vue\towWayBind.md
-->
<h2>Vue双向绑定原理</h2>

### Vue2.X

#### 访问器属性

访问器属性是对象中的一种特殊属性，它不能直接在对象中设置，而必须通过 defineProperty() 方法单独定义

```
var obj = {}
Object.defineProperty(obj, 'hello', {
  get: function(){
    console.log('get方法被调用了')
  },
  set: function(val){
    console.log('set方法被调用了，参数是'+ val)
  }
})

obj.hello;   //get方法被调用了
obj.hello = 'abc';   //set方法被调用了，参数是abc
```

用defineProperty来监听数据的变化

#### 分解任务

我们最终要实现的是：

```
<div id="app">
  <input type="text" v-model="text">
  {{ text }}
</div>
```

```
var vm = new Vue({
  el: '#app',
  data: {
    text: 'hello world'
  }
})
```

首先将该任务分成几个子任务：

1、输入框以及文本节点与 data 中的数据绑定

2、输入框内容变化时，data 中的数据同步变化。即 view => model 的变化。

3、data 中的数据变化时，文本节点的内容同步变化。即 model => view 的变化。

要实现任务一，需要对 DOM 进行编译，这里有一个知识点：DocumentFragment。

#### DocumentFragment

DocumentFragment（文档片段）可以看作节点容器，它可以包含多个子节点，当我们将它插入到 DOM 中时，只有它的子节点会插入目标节点，所以把它看作一组节点的容器。使用 DocumentFragment 处理节点，速度和性能远远优于直接操作 DOM。Vue 进行编译时，就是将挂载目标的所有子节点劫持（真的是劫持，通过 append 方法，DOM 中的节点会被自动删除）到 DocumentFragment 中，经过一番处理后，再将 DocumentFragment 整体返回插入挂载目标。

```
<div id="app">
  <input type="text" id="a">
  <span id="b"></span>
</div>

<script>
  var dom = nodeToFragment(documnet.getElementById('app'));

  function nodeToFragment(node) {
    var flag = document.createDocumentFragment();
    var child;
    while (child = node.firstChild) {
      flag.appendChild(child);
    }
    return flag;
  }

  documnet.getElementById('app').appendChild(dom)
</script>
```

#### 数据初始化绑定

```
function compile(node, vm){
  var reg = /\{\{(.*)\}\}/;
  //节点类型为元素
  if(node.nodeType ===1 ) {
    var attr = node.attributes;
    //解析属性
    for (var i =0; i< attr.length;i++){
      if(attr[i].nodenName == 'v-model') {
        var name = attr[i].nodeValue;   //获取v-model绑定的属性名
        node.value = vm.data[name];   //将data的值赋给该node
        node.removeAttribute('v-model')
      }
    }
  }

  //节点类型为text
  if(node.nodeType === 3) {
    if(reg.test(node.nodeValue)){
      var name = RegRxp.$1 //获取匹配到的字符串
      name = name.trim();
      node.nodeValue = vm.data[name]
    }
  }
}

function nodeToFragment(node, vm) {
  var flag = document.createDocumentFragment();
  var child;

  while(child = node.firstChild) {
    compile(child, vm);
    flag.appendChild(child)
  }

  return flag;
}

function Vue(option) {
  this.data = options.data;
  var id = options.el;
  var dom = nodeToFragment(documnet.getElementById(id),this);
  // 编译完成后，将dom返回到app中
  document.getElementById(id).appendChild(dom)
}

var vm = new Vue({
  el: 'app',
  data: {
    text: 'hello world'
  }
})
```
以上代码实现了任务一，我们可以看到，hello world已经呈现在输入框和文本节点中。

#### 响应式的数据绑定

再来看任务二的实现思路：当我们在输入框输入数据的时候，首先触发 input 事件（或者 keyup、change 事件），在相应的事件处理程序中，我们获取输入框的 value 并赋值给 vm 实例的 text 属性。我们会利用 defineProperty 将 data 中的 text 设置为 vm 的访问器属性，因此给 vm.text 赋值，就会触发 set 方法。在 set 方法中主要做两件事，第一是更新属性的值，第二留到任务三再说。

```
function defineReactive(obj, key, val){
  Object.defineProperty(obj, key, {
    get: function(){
      return val
    },
    set: function(newVal){
      if(newVal === val) return
      val = newVal
    }
  })
}

function observe(obj, vm) {
  Object.keys(obj).forEach(key=>{
    defineReactive(vm, key, obj[key])
  })
}

function Vue(options) {
  this.data = options.data;
  var data = this.data;

  observe(data,this)

  var id = options.el;
  var dom = nodeToFragment(document.getElementById(id), this);

  document.getElementById(id).appendChild(dom)
}
```

```
function compile(node, vm){
  var reg = /\{\{(.*)\}\}/;
  if(node.nodeType === 1) {
    var attr = node.attributes;
    for(var i=0; i<attr.length;i++){
      if(attr[i].nodeName == 'v-model') {
        var name = attr[i].nodeValue;
        node.addEventListener('input',function(e){
          vm[name] = e.target.value
        })
        node.value = vm[name]
        node.removeAttribute('v-model)
      }
    }
  }

  if(nodeType === 3) {
    if(reg.test(node.nodeValue)){
      var name = RegExp.$1;
      node.nodeValue = vm[name]
    }
  }
}
```
任务二也就完成了，text 属性值会与输入框的内容同步变化：

#### 订阅/发布模式（subscribe&publish）

text 属性变化了，set 方法触发了，但是文本节点的内容没有变化。如何让同样绑定到 text 的文本节点也同步变化呢？这里又有一个知识点：订阅发布模式。

订阅发布模式（又称观察者模式）定义了一种一对多的关系，让多个观察者同时监听某一个主题对象，这个主题对象的状态发生改变时就会通知所有观察者对象。

发布者发出通知 => 主题对象收到通知并推送给订阅者 => 订阅者执行相应操作

```
// 一个发布者publisher
var pub = {
  public: function() {
    dep.notify();
  }
}

//三个订阅者subscribers
var sub1 = { updata: function(){ console.log(1) } }
var sub2 = { updata: function(){ console.log(2) } }
var sub3 = { updata: function(){ console.log(3) } }

//一个主题对象
function Dep (){
  this.subs = [sub1, sub2, sub3];
}

Dep.prototype.notify = function () {
  this.subs.forEach( sub => {
    sub.updata()
  })
}
var dep = new Dep()
pub.publish()   //1, 2, 3
```

之前提到的，当 set 方法触发后做的第二件事就是作为发布者发出通知：“我是属性 text，我变了”。文本节点则是作为订阅者，在收到消息后执行相应的更新操作。

#### 双向绑定的实现

回顾一下，每当 new 一个 Vue，主要做了两件事：第一个是监听数据：observe(data)，第二个是编译 HTML：nodeToFragement(id)。

在监听数据的过程中，会为 data 中的每一个属性生成一个主题对象 dep。

在编译 HTML 的过程中，会为每个与数据绑定相关的节点生成一个订阅者 watcher，watcher 会将自己添加到相应属性的 dep 中。

我们已经实现：修改输入框内容 => 在事件回调函数中修改属性值 => 触发属性的 set 方法。

接下来我们要实现的是：发出通知 dep.notify() => 触发订阅者的 update 方法 => 更新视图。

这里的关键逻辑是：如何将 watcher 添加到关联属性的 dep 中。

```
function compile(node, vm){
  var reg = /\{\{(.*)\}\}/;

  if(node.nodeType ===3) {
    if(reg.test(node.nodeValue)){
      var name = RegExp.$1;;
      name = name.trim();
      // node.nodeValue = vm[name];

      new Watcher(vm, node, name)
    }
  }
}
```
在编译 HTML 过程中，为每个与 data 关联的节点生成一个 Watcher。Watcher 函数中发生了什么呢？

```
function Watcher (vm, node, name){
  Dep.target = this;
  this.name = name;
  this.node = node;
  this.vm = vm;
  this.update();
  Dep.target = null;
}

Watcher.prototype = {
  updata: function() {
    this.get();
    this.node.nodeValue = this.value;
  },
  get: function(){
    this.value = this.vm.[this.name]
  }
}
```

首先，将自己赋给了一个全局变量 Dep.target；

其次，执行了 update 方法，进而执行了 get 方法，get 的方法读取了 vm 的访问器属性，从而触发了访问器属性的 get 方法，get 方法中将该 watcher 添加到了对应访问器属性的 dep 中；

再次，获取属性的值，然后更新视图。

最后，将 Dep.target 设为空。因为它是全局变量，也是 watcher 与 dep 关联的唯一桥梁，任何时刻都必须保证 Dep.target 只有一个值。

```
function defineReactive(obj, key, val){
  var dep = new Dep();

  Object.defineProperty(obj,key,{
    get: function(){
      if(Dep.target) dep.addSub(Dep.target);
      return val;
    },

    set: function(newVal){
      if(newVal === val) return 
      val = neewVal;
      dep.notify()
    }
  })
}

function Dep() {
  this.subs = []
}

Dep.prototype = {
  addSub: function(sub){
    this.subs.push(sub);
  },

  notify: function(){
    this.subs.forEach(sub=>{
      sub.update()
    })
  }
}
```

至此，hello world 双向绑定就基本实现了。文本内容会随输入框内容同步变化，在控制器中修改 vm.text 的值，会同步反映到文本内容中。

完整代码：https://github.com/bison1994/two-way-data-binding

更详尽的源码分析，可以参考滴滴的这篇文章：https://github.com/DDFE/DDFE-blog/issues/7

### Vue3.X

相信大家都知道，vue实现数据双向绑定无非就是采用数据劫持的方式，结合发布订阅模式，通过Object.defineProperty()来劫持各个属性的setter，getter以监听属性的变动，在数据变动时发布消息给订阅者，以上是vue2.x的实现原理,3.0的话就是proxy替换Object.defineProperty()，其他流程没有改变。总结一下数据相应原理就发布订阅模式（观察者模式）+ 数据劫持（defineProperty or proxy）

![blockchain](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f88b43aac797442a8a128eec66a14e17~tplv-k3u1fbpfcp-watermark.awebp)