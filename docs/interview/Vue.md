# Vue ⾯试题

## Vue中子组件是否能使用在子组件上绑定了但未在props中定义的变量 ？

在 Vue.js 中，子组件默认情况下不能使用未在 props 选项中明确声明的属性。Vue 的组件系统设计旨在明确组件之间的接口，其中 props 作为组件公开的属性，必须被明确声明。这一设计原则增强了代码的可读性和可维护性，确保了组件之间通信的清晰和有序。

绕过限制的方法：

使用 $attrs
尽管子组件不能直接使用未声明的 props，但它可以通过 $attrs 对象来接收来自父组件的非 props 属性。$attrs 包含了传递给组件但未被 props 接收的所有属性（包括 class 和 style，以及绑定的事件监听器，如果 inheritAttrs 配置为 false 的话）。

## Vue中给对象添加新属性时，界面不刷新怎么办?

- 如果为对象添加少量的新属性，可以直接采用Vue.set()

- 如果需要为新对象添加大量的新属性，则通过Object.assign()创建新对象

- 如果你实在不知道怎么操作时，可采取$forceUpdate()进行强制刷新 (不建议)

## 虚拟 dom 渲染到页面的时候，框架会做哪些处理？

当虚拟 DOM 渲染到页面时，框架通常会执行以下动作：

1. Diff 算法：框架会将新的虚拟 DOM 与旧的虚拟 DOM 进行对比，找出它们之间的差异。这个过程被称为 Diff 算法。Diff 算法的目标是通过最小化操作次数来更新真实 DOM，以提高性能。

2. 创建和更新 DOM 节点：根据 Diff 算法的结果，框架会创建或更新需要改变的 DOM 节点。如果一个节点在新的虚拟 DOM 中存在但在旧的虚拟 DOM 中不存在，框架会创建该节点并添加到页面上。如果一个节点在新的虚拟 DOM 和旧的虚拟 DOM 中都存在，但其属性或子节点发生变化，框架会更新相应的 DOM 节点。这样可以确保只有实际需要更改的部分才会重新渲染，减少不必要的操作。

3. 处理事件绑定：框架会重新绑定事件处理程序，以便在更新后正确响应用户交互。这包括添加、更新或删除事件监听器。

4. 卸载节点：如果一个节点在新的虚拟 DOM 中不存在但在旧的虚拟 DOM 中存在，框架会从页面上移除该节点。这可以防止内存泄漏和资源浪费。

5. 触发生命周期钩子：在渲染到页面后，框架会触发相应的生命周期钩子函数（如 Vue 中的 mounted），以便开发人员可以在适当的时机执行自定义操作。

虚拟 DOM 渲染到页面时，框架会根据 Diff 算法的结果进行 DOM 的创建、更新和删除操作。这样可以最小化对真实 DOM 的改动，提高性能，并确保页面与新的虚拟 DOM 保持同步。此外，框架还会处理事件绑定和触发生命周期钩子函数，以便提供更多的开发扩展能力和灵活性。

## 你对 MVVM 的理解?

MVVM 模式，顾名思义即 Model-View-ViewModel 模式。它萌芽于 2005 年微软推出的基于 Windows 的⽤户界⾯框架 WPF ，前端最早的 MVVM 框架 knockout 在 2010 年发布。

Model 层: 对应数据层的域模型，它主要做域模型的同步。通过 Ajax/fetch 等 API 完成客户端和服务端业务 Model 的同 步。在层间关系⾥，它主要⽤于抽象出 ViewModel 中视图的 Model。

View 层:作为视图模板存在，在 MVVM ⾥，整个 View 是⼀个动态模板。除了定义结构、布局外，它展示的是 ViewModel 层的数据和状态。View 层不负责处理状态，View 层做的是 数据绑定的声明、 指令的声明、 事件绑定的声 明。

ViewModel 层:把 View 需要的层数据暴露，并对 View 层的 数据绑定声明、 指令声明、 事件绑定声明 负责，也就是处 理 View 层的具体业务逻辑。ViewModel 底层会做好绑定属性的监听。当 ViewModel 中数据变化，View 层会得到更 新；⽽当 View 中声明了数据的双向绑定（通常是表单元素），框架也会监听 View 层（表单）值的变化。⼀旦值变 化，View 层绑定的 ViewModel 中的数据也会得到⾃动更新。

## MVVM 的优缺点?

优点:

1. 分离视图（View）和模型（Model）,降低代码耦合，提⾼视图或者逻辑的重⽤性: ⽐如视图（View）可以独⽴于 Model 变化和修改，⼀个 ViewModel 可以绑定不同的"View"上，当 View 变化的时候 Model 不可以不变，当 Model 变化 的时候 View 也可以不变。你可以把⼀些视图逻辑放在⼀个 ViewModel ⾥⾯，让很多 view 重⽤这段视图逻辑
2. 提⾼可测试性: ViewModel 的存在可以帮助开发者更好地编写测试代码
3. ⾃动更新 dom: 利⽤双向绑定,数据更新后视图⾃动更新,让开发者从繁琐的⼿动 dom 中解放

缺点:

1. Bug 很难被调试: 因为使⽤双向绑定的模式，当你看到界⾯异常了，有可能是你 View 的代码有 Bug，也可能是 Model 的代码有问题。数据绑定使得⼀个位置的 Bug 被快速传递到别的位置，要定位原始出问题的地⽅就变得不那么容易 了。另外，数据绑定的声明是指令式地写在 View 的模版当中的，这些内容是没办法去打断点 debug 的
2. ⼀个⼤的模块中 model 也会很⼤，虽然使⽤⽅便了也很容易保证了数据的⼀致性，当时⻓期持有，不释放内存就造 成了花费更多的内存
3. 对于⼤型的图形应⽤程序，视图状态较多，ViewModel 的构建和维护的成本都会⽐较⾼

## 你对 Vue ⽣命周期的理解？

⽣命周期是什么

Vue 实例有⼀个完整的⽣命周期，也就是从开始创建、初始化数据、编译模版、挂载 Dom -> 渲染、更新 -> 渲染、卸载 等⼀系列过程，我们称这是 Vue 的⽣命周期。

各个⽣命周期的作⽤:

1. beforeCreate 组件实例被创建之初，组件的属性⽣效之前
2. created 组件实例已经完全创建，属性也绑定，但真实 dom 还没有⽣成， $el 还不可⽤
3. beforeMount 在挂载开始之前被调⽤：相关的 render 函数⾸次被调⽤
4. mounted el 被新创建的 vm.$el 替换，并挂载到实例上去之后调⽤该钩⼦
5. beforeUpdate 组件数据更新之前调⽤，发⽣在虚拟 DOM 打补丁之前
6. update 组件数据更新之后
7. activited keep-alive 专属，组件被激活时调⽤
8. deactivated keep-alive 专属，组件被销毁时调⽤
9. beforeDestory 组件销毁前调⽤
10. destoryed 组件销毁后调⽤

## 异步请求适合在哪个⽣命周期调⽤？

首先给出结论：created 和 mounted 中发起 ajax 请求是一样的，没有区别。
为啥没有区别：created 和 mounted 是在同一个 tick 中执行的，而 ajax 请求的时间一定会超过一个 tick。所以即便 ajax 的请求耗时是 0ms， 那么也是在 nextTick 中更新数据到 DOM 中。所以说在不依赖 DOM 节点的情况下一点区别都没有。

## Vue 组件如何通信？

[详情连接](/share/vuenote?id=vue%e7%bb%84%e4%bb%b6%e4%b9%8b%e9%97%b4%e9%80%9a%e4%bf%a1%e7%9a%848%e7%a7%8d%e6%96%b9%e5%bc%8f)

## computed 和 watch 有什么区别?

computed:

1. computed 是计算属性,也就是计算值,它更多⽤于计算值的场景
2. computed 具有缓存性,computed 的值在 getter 执⾏后是会缓存的，只有在它依赖的属性值改变之后，下⼀次获取 computed 的值时才会重新调⽤对应的 getter 来计算
3. computed 适⽤于计算⽐较消耗性能的计算场景

watch:

1. 更多的是「观察」的作⽤,类似于某些数据的监听回调,⽤于观察 props $emit 或者本组件的值,当数据变化时来执 ⾏回调进⾏后续操作
2. ⽆缓存性，⻚⾯重新渲染时值不变化也会执⾏

⼩结:

1. 当我们要进⾏数值计算,⽽且依赖于其他数据，那么把这个数据设计为 computed
2. 如果你需要在某个数据变化时做⼀些事情，使⽤ watch 来观察这个数据变化

## Vue 是如何实现双向绑定的?

[vue 双向绑定原理](/share/towWayBind)

## Proxy 与 Object.defineProperty 的优劣对⽐?

Proxy 的优势如下:

- Proxy 可以直接监听对象⽽⾮属性
- Proxy 可以直接监听数组的变化
- Proxy 有多达 13 种拦截⽅法,不限于 apply、ownKeys、deleteProperty、has 等等是 Object.defineProperty 不具备的 Proxy 返回的是⼀个新对象,我们可以只操作新的对象达到⽬的,⽽ Object.defineProperty 只能遍历对象属性直接修改
- Proxy 作为新标准将受到浏览器⼚商重点持续的性能优化，也就是传说中的新标准的性能红利

Object.defineProperty 的优势如下:

- 兼容性好,⽀持 IE9

## 你是如何理解 Vue 的响应式系统的?

## 既然 Vue 通过数据劫持可以精准探测数据变化,为什么还需要虚拟 DOM 进⾏ diff 检测差异?

现代前端框架有两种⽅式侦测变化,⼀种是 pull ⼀种是 push

pull: 其代表为 React,我们可以回忆⼀下 React 是如何侦测到变化的,我们通常会⽤ setState API 显式更新,然后 React 会进 ⾏⼀层层的 Virtual Dom Diff 操作找出差异,然后 Patch 到 DOM 上,React 从⼀开始就不知道到底是哪发⽣了变化,只是知道 「有变化了」,然后再进⾏⽐较暴⼒的 Diff 操作查找「哪发⽣变化了」，另外⼀个代表就是 Angular 的脏检查操作。

push: Vue 的响应式系统则是 push 的代表,当 Vue 程序初始化的时候就会对数据 data 进⾏依赖的收集,⼀但数据发⽣变化,响 应式系统就会⽴刻得知,因此 Vue 是⼀开始就知道是「在哪发⽣变化了」,但是这⼜会产⽣⼀个问题,如果你熟悉 Vue 的响 应式系统就知道,通常⼀个绑定⼀个数据就需要⼀个 Watcher,⼀但我们的绑定细粒度过⾼就会产⽣⼤量的 Watcher,这会 带来内存以及依赖追踪的开销,⽽细粒度过低会⽆法精准侦测变化,因此 Vue 的设计是选择中等细粒度的⽅案,在组件级别 进⾏ push 侦测的⽅式,也就是那套响应式系统,通常我们会第⼀时间侦测到发⽣变化的组件,然后在组件内部进⾏ Virtual Dom Diff 获取更加具体的差异,⽽ Virtual Dom Diff 则是 pull 操作,Vue 是 push+pull 结合的⽅式进⾏变化侦测的.

## Vue 中的 key 到底有什么⽤？

key 是为 Vue 中的 vnode 标记的唯⼀ id,通过这个 key,我们的 diff 操作可以更准确、更快速

diff 算法的过程中,先会进⾏新旧节点的⾸尾交叉对⽐,当⽆法匹配的时候会⽤新节点的 key 与旧节点进⾏⽐对,然后超出 差异.

> diff 程可以概括为：oldCh 和 newCh 各有两个头尾的变量 StartIdx 和 EndIdx，它们的 2 个变量相互⽐较，⼀共有 4 种 ⽐较⽅式。如果 4 种⽐较都没匹配，如果设置了 key，就会⽤ key 进⾏⽐较，在⽐较的过程中，变量会往中间靠， ⼀旦 StartIdx>EndIdx 表明 oldCh 和 newCh ⾄少有⼀个已经遍历完了，就会结束⽐较,这四种⽐较⽅式就是⾸、尾、 旧尾新头、旧头新尾.

- 准确: 如果不加 key ,那么 vue 会选择复⽤节点(Vue 的就地更新策略),导致之前节点的状态被保留下来,会产⽣⼀系列 的 bug.
- 快速: key 的唯⼀性可以被 Map 数据结构充分利⽤,相⽐于遍历查找的时间复杂度 O(n),Map 的时间复杂度仅仅为 O(1).

## vue 是如何识别和解析指令的？

解析模板：首先，Vue.js 会将模板代码转换为抽象语法树（AST）。
这个过程由 Vue.js 的编译器完成。编译器会遍历模板代码，将其解析成一棵抽象语法树，该树表示了模板的结构和各个元素之间的关系。

识别指令：在遍历抽象语法树的过程中，编译器会识别出模板中的指令。指令通常以 v- 开头，例如 v-if 、 v-for 、 v-bind 、 v-on 等。编译器会根据指令的名称和位置来确定它们的作用。

提取指令参数和修饰符：对于识别出的指令，编译器会进一步提取指令的参数和修饰符。指令参数通常是指令名称后面的表达式或变量，用于指定指令的具体行为。修饰符是一些额外的标识符，用于修改指令的行为或增加一些特定功能。

解析指令表达式：针对具有表达式的指令，编译器会解析指令表达式并生成对应的代码。指令表达式通常是模板中的变量或计算属性，用于动态地绑定数据到指令上。编译器会将指令表达式转化为可执行的 JavaScript 代码，以便在运行时进行数据绑定。

生成渲染函数：最后，编译器将解析后的模板和指令转换为渲染函数。渲染函数是一个 JavaScript 函数，它接收数据作为参数，并返回一个虚拟 DOM（VNode）树，用于渲染组件的视图。渲染函数包含了对指令的执行逻辑和对模板变量的处理。

Vue.js 通过编译器对模板进行解析，识别和解析指令，并将其转化为渲染函数。这个过程包括解析模板、识别指令、提取参数和修饰符、解析指令表达式，最终生成渲染函数。通过渲染函数，Vue.js 能够根据数据的变化动态更新组件的视图。

## Vue 项目中，你做过哪些性能优化？

## 怎么在 Vue 中定义全局方法？

在 Vue.js 中定义全局方法，可以通过多种方式实现，包括直接在 Vue 的原型对象上添加方法、使用 Vue 3 的全局 API (app.config.globalProperties)、以及通过混入 (mixin) 等方法。

以下是几种常见的方法：

1. 直接在 Vue 的原型对象上添加方法：

```javascript
Vue.prototype.$myMethod = function (message) {
  console.log(message);
};
```

在 Vue 组件中，可以通过 this.$myMethod() 来调用这个方法。

2. 使用 Vue 3 的全局 API (app.config.globalProperties)：

```javascript
import { createApp } from "vue";

const app = createApp(App);

app.config.globalProperties.$myMethod = function (message) {
  console.log(message);
};

app.mount("#app");
```

在 Vue 组件中，可以通过 this.$myMethod() 来调用这个方法。

3. 使用混入 (mixin)：

```javascript
const myMixin = {
  methods: {
    $myMethod(message) {
      console.log(message);
    },
  },
};
```

```javascript
import { createApp } from "vue";

const app = createApp(App);

app.mixin(myMixin);

app.mount("#app");
```

在 Vue 组件中，可以通过 this.$myMethod() 来调用这个方法。

方法四：创建插件
你可以创建一个 Vue 插件来封装全局方法，并在 main.js 中安装插件。

```javascript
// plugins/myPlugin.js
export default {
  install(app) {
    app.config.globalProperties.$myMethod = function (message) {
      console.log(message);
    };
  },
};

// main.js
import { createApp } from "vue";
import App from "./App.vue";
import myPlugin from "./plugins/myPlugin";

const app = createApp(App);

app.use(myPlugin);

app.mount("#app");
```

在 Vue 组件中，可以通过 this.$myMethod() 来调用这个方法。

以上是几种常见的方法，可以根据具体的需求和项目结构选择合适的方法来定义全局方法。
