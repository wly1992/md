# 模板语法（指令与修饰符）

在底层的实现上，Vue 将模板编译成虚拟 DOM 渲染函数。结合响应系统，Vue 能够智能地计算出最少需要重新渲染多少组件，并把 DOM 操作次数减到最少。

如果你熟悉虚拟 DOM 并且偏爱 JavaScript 的原始力量，你也可以不用模板，**直接写渲染 (render) 函数，使用可选的 JSX 语法。**

#### 指令

指令 (Directives) 是带有 v- 前缀的特殊 attribute。指令 attribute 的值预期是单个 JavaScript 表达式 (v-for 是例外情况，稍后我们再讨论)。指令的职责是，当表达式的值改变时，将其产生的连带影响，响应式地作用于 DOM。

##### vue的内置指令有哪些构成

内置指令有16个：v-text、v-html、v-show、v-if、v-else、v-else-if、v-for、v-on、v-bind、v-model、v-slot、v-pre、v-cloak、v-once、v-memo、v-is；其中v-memo是3.2新增的，v-is在3.1.0中废弃。v-show用于控制元素的显藏、v-if用于根据表达式的真假值来有条件地渲染元素。

1. v-text的作用是，用于更新元素的 textContent

2. v-html与v-text很像，只是v-html用于更新元素的 innerHTML

3. v-show可以根据表达式的真假值，切换元素的display值，用于控制元素的展示和隐藏

需要注意：v-show 不支持 `<template>` 元素，也不支持 v-else

4. v-if用于根据表达式的真假值来有条件地渲染元素

与v-show相比，v-if在切换时是元素的销毁或重建，而不是简单的显示隐藏；并且v-if可以是 `<template>`，如果元素是 `<template>`，将提取它的内容作为条件块

5. v-else无需表达式，表示添加一个“else 块”，相当于v-if满足条件时展示v-if的元素，否则展示v-else的元素

6. v-else-if 同理，表示 v-if 的“else if 块”，和v-else一样，前一个兄弟元素必须有v-if或 v-else-if

7. v-for一个用于迭代的指令，可以根据源数据多次渲染元素或模板块

8. v-on用于给元素绑定事件，可以缩写为：@

事件修饰符

- .stop - 调用 event.stopPropagation()
- .prevent - 调用 event.preventDefault()
- .capture - 添加事件侦听器时使用 capture 模式
- .self - 只当事件是从侦听器绑定的元素本身触发时才触发回调
- .once - 只触发一次回调
- .native - 将原生事件绑定到组件 我们知道在自定义组件上，只能监听自定义事件，一些原生事件（比如click）是没有办法直接触发的，但是使用.native修饰符可以帮我们办到这点
- .passive - Vue 还对应 addEventListener 中的 passive 选项提供了 .passive 修饰符

```html
<template>
  <div v-on:scroll.passive="onScroll"></div>
</template>
```

按键修饰符

- .enter
- .page-down

keyCode 的事件用法已经被废弃了并可能不会被最新的浏览器支持；为了在必要的情况下支持旧浏览器，Vue 提供了绝大多数常用的按键码的别名：

enter、tab、delete、esc、space、up、down、left、right、ctrl、alt、shift、meta

- .{keyAlias} - 仅当事件是从特定键触发时才触发回调（enter、tab、delete、esc、space、up、down、left、right、ctrl、alt、shift、meta）
- .left - 只当点击鼠标左键时触发
- .right - 只当点击鼠标右键时触发
- .middle - 只当点击鼠标中键时触发


需要注意，用在普通元素上时，只能监听原生 DOM 事件。用在自定义元素组件上时，也可以监听子组件触发的自定义事件

9. v-bind用于绑定数据和元素属性，可以缩写为: 或.(当使用 .prop 修饰符时)

v-bind的3个修饰符

- .camel - 将 kebab-case attribute 名转换为 camelCase
- .sync - 当我们想要在父组件和子组件之间对某个属性值进行双向绑定时,有什么便捷的方式？是的只要.sync修饰符即可办到
- .prop - 将一个绑定强制设置为一个 DOM property。3.2+
- .attr - 将一个绑定强制设置为一个 DOM attribute。3.2+

10. v-model限制于`<input>` `<select>` `<textarea>` 和 components

v-model的3个修饰符：

- .lazy - 惰性更新，监听 change 而不是 input 事件
- .number - 输入字符串转为有效的数字
- .trim - 输入首尾空格过滤

11. v-pre指令用于跳过这个元素及其子元素的编译过程，例如:

```html
<template>
  <span v-pre>{{ this will not be comiled }}</span>
</template>
````

12. v-cloak指令主要用于解决插值表达式在页面闪烁问题

```html
<div v-cloak>
  {{ message }}
</div>
```

```css
[v-cloak] {
  display: none
}
```

这样div只会在编译结束后显示

13. v-once指令用于表示只渲染一次，当要重新渲染，元素/组件及其所有的子节点将被视为静态内容并跳过

14. v-memo 3.2+ 用于缓存一个模板的子树，该指令接收一个固定长度的数组作为依赖值进行记忆比对。如果数组中的每个值都和上次渲染的时候相同，则整个该子树的更新会被跳过

15.  v-is 已在 3.1.0 中废弃，改用:is

```html
<component :is="currentView"></component>
```

