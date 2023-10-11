# Vue 中生命周期总结及路由生命周期

1. 根组件实例：8 个 (beforeCreate、created、beforeMount、mounted、beforeUpdate、updated、beforeDestroy、destroyed)
2. 组件实例：8 个 (beforeCreate、created、beforeMount、mounted、beforeUpdate、updated、beforeDestroy、destroyed)
3. 全局路由钩子：2 个 (beforeEach、 beforeResolve、afterEach)
4. 组件路由钩子：3 个 (beforeRouteEnter、beforeRouteUpdate、beforeRouteLeave)
5. 路由独立守卫:1 个(beforeEnter)
6. 指令的周期： 5 个 (bind、inserted、update、componentUpdated、unbind)
7. beforeRouteEnter 的 next 所对应的周期
8. nextTick 所对应的周期
9. keep-alive的生命周期2个 activated、deactivated

#### Vue 各个⽣命周期的作⽤:

Vue 实例有⼀个完整的⽣命周期，也就是从开始创建、初始化数据、编译模版、挂载Dom -> 渲染、更新 -> 渲染、卸载 等⼀系列过程，我们称这是Vue的⽣命周期。

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

![lifecycle](https://v2.cn.vuejs.org/images/lifecycle.png "lifecycle")


## Vue-router 路由生命周期也叫导航守卫

### 分 3 块：全局守卫、路由独立守卫、组件内守卫

#### 全局守卫 main.js

```js
router.beforeEach((to, from, next) => {
  // 全局前置守卫
  // if(to.fullPath === '/shoppingCart'){
  //   //如果没有登录?对不起先去登录一下
  //   next('/login')
  // }
  console.log('1 beforeEach', to, from)  next()
})
// 时间触发比 全局前置守卫慢些
router.beforeResolve((to, from, next) => {
  // 全局解析守卫
  console.log('3 beforeResolve', to, from)
  next()
})

router.afterEach((to, from) => {
  // 全局后置守卫、钩子
  console.log('4 afterEach', to, from)
})
```

#### 路由独立守卫 router.js

```js
{
  path: '/a',
  name: 'pageA',
  components:{
    default: pageA,
    ppp: Test
  },
  beforeEnter: (to,from,next)=>{
    console.log('2 beforeEnter',to,from)
    next()
  },
},
```

#### 组件内守卫 xxx.vue

```js
export default {
  beforeRouteEnter(to, from, next) {
    //这里 拿不到this
    // 路由跳转，使用此组件时触发
    console.log("beforeRouteEnter", to, from);
    next();
  },
  beforeRouteUpdate(to, from, next) {
    //可以获取 this
    // /a/123 /a/456  当 组件被复用时，触发此方法
    console.log("beforeRouteUpdate", to, from);
    next();
  },
  beforeRouteLeave(to, from, next) {
    //可以获取this
    //路由跳转，不适用此组件时触发
    console.log("beforeRouteLeave", to, from);
    next();
  },
};
```

## 自定义指令有 5 个生命周期(也叫作钩子函数)

1. bind 只调用一次，指令第一次绑定到元素时候调用，用这个钩子可以定义一个绑定时执行一次的初始化动作。

2. inserted:被绑定的元素插入父节点的时候调用(父节点存在即可调用，不必存在 document 中)

3. update: 被绑定于元素所在模板更新时调用，而且无论绑定值是否有变化，通过比较更新前后的绑定值，忽略不必要的模板更新

4. componentUpdate :被绑定的元素所在模板完成一次更新更新周期的时候调用

5. unbind: 只调用一次，指令与元素解绑的时候调用

```js
Vue.directive("hello",{
  bind:function(el,bingind,vnode){
    //只调用一次，指令第一次绑定到元素时候调用，
    //用这个钩子可以定义一个绑定时执行一次的初始化动作。
    el.style["color"] = bingind.value;
    console.log("1-bind");
  },
  inserted:function(){
    //被绑定的元素插入父节点的时候调用(父节点存在即可调用，不必存在document中)
    console.log("2-inserted");
  },
  update:function(){
    //被绑定于元素所在模板更新时调用，而且无论绑定值是否有变化
    //通过比较更新前后的绑定值，忽略不必要的模板更新
    console.log("3-update");
  },
  componentUpdated:function(){
    //被绑定元素所在模板完成一次更新周期时调用
    console.log('4 - componentUpdated');
  },
  unbind:function(){
    //只调用一次，指令与元素解绑时调用。
    console.log('5 - unbind');
  }
})
```

### keep-alive的生命周期

- 初次进入时：created > mounted > activated；退出后触发 deactivated
- 再次进入：会触发 activated；事件挂载的方法等，只执行一次的放在 mounted 中；组件每次进去执行的方法放在 activated 中
