<!--
 * @Autor: Wanglinyu
 * @Date: 2021-08-09 10:26:34
 * @LastEditors: Wanglinyu
 * @LastEditTime: 2021-08-09 10:42:06
 * @Description: 
 * @FilePath: \md\vue\componentCommunication.md
-->

<h2>vue组件之间通信的8种方式</h2>

### 1.props和$emit

### 2.$attrs和$listeners

如果父组件A下面有子组件B，组件B下面有组件C,这时如果组件A想传递数据给组件C怎么办呢？

### 3.中央事件总线

上面两种方式处理的都是父子组件之间的数据传递，而如果两个组件不是父子关系呢？这种情况下可以使用中央事件总线的方式。新建一个Vue事件bus对象，然后通过bus.$emit触发事件，bus.$on监听触发的事件。

> var bus=new Vue();

> bus.$emit('globalEvent',val)

> bus.$on('globalEvent',(val)=>{})

### 4.provide和inject

他们成对出现，用于父级组件向下传递数据。

父组件中通过provider来提供变量，然后在子组件中通过inject来注入变量。不论子组件有多深，只要调用了inject那么就可以注入provider中的数据。而不是局限于只能从当前父组件的prop属性来获取数据，只要在父组件的生命周期内，子组件都可以调用。

### 5.v-model

父组件通过v-model传递值给子组件时，会自动传递一个value的prop属性，在子组件中通过this.$emit(‘input',val)自动修改v-model绑定的值

### 6.$parent和$children

在组件内部可以直接通过子组件$parent对父组件进行操作，父组件通过$children对子组件进行操作.

### 7.$ref

### 8. vuex处理组件之间的数据交互
