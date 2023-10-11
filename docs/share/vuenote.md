# Vue知识总结

### 8. vuex处理组件之间的数据交互

## Vuex

### 1.state与mapState 
初始值
```
state：{ //设置要访问的全局对象
    count: 0  //初始属性
} 
```
通过`this.$store.state.count`访问

### 2.getters与mapGetters 

#### getters

对state里的初始值做一些处理

就像计算属性一样，getter 的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生了改变才会被重新计算。

```
getters: {
    handleDate: state => {
        return state.count+1
    }
}
```
通过`this.$store.getters.handleDate`访问

Getter 也可以接受其他 getter 作为第二个参数：

```
getters: {
    handleDate: state => {
        return state.count+1
    },
    doneTodosCount: (state, getters) => {
        return getters.doneTodos.length
    }
}
```

> 注意，getter 在通过方法访问时，每次都会去进行调用，而不会缓存结果。

#### mapGetters 辅助函数

mapGetters 辅助函数仅仅是将 store 中的 getter 映射到局部计算属性：

```
import { mapGetters } from 'vuex'

export default {
  // ...
  computed: {
  // 使用对象展开运算符将 getter 混入 computed 对象中
    ...mapGetters([
      'doneTodosCount',
      'anotherGetter',
      // ...
    ])
  }
}
```
如果你想将一个 getter 属性另取一个名字，使用对象形式：

```
...mapGetters({
  // 把 `this.doneCount` 映射为 `this.$store.getters.doneTodosCount`
  doneCount: 'doneTodosCount'
})
```

### 3.mutation

更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。Vuex 中的 mutation 非常类似于事件：每个 mutation 都有一个字符串的 事件类型 (type) 和 一个 回调函数 (handler)。这个回调函数就是我们实际进行状态更改的地方，并且它会接受 state 作为第一个参数：

```
const store = new Vuex.Store({
  state: {
    count: 1
  },
  mutations: {
    increment (state) {
      // 变更状态
      state.count++
    }
  }
})
```

你不能直接调用一个 mutation handler。这个选项更像是事件注册：“当触发一个类型为 increment 的 mutation 时，调用此函数。”要唤醒一个 mutation handler，你需要以相应的 type 调用 store.commit 方法：

`this.$store.commit('increment')`

你可以向 store.commit 传入额外的参数，即 mutation 的 载荷（payload）：

```
mutations: {
  increment (state, n) {
    state.count += n
  }
}
```

`this.$store.commit('increment', 10)`

还可以这样提交

```
this.$store.commit({
  type: 'increment',
  amount: 10
})
```

> **Mutation 必须是同步函数**

#### mapMutations 
在组件中调用可以通过mapMutations辅助函数将组件中的 methods 映射为 store.commit 调用
```
import { mapMutations } from 'vuex'

export default {
  // ...
  methods: {
    ...mapMutations([
      'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`

      // `mapMutations` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
    ]),
    ...mapMutations({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
    })
  }
}
```

### 4.action 
Action 类似于 mutation，不同在于：

- Action 提交的是 mutation，而不是直接变更状态。
- Action 可以包含任意异步操作。

让我们来注册一个简单的 action：

```
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  },
  actions: {
    increment (context) {
      context.commit('increment')
    }
  }
})
```

Action 函数接受一个与 store 实例具有相同方法和属性的 context 对象，因此你可以调用 context.commit 提交一个 mutation，或者通过 context.state 和 context.getters 来获取 state 和 getters。当我们在之后介绍到 Modules 时，你就知道 context 对象为什么不是 store 实例本身了。

实践中，我们会经常用到 ES2015 的 参数解构 来简化代码（特别是我们需要调用 commit 很多次的时候）：

```
actions: {
  increment ({ commit }) {
    commit('increment')
  }
}
```

Action 通过 store.dispatch 方法触发：

`this.$store.dispatch('increment')`

乍一眼看上去感觉多此一举，我们直接分发 mutation 岂不更方便？实际上并非如此，还记得 mutation 必须同步执行这个限制么？Action 就不受约束！我们可以在 action 内部执行异步操作：

```
actions: {
  incrementAsync ({ commit }) {
    setTimeout(() => {
      commit('increment')
    }, 1000)
  }
}
```
Actions 支持同样的载荷方式和对象方式进行分发：

```
// 以载荷形式分发
store.dispatch('incrementAsync', {
  amount: 10
})

// 以对象形式分发
store.dispatch({
  type: 'incrementAsync',
  amount: 10
})
```

在组件中分发 Action

你在组件中使用 this.$store.dispatch('xxx') 分发 action，或者使用 mapActions 辅助函数将组件的 methods 映射为 store.dispatch 调用（需要先在根节点注入 store）：

```
import { mapActions } from 'vuex'

export default {
  // ...
  methods: {
    ...mapActions([
      'increment', // 将 `this.increment()` 映射为 `this.$store.dispatch('increment')`

      // `mapActions` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.dispatch('incrementBy', amount)`
    ]),
    ...mapActions({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.dispatch('increment')`
    })
  }
}
```
### 5.module

由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store 对象就有可能变得相当臃肿。

为了解决以上问题，Vuex 允许我们将 store 分割成模块（module）。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块——从上至下进行同样方式的分割：

```
const moduleA = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```

## 处理vue-cli3热加载过慢问题

```
npm install babel-plugin-dynamic-import-node --save-dev
```

在 .babelrc文件中添加

```
"env": {
    "development": {
      "plugins": ["dynamic-import-node"]
    }
}
```

## Vue双向绑定原理

## Vue es6技巧写法

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

## Vue项目优化

https://blog.csdn.net/qq_37939251/article/details/100031285


<h2>Vue diff算法原理</h2>

https://juejin.cn/post/6997965021401579556

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

<h2>Vuex</h2>

### 1.state与mapState 
初始值
```
state：{ //设置要访问的全局对象
    count: 0  //初始属性
} 
```
通过`this.$store.state.count`访问

### 2.getters与mapGetters 

#### getters

对state里的初始值做一些处理

就像计算属性一样，getter 的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生了改变才会被重新计算。

```
getters: {
    handleDate: state => {
        return state.count+1
    }
}
```
通过`this.$store.getters.handleDate`访问

Getter 也可以接受其他 getter 作为第二个参数：

```
getters: {
    handleDate: state => {
        return state.count+1
    },
    doneTodosCount: (state, getters) => {
        return getters.doneTodos.length
    }
}
```

> 注意，getter 在通过方法访问时，每次都会去进行调用，而不会缓存结果。

#### mapGetters 辅助函数

mapGetters 辅助函数仅仅是将 store 中的 getter 映射到局部计算属性：

```
import { mapGetters } from 'vuex'

export default {
  // ...
  computed: {
  // 使用对象展开运算符将 getter 混入 computed 对象中
    ...mapGetters([
      'doneTodosCount',
      'anotherGetter',
      // ...
    ])
  }
}
```
如果你想将一个 getter 属性另取一个名字，使用对象形式：

```
...mapGetters({
  // 把 `this.doneCount` 映射为 `this.$store.getters.doneTodosCount`
  doneCount: 'doneTodosCount'
})
```

### 3.mutation

更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。Vuex 中的 mutation 非常类似于事件：每个 mutation 都有一个字符串的 事件类型 (type) 和 一个 回调函数 (handler)。这个回调函数就是我们实际进行状态更改的地方，并且它会接受 state 作为第一个参数：

```
const store = new Vuex.Store({
  state: {
    count: 1
  },
  mutations: {
    increment (state) {
      // 变更状态
      state.count++
    }
  }
})
```

你不能直接调用一个 mutation handler。这个选项更像是事件注册：“当触发一个类型为 increment 的 mutation 时，调用此函数。”要唤醒一个 mutation handler，你需要以相应的 type 调用 store.commit 方法：

`this.$store.commit('increment')`

你可以向 store.commit 传入额外的参数，即 mutation 的 载荷（payload）：

```
mutations: {
  increment (state, n) {
    state.count += n
  }
}
```

`this.$store.commit('increment', 10)`

还可以这样提交

```
this.$store.commit({
  type: 'increment',
  amount: 10
})
```

> **Mutation 必须是同步函数**

#### mapMutations 
在组件中调用可以通过mapMutations辅助函数将组件中的 methods 映射为 store.commit 调用
```
import { mapMutations } from 'vuex'

export default {
  // ...
  methods: {
    ...mapMutations([
      'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`

      // `mapMutations` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
    ]),
    ...mapMutations({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
    })
  }
}
```

### 4.action 
Action 类似于 mutation，不同在于：

- Action 提交的是 mutation，而不是直接变更状态。
- Action 可以包含任意异步操作。

让我们来注册一个简单的 action：

```
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  },
  actions: {
    increment (context) {
      context.commit('increment')
    }
  }
})
```

Action 函数接受一个与 store 实例具有相同方法和属性的 context 对象，因此你可以调用 context.commit 提交一个 mutation，或者通过 context.state 和 context.getters 来获取 state 和 getters。当我们在之后介绍到 Modules 时，你就知道 context 对象为什么不是 store 实例本身了。

实践中，我们会经常用到 ES2015 的 参数解构 来简化代码（特别是我们需要调用 commit 很多次的时候）：

```
actions: {
  increment ({ commit }) {
    commit('increment')
  }
}
```

Action 通过 store.dispatch 方法触发：

`this.$store.dispatch('increment')`

乍一眼看上去感觉多此一举，我们直接分发 mutation 岂不更方便？实际上并非如此，还记得 mutation 必须同步执行这个限制么？Action 就不受约束！我们可以在 action 内部执行异步操作：

```
actions: {
  incrementAsync ({ commit }) {
    setTimeout(() => {
      commit('increment')
    }, 1000)
  }
}
```
Actions 支持同样的载荷方式和对象方式进行分发：

```
// 以载荷形式分发
store.dispatch('incrementAsync', {
  amount: 10
})

// 以对象形式分发
store.dispatch({
  type: 'incrementAsync',
  amount: 10
})
```

在组件中分发 Action

你在组件中使用 this.$store.dispatch('xxx') 分发 action，或者使用 mapActions 辅助函数将组件的 methods 映射为 store.dispatch 调用（需要先在根节点注入 store）：

```
import { mapActions } from 'vuex'

export default {
  // ...
  methods: {
    ...mapActions([
      'increment', // 将 `this.increment()` 映射为 `this.$store.dispatch('increment')`

      // `mapActions` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.dispatch('incrementBy', amount)`
    ]),
    ...mapActions({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.dispatch('increment')`
    })
  }
}
```
### 5.module

由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store 对象就有可能变得相当臃肿。

为了解决以上问题，Vuex 允许我们将 store 分割成模块（module）。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块——从上至下进行同样方式的分割：

```
const moduleA = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```