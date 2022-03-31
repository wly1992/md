# Vue⾯试题

## 你对MVVM的理解?

MVVM 模式，顾名思义即 Model-View-ViewModel 模式。它萌芽于2005年微软推出的基于 Windows 的⽤户界⾯框架 WPF ，前端最早的 MVVM 框架 knockout 在2010年发布。

Model 层: 对应数据层的域模型，它主要做域模型的同步。通过 Ajax/fetch 等 API 完成客户端和服务端业务 Model 的同 步。在层间关系⾥，它主要⽤于抽象出 ViewModel 中视图的 Model。

View 层:作为视图模板存在，在 MVVM ⾥，整个 View 是⼀个动态模板。除了定义结构、布局外，它展示的是 ViewModel 层的数据和状态。View 层不负责处理状态，View 层做的是 数据绑定的声明、 指令的声明、 事件绑定的声 明。

ViewModel 层:把 View 需要的层数据暴露，并对 View 层的 数据绑定声明、 指令声明、 事件绑定声明 负责，也就是处 理 View 层的具体业务逻辑。ViewModel 底层会做好绑定属性的监听。当 ViewModel 中数据变化，View 层会得到更 新；⽽当 View 中声明了数据的双向绑定（通常是表单元素），框架也会监听 View 层（表单）值的变化。⼀旦值变 化，View 层绑定的 ViewModel 中的数据也会得到⾃动更新。

## MVVM的优缺点?

优点:

1. 分离视图（View）和模型（Model）,降低代码耦合，提⾼视图或者逻辑的重⽤性: ⽐如视图（View）可以独⽴于 Model变化和修改，⼀个ViewModel可以绑定不同的"View"上，当View变化的时候Model不可以不变，当Model变化 的时候View也可以不变。你可以把⼀些视图逻辑放在⼀个ViewModel⾥⾯，让很多view重⽤这段视图逻辑 
2. 提⾼可测试性: ViewModel的存在可以帮助开发者更好地编写测试代码 
3. ⾃动更新dom: 利⽤双向绑定,数据更新后视图⾃动更新,让开发者从繁琐的⼿动dom中解放

缺点: 

1. Bug很难被调试: 因为使⽤双向绑定的模式，当你看到界⾯异常了，有可能是你View的代码有Bug，也可能是Model 的代码有问题。数据绑定使得⼀个位置的Bug被快速传递到别的位置，要定位原始出问题的地⽅就变得不那么容易 了。另外，数据绑定的声明是指令式地写在View的模版当中的，这些内容是没办法去打断点debug的 
2. ⼀个⼤的模块中model也会很⼤，虽然使⽤⽅便了也很容易保证了数据的⼀致性，当时⻓期持有，不释放内存就造 成了花费更多的内存 
3. 对于⼤型的图形应⽤程序，视图状态较多，ViewModel的构建和维护的成本都会⽐较⾼

## 你对Vue⽣命周期的理解？

⽣命周期是什么

Vue 实例有⼀个完整的⽣命周期，也就是从开始创建、初始化数据、编译模版、挂载Dom -> 渲染、更新 -> 渲染、卸载 等⼀系列过程，我们称这是Vue的⽣命周期。

各个⽣命周期的作⽤: 

1. beforeCreate 组件实例被创建之初，组件的属性⽣效之前
2. created 组件实例已经完全创建，属性也绑定，但真实dom还没有⽣成， $el 还不可⽤
3. beforeMount 在挂载开始之前被调⽤：相关的 render 函数⾸次被调⽤
4. mounted el 被新创建的 vm.$el 替换，并挂载到实例上去之后调⽤该钩⼦
5. beforeUpdate 组件数据更新之前调⽤，发⽣在虚拟 DOM 打补丁之前
6. update 组件数据更新之后
7. activited keep-alive专属，组件被激活时调⽤
8. deactivated keep-alive专属，组件被销毁时调⽤
9. beforeDestory 组件销毁前调⽤
10. destoryed 组件销毁后调⽤

## 异步请求适合在哪个⽣命周期调⽤？

首先给出结论：created 和 mounted 中发起 ajax 请求是一样的，没有区别。
为啥没有区别：created 和 mounted 是在同一个 tick 中执行的，而ajax 请求的时间一定会超过一个 tick。所以即便ajax的请求耗时是 0ms， 那么也是在 nextTick 中更新数据到 DOM 中。所以说在不依赖 DOM 节点的情况下一点区别都没有。

## Vue组件如何通信？

[详情连接](/share/vuenote?id=vue%e7%bb%84%e4%bb%b6%e4%b9%8b%e9%97%b4%e9%80%9a%e4%bf%a1%e7%9a%848%e7%a7%8d%e6%96%b9%e5%bc%8f)

## computed和watch有什么区别?

computed: 

1. computed 是计算属性,也就是计算值,它更多⽤于计算值的场景 
2. computed 具有缓存性,computed的值在getter执⾏后是会缓存的，只有在它依赖的属性值改变之后，下⼀次获取 computed的值时才会重新调⽤对应的getter来计算 
3. computed 适⽤于计算⽐较消耗性能的计算场景

watch: 
1. 更多的是「观察」的作⽤,类似于某些数据的监听回调,⽤于观察 props $emit 或者本组件的值,当数据变化时来执 ⾏回调进⾏后续操作 
2. ⽆缓存性，⻚⾯重新渲染时值不变化也会执⾏ 

⼩结: 
1. 当我们要进⾏数值计算,⽽且依赖于其他数据，那么把这个数据设计为computed 
2. 如果你需要在某个数据变化时做⼀些事情，使⽤watch来观察这个数据变化

## Vue是如何实现双向绑定的?

[vue双向绑定原理](/share/towWayBind)

## Proxy与Object.defineProperty的优劣对⽐?

Proxy的优势如下: 

- Proxy可以直接监听对象⽽⾮属性 
- Proxy可以直接监听数组的变化 
- Proxy有多达13种拦截⽅法,不限于apply、ownKeys、deleteProperty、has等等是 Object.defineProperty 不具备的 Proxy返回的是⼀个新对象,我们可以只操作新的对象达到⽬的,⽽ Object.defineProperty 只能遍历对象属性直接修改
- Proxy作为新标准将受到浏览器⼚商重点持续的性能优化，也就是传说中的新标准的性能红利 

Object.defineProperty的优势如下: 

- 兼容性好,⽀持IE9

## 你是如何理解Vue的响应式系统的?

## 既然Vue通过数据劫持可以精准探测数据变化,为什么还需要虚拟 DOM进⾏diff检测差异?

现代前端框架有两种⽅式侦测变化,⼀种是pull⼀种是push 

pull: 其代表为React,我们可以回忆⼀下React是如何侦测到变化的,我们通常会⽤ setState API显式更新,然后React会进 ⾏⼀层层的Virtual Dom Diff操作找出差异,然后Patch到DOM上,React从⼀开始就不知道到底是哪发⽣了变化,只是知道 「有变化了」,然后再进⾏⽐较暴⼒的Diff操作查找「哪发⽣变化了」，另外⼀个代表就是Angular的脏检查操作。 

push: Vue的响应式系统则是push的代表,当Vue程序初始化的时候就会对数据data进⾏依赖的收集,⼀但数据发⽣变化,响 应式系统就会⽴刻得知,因此Vue是⼀开始就知道是「在哪发⽣变化了」,但是这⼜会产⽣⼀个问题,如果你熟悉Vue的响 应式系统就知道,通常⼀个绑定⼀个数据就需要⼀个Watcher,⼀但我们的绑定细粒度过⾼就会产⽣⼤量的Watcher,这会 带来内存以及依赖追踪的开销,⽽细粒度过低会⽆法精准侦测变化,因此Vue的设计是选择中等细粒度的⽅案,在组件级别 进⾏push侦测的⽅式,也就是那套响应式系统,通常我们会第⼀时间侦测到发⽣变化的组件,然后在组件内部进⾏Virtual Dom Diff获取更加具体的差异,⽽Virtual Dom Diff则是pull操作,Vue是push+pull结合的⽅式进⾏变化侦测的.

## Vue中的key到底有什么⽤？

key 是为Vue中的vnode标记的唯⼀id,通过这个key,我们的diff操作可以更准确、更快速

diff算法的过程中,先会进⾏新旧节点的⾸尾交叉对⽐,当⽆法匹配的时候会⽤新节点的 key 与旧节点进⾏⽐对,然后超出 差异.

> diff程可以概括为：oldCh和newCh各有两个头尾的变量StartIdx和EndIdx，它们的2个变量相互⽐较，⼀共有4种 ⽐较⽅式。如果4种⽐较都没匹配，如果设置了key，就会⽤key进⾏⽐较，在⽐较的过程中，变量会往中间靠， ⼀旦StartIdx>EndIdx表明oldCh和newCh⾄少有⼀个已经遍历完了，就会结束⽐较,这四种⽐较⽅式就是⾸、尾、 旧尾新头、旧头新尾.

- 准确: 如果不加 key ,那么vue会选择复⽤节点(Vue的就地更新策略),导致之前节点的状态被保留下来,会产⽣⼀系列 的bug.
- 快速: key的唯⼀性可以被Map数据结构充分利⽤,相⽐于遍历查找的时间复杂度O(n),Map的时间复杂度仅仅为O(1).