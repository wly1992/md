<!--
 * @Autor: Wanglinyu
 * @Date: 2021-08-09 11:06:34
 * @LastEditors: Wanglinyu
 * @LastEditTime: 2021-08-09 11:07:08
 * @Description: 
 * @FilePath: \md\vue\vuees6.md
-->
<h2>Vue es6技巧写法</h2>

### 一个值判断a或者判断b

- 普通写法

```
if(flg===a || fla===b)
```
- 其他
```
['a','b'].includes(flg)
```

### V-FOR渲染

- 一般
```
<li v-for="(item,index) in data" :key="index">
  {{item.uuid}}
</li>
```
- 解构赋值
```
<li v-for="{uuid} in data" :key="uuid">
  {{uuid}}
</li>
```
### Watch多个方法调用， 使用数组

```
// 你可以传入回调数组，它们会被逐一调用
e: [
  'handle1',
  function handle2 (val, oldVal) { /* ... */ },
  {
    handler: function handle3 (val, oldVal) { /* ... */ },
    /* ... */
  }
]
```

### hook监听生命周期

- 同一组件
```
this.$on/$once('hook:生命周期'，callback)
```
- 父子组件
不使用hook
```
//父组件中这样写
<rl-child
  :value="40"
  @childMounted="handleChildMounted"
/>
// 子组件中这样写
mounted () {
  this.$emit('childMounted')
},
```
使用hook
```
//父组件中这样写
<rl-child
  :value="40"
  @hook:mounted="handleChildMounted"
/>
 
// 子组件中不用写东西
mounted () {
   
},
```