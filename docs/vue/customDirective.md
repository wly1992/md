# 自定义指令

在 Vue，除了核心功能默认内置的指令 ( v-model 和 v-show )，Vue 也允许注册自定义指令。它的作用价值在于当开发人员在某些场景下需要对普通 DOM 元素进行操作。

当页面加载时，该元素将获得焦点 (注意：autofocus 在移动版 Safari 上不工作)。事实上，只要你在打开这个页面后还没点击过任何内容，这个输入框就应当还是处于聚焦状态。现在让我们用指令来实现这个功能：

### 全局注册和局部注册

Vue自定义指令有全局注册和局部注册两种方式。先来看看注册全局指令的方式，通过 Vue.directive( id, [definition] ) 方式注册全局指令。然后在入口文件中进行 Vue.use() 调用。

```js
// 注册一个全局自定义指令 `v-focus`
Vue.directive('focus', {
  // 当被绑定的元素插入到 DOM 中时……
  inserted: function (el) {
    // 聚焦元素
    el.focus()
  }
})
```

如果想注册局部指令，组件中也接受一个 directives 的选项：

```js
// 注册一个局部自定义指令 `v-focus`
directives: {
  focus: {
    // 指令的定义
    inserted: function (el) {
      el.focus()
    }
  }
}
```



### 批量注册指令

1. 批量注册指令，新建 directives/index.js 文件

```js
import copy from './copy'
import longpress from './longpress'
// 自定义指令
const directives = {
  copy,
  longpress,
}
 
export default {
  install(Vue) {
    Object.keys(directives).forEach((key) => {
      Vue.directive(key, directives[key])
    })
  },
}
```

2. 在 main.js 引入并调用

```js
import Vue from 'vue'
import Directives from './JS/directives'
Vue.use(Directives)
```

### 钩子函数

- bind: 只调用一次，指令第一次绑定到元素时调用，可以定义一个在绑定时执行一次的初始化动作。

- inserted: 被绑定元素插入父节点时调用（父节点存在即可调用，不必存在于 document 中）。

- update: 被绑定元素所在的模板更新时调用，而不论绑定值是否变化。通过比较更新前后的绑定值。

- componentUpdated: 被绑定元素所在模板完成一次更新周期时调用。

- unbind: 只调用一次， 指令与元素解绑时调用。

### 几个实用的 Vue 自定义指令

1. 复制粘贴指令 v-copy  需求：实现一键复制文本内容，用于鼠标右键粘贴或者 ctrl+v。

```js
import { Message } from 'element-ui'
const copy = {
  bind(el, { value }) {
    el.$value = value
    el.handler = () => {
      if (!el.$value) {
        // 值为空的时候，给出提示。可根据项目UI仔细设计
        Message.error('无复制内容')
        return
      }
      // 动态创建 textarea 标签
      const textarea = document.createElement('textarea')
      // 将该 textarea 设为 readonly 防止 iOS 下自动唤起键盘，同时将 textarea 移出可视区域
      textarea.readOnly = 'readonly'
      textarea.style.position = 'absolute'
      textarea.style.left = '-9999px'
      // 将要 copy 的值赋给 textarea 标签的 value 属性
      textarea.value = el.$value
      // 将 textarea 插入到 body 中
      document.body.appendChild(textarea)
      // 选中值并复制
      textarea.select()
      const result = document.execCommand('Copy')
      if (result) {
        Message.success('复制成功') // 可根据项目UI仔细设计
      }
      document.body.removeChild(textarea)
    }
    // 绑定点击事件，就是所谓的一键 copy 啦
    el.addEventListener('click', el.handler)
  },
  // 当传进来的值更新的时候触发
  componentUpdated(el, { value }) {
    el.$value = value
  },
  // 指令与元素解绑的时候，移除事件绑定
  unbind(el) {
    el.removeEventListener('click', el.handler)
  },
}

export default copy

```
示例： 

```html
<template>
  <el-button type="primary"  v-copy="copyText">复制</el-button>
</template>
 
<script> 
export default {
  data() {
     return {
       copyText: 'https://www.baidu.com',
     }
   },
} 
</script>
```


2. v-debounce 需求：防止按钮在短时间内被多次点击，使用防抖函数限制规定时间内只能点击一次

```js
const debounce = {

  inserted: function (el, binding) {
    let timer;
    let flag = true;
    el.addEventListener('click', () => {
      if (timer) {
        clearTimeout(timer)
      }
      if(timerFlag){
        binding.value()
        flag = false
      }
      timer = setTimeout(() => {
        flag = true;
      }, 1000)
    })
  },
}

export default debounce

```

3. v-emoji

背景：开发中遇到的表单输入，往往会有对输入内容的限制，比如不能输入表情和特殊字符，只能输入数字或字母等。

```js
let findEle = (parent, type) => {
  return parent.tagName.toLowerCase() === type ? parent : parent.querySelector(type)
}
 
const trigger = (el, type) => {
  const e = document.createEvent('HTMLEvents')
  e.initEvent(type, true, true)
  el.dispatchEvent(e)
}
 
const emoji = {
  bind: function (el, binding, vnode) {
    // 正则规则可根据需求自定义
    var regRule = /[^u4E00-u9FA5|d|a-zA-Z|rns,.?!，。？！…—&$=()-+/*{}[]]|s/g
    let $inp = findEle(el, 'input')
    el.$inp = $inp
    $inp.handle = function () {
      let val = $inp.value
      $inp.value = val.replace(regRule, '')
 
      trigger($inp, 'input')
    }
    $inp.addEventListener('keyup', $inp.handle)
  },
  unbind: function (el) {
    el.$inp.removeEventListener('keyup', el.$inp.handle)
  },
}
 
export default emoji
```

4. v-lazy 需求：实现一个图片懒加载指令，只加载浏览器可见区域的图片。

```js
const lazy = {
  inserted: function (el, binding) {
    // 判断浏览器是否支持Intersection Observer API
    if ('IntersectionObserver' in window) {
      // 创建一个Intersection Observer实例
      const observer = new IntersectionObserver(function (entries) {
        entries.forEach(function (entry) {
          // 判断图片是否进入可视区域
          if (entry.isIntersecting) {
            // 加载图片
            el.setAttribute('src', binding.value);
            // 停止观察
            observer.unobserve(el);
          }
        });
      });
      // 开始观察图片元素
      observer.observe(el);
    } else {
      // 不支持Intersection Observer API的浏览器，直接加载图片
      el.setAttribute('src', binding.value);
    }
  }
}

export default lazy

```

```html
<img v-lazy="图片路径" />
```

5. v-waterMarker  需求：给整个页面添加背景水印

```js
function addWaterMarker(str, parentNode, font, textColor) {
  // 水印文字，父元素，字体，文字颜色
  var can = document.createElement('canvas')
  parentNode.appendChild(can)
  can.width = 200
  can.height = 150
  can.style.display = 'none'
  var cans = can.getContext('2d')
  cans.rotate((-20 * Math.PI) / 180)
  cans.font = font || '16px Microsoft JhengHei'
  cans.fillStyle = textColor || 'rgba(180, 180, 180, 0.3)'
  cans.textAlign = 'left'
  cans.textBaseline = 'Middle'
  cans.fillText(str, can.width / 10, can.height / 2)
  parentNode.style.backgroundImage = 'url(' + can.toDataURL('image/png') + ')'
}
 
const waterMarker = {
  bind: function (el, binding) {
    addWaterMarker(binding.value.text, el, binding.value.font, binding.value.textColor)
  },
}
 
export default waterMarker

```

6. v-drag 需求：实现一个拖拽指令，可在页面可视区域任意拖拽元素。

```js
const drag = {
  inserted: function (el) {
    el.style.cursor = 'move'
    el.style.position = 'absolute'
    el.onmousedown = function (e) {
      let disx = e.pageX - el.offsetLeft
      let disy = e.pageY - el.offsetTop
      document.onmousemove = function (e) {
        let x = e.pageX - disx
        let y = e.pageY - disy
        let maxX = document.body.clientWidth - parseInt(window.getComputedStyle(el).width)
        let maxY = document.body.clientHeight - parseInt(window.getComputedStyle(el).height)
        if (x < 0) {
          x = 0
        } else if (x > maxX) {
          x = maxX
        }
 
        if (y < 0) {
          y = 0
        } else if (y > maxY) {
          y = maxY
        }
 
        el.style.left = x + 'px'
        el.style.top = y + 'px'
      }
      document.onmouseup = function () {
        document.onmousemove = document.onmouseup = null
      }
    }
  },
}

export default drag

```

```html
<template>
  <div class="el-dialog" v-drag></div>
</template>
```