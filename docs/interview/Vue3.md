# Vue3 ⾯试题

## Proxy 和 Object.defineProperty 的区别是啥？

## vue3 相比较于 vue2，在编译阶段有哪些改进？

## Vue 中的 ref、toRef 和 toRefs 有什么区别？

## vue3 为什么要引入 Composition API ？

Vue 3 引入 Composition API 的原因主要是为了更好地解决以下几个问题：

1. 更好的代码组织和重用
   在 Vue 2 中，使用选项式 API（Options API）来定义组件的逻辑，通常将数据、方法、计算属性和生命周期钩子分开写在不同的配置对象中。当组件变得复杂时，不同功能的代码可能会散落在各个部分，难以维护和重用。

示例：

```js
// Vue 2 中使用 Options API
export default {
  data() {
    return {
      count: 0,
      message: "Hello World",
    };
  },
  computed: {
    doubleCount() {
      return this.count * 2;
    },
  },
  methods: {
    increment() {
      this.count++;
    },
  },
  created() {
    console.log(this.message);
  },
};
```

通过 Composition API，可以将相关功能的逻辑组织在一起，从而提高代码的可读性和可维护性。

```js
// Vue 3 中使用 Composition API
import { ref, computed, onMounted } from 'vue';

export default {
  setup() {
    const count = ref(0);
    const message = ref('Hello World');
    
    const doubleCount = computed(() => count.value * 2);
    
    const increment = () => {
      count.value++;
    };
    
    onMounted(() => {
      console.log(message.value);
    });
    
    return {
      count,
      doubleCount,
      increment,
      message
    };
  }
};
``` 

2. 更好的逻辑复用
在 Vue 2 中，逻辑复用主要通过 mixins 和 scoped slots 实现，但它们都有一些缺点，比如命名冲突和代码可读性差。

Composition API 通过组合函数（composable functions）来实现逻辑复用，这些函数可以在多个组件之间共享和复用逻辑，避免了 mixins 的缺点。

示例：

```js
// 一个组合函数，可以在多个组件中复用
import { ref, onMounted } from 'vue';

export function useMessage() {
  const message = ref('Hello World');
  
  onMounted(() => {
    console.log(message.value);
  });
  
  return {
    message
  };
}
```

在组件中使用：

```js
import { ref } from 'vue';
import { useMessage } from './useMessage';

export default {
  setup() {
    const count = ref(0);
    const { message } = useMessage();
    
    const increment = () => {
      count.value++;
    };
    
    return {
      count,
      message,
      increment
    };
  }
};
```

3. 更好的 TypeScript 支持
Composition API 天然地支持 TypeScript，使得类型推断和类型检查更为自然和方便。相比于 Options API，通过 Composition API 定义的逻辑更容易进行类型声明和类型推断，提升了开发体验。

示例：

```js
import { ref, computed } from 'vue';

export default {
  setup() {
    const count = ref<number>(0);
    const doubleCount = computed<number>(() => count.value * 2);
    
    const increment = (): void => {
      count.value++;
    };
    
    return {
      count,
      doubleCount,
      increment
    };
  }
};
```
4. 适应函数式编程趋势
Composition API 借鉴了函数式编程的思想，将逻辑封装成函数，使得代码更加简洁、模块化、可测试，同时也更符合现代 JavaScript 开发趋势。

总结
Vue 3 引入 Composition API 主要是为了提升代码组织和复用性、提供更好的 TypeScript 支持、适应函数式编程趋势，并且解决 Vue 2 中存在的一些问题。通过 Composition API，可以让组件逻辑更加清晰、灵活和易于维护。


## 怎么理解 Vue3 提供的 markRaw ？

在 Vue 3 中，markRaw 是一个用于告诉 Vue 的响应性系统不要对某个对象进行转换或追踪其响应性的函数。当你有一个对象，并且你确定你不需要它成为响应性对象时，你可以使用 markRaw 来标记它。这在一些场景中非常有用，比如当你需要集成一个第三方库或插件，并且这个库或插件的某些部分不需要是响应性的。

下面是一个详细的示例，展示了如何在 Vue 3 中使用 markRaw：

```js
import { reactive, markRaw, toRefs } from "vue";

// 创建一个普通的 JavaScript 对象
const rawObject = {
  name: "Raw Object",
  value: "This is a raw object and it will not be reactive.",
};

// 使用 markRaw 标记这个对象，使其保持原样
const markedRawObject = markRaw(rawObject);

// 创建一个响应性对象
const state = reactive({
  // 将标记过的 rawObject 放入响应性对象中
  markedRawObject,
  // 另一个普通的对象，它会被转换为响应性对象
  reactiveObject: {
    name: "Reactive Object",
    value: "This is a reactive object and it will track changes.",
  },
});

// 在组件中使用这些对象
export default {
  setup() {
    // 使用 toRefs 来解构响应性对象的属性，以便在模板中直接使用
    const { markedRawObject, reactiveObject } = toRefs(state);

    // 由于 markedRawObject 是被 markRaw 标记过的，修改它不会触发 Vue 的响应性系统
    markedRawObject.value.name = "Modified Raw Object"; // 这不会触发更新

    // 修改 reactiveObject 则会触发 Vue 的响应性更新
    reactiveObject.value.name = "Modified Reactive Object"; // 这会触发更新

    // 返回这些属性，以便在模板中使用
    return {
      markedRawObject,
      reactiveObject,
    };
  },
};
```

## Vue 中的 h 函数有什么用？

在 Vue 中，h 函数是一个帮助函数，用于创建虚拟 DOM 节点。1、h 函数用于创建虚拟 DOM 节点；2、它是 Vue 3 中组合式 API 的一部分；3、h 函数使得在渲染函数中直接创建 VNode 变得更加简洁和灵活。通过使用 h 函数，开发者可以更好地控制组件的渲染过程，特别是在需要动态生成复杂的 DOM 结构时。它在自定义渲染逻辑和优化性能方面提供了强大的支持。
