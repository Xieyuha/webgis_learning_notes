# Vue 学习笔记

## Vue 3 简介

### 创建 Vue 工程

### 基于 Vite 创建 Vue3 工程

- Vite 项目中，`index.html` 是项目的入口文件，在项目最外层。
- 加载 `index.html` 后，Vite 解析 `<script type="module" src="/src/main.ts"></script>`，指向 JavaScript 入口。
- Vue 3 中通过 `createApp` 函数创建一个应用实例。

### Vue 3 核心语法

#### Options API 与 Composition API

- Composition API：
  - 基于函数的 API。
  - 通过 `setup` 函数配置组件。
  - 数据直接声明，方法使用函数声明。

#### setup

- 内部没有 `this`，Vue 3 弱化了 `this` 的概念。
- 返回值会暴露给模板使用；可用简写，渲染模板时直接使用。
- 在 `beforeCreate` 和 `created` 之前执行。
- 当并列使用 `data` 和 `setup` 时，`data` 可以读取 `setup` 返回的数据，但 `setup` 不能读取 `data` 中的数据。

#### ref

- 创建基本数据类型的响应式数据。
- 通过 `.value` 读取和修改数据（`.value` 才是响应式数据）。
- TypeScript 示例：`const name: string = ref('sam').value`。

#### reactive

- 深层次包裹源对象，创建响应式对象。
- 缺点：重新分配对象时（整体修改），响应式失效。

#### 使用原则

1. 若需要一个基本类型的响应式数据，必须使用 `ref`。
2. 若需要一个响应式对象，层级不深，`ref`、`reactive` 都可以。
3. 若需要一个响应式对象，且层级较深，推荐使用 `reactive`。

#### toRefs 和 toRef

1. `toRefs`：将 `reactive` 创建的响应式对象，转换为包含 `ref` 的对象。
2. `toRef`：将 `reactive` 创建的响应式对象中的某个属性，转换为 `ref`。

说明：把 `reactive` 对象的值拿出来并“带上响应式”。

#### computed

- 与 Vue 2 中的 `computed` 类似，基于函数创建计算属性。
- 返回值 `return` 是 `ref`（响应式）的。

```ts
computed(() => {
  return xxx
})

computed({
  get: () => {
    return xxx
  },
  set: (value) => {
    // ...
  }
})
```

#### watch

- 监视数据的变化，特别针对响应式数据，建议写成函数形式。
- 监视 `ref` 响应式数据，目标写数据本身（不要写 `.value`）。
- 监视 `reactive` 响应式对象，默认隐式创建深度监视。
- 也可监视 getter 函数返回值（监视具体值的变化，适合基本类型）。
- 或者监视一个包含上述多种形式的数组。

```ts
const count = reactive({
  value: 0,
  index: 0,
})

// getter 函数，监视具体值
const stopWatch = watch(
  () => count.value,
  (newVal, oldVal) => {
    // 对象完全改变，oldVal 改变；否则为同一对象（非立即执行时）
    // 监测指针变化，内容变化不触发
    // ...
    // watch 返回一个函数，用于停止监视
    if (newVal === 10) {
      stopWatch() // 停止监视
    }
  },
  // 配置对象
  {
    immediate: true, // 立即执行一次回调，oldVal 为 undefined
    deep: true, // 深度监视
  }
)
```

##### 情况一

监视 `ref` 定义的【基本类型】数据：直接写数据名即可，监视的是其 `value` 值的改变。

##### 情况二

监视 `ref` 定义的【对象类型】数据：直接写数据名，监视的是对象的【地址值】。若想监视对象内部的数据，要手动开启深度监视。

> 注意：
>
> - 若修改的是 `ref` 定义的对象中的属性，`newValue` 和 `oldValue` 都是新值，因为它们是同一个对象。
> - 若修改整个 `ref` 定义的对象，`newValue` 是新值，`oldValue` 是旧值，因为不是同一个对象了。

##### 情况三

监视 `reactive` 定义的【对象类型】数据，且默认开启了深度监视。

##### 情况四

监视 `ref` 或 `reactive` 定义的【对象类型】数据中的某个属性，注意点如下：

1. 若该属性值不是【对象类型】，需要写成函数形式。
2. 若该属性值依然是【对象类型】，可直接写，也可写成函数，建议写成函数。

结论：监视的是对象里的属性，最好写成函数式；若监视对象本身，则监视的是地址值。需要关注对象内部变化时，手动开启深度监视。

##### 情况五

监视上述的多个数据：`[() => data1, data2.value, ...]`

#### watchEffect

立即执行，并且自动收集依赖（不需要明确指出监视的数据）。

#### 标签的 ref 属性

ref 属性用于注册模板引用

- 用于普通`DOM`标签上，获得的是`DOM`节点
- 用于组件标签上，获得`组件实例`对象

```ts
<template>
  <div>
	<h2> ref="myTitle">标题</h2>
  </div>
</template>
<script lang="ts">
	import { ref,defineExpose } from 'vue';
	let myTitle = ref(null);
	defineExpose({
	  myTitle})
</script>
```

#### props的使用

接收a，同时保存props+限制类型+?表示可选+指定默认值

```ts
<Person a="haha"> </Person>
import { defineProps,withDefaults } from 'vue'
const props=defineProps<{a?:string}>()
```

#### 生命周期

创建：setup
挂载：
- onBeforeMount(()=>{log('挂载前')})
- 先子后父

#### 自定义hooks

hooks/useSum.ts

- 什么是`hook`？—— 本质是一个函数，把`setup`函数中使用的`Composition API`进行了封装，类似于`vue2.x`中的`mixin`。

- 自定义`hook`的优势：复用代码, 让`setup`中的逻辑更清楚易懂。

### 路由

#### 路由基本理解

- 是一种key-value的对应关系
  - key:路径
  - value:组件
- route:路由
- router:路由器，一个表，管理所有路由
- router-link:页面不刷新跳转，组件是对a标签的渲染，根据是否匹配自动添加类名
- router-view:展示对应组件，相当于容器


#### 基本切换效果

- 路由配置文件代码如下：
  ```ts
    import {createRouter,createWebHistory} from 'vue-router'
    import Home from '@/pages/Home.vue'
    import News from '@/pages/News.vue'
    import About from '@/pages/About.vue'

    const router = createRouter({
    	history:createWebHistory(),
    	routes:[
    		{
    			path:'/home',
    			component:Home
    		},
    		{
    			path:'/about',
    			component:About
    		}
    	]
    })
    export default router
  ```

* `main.ts`代码如下：

  ```ts
  import router from './router/index'
  app.use(router)
  
  app.mount('#app')
  ```

- `App.vue`代码如下

  ```ts
  <template>
    <div class="app">
      <h2 class="title">Vue路由测试</h2>
      <!-- 导航区 -->
      <div class="navigate">
        <RouterLink to="/home" active-class="active">首页</RouterLink>
        <RouterLink to="/news" active-class="active">新闻</RouterLink>
        <RouterLink to="/about" active-class="active">关于</RouterLink>
      </div>
      <!-- 展示区 -->
      <div class="main-content">
        <RouterView></RouterView>
      </div>
    </div>
  </template>
  
  <script lang="ts" setup name="App">
    import {RouterLink,RouterView} from 'vue-router'  
  </script>
  ```

#### 两个注意点
1. 路由组件通常放在`pages`或`views`文件夹下，一般组件放在`components`文件夹下
2. 通过点击导航，视觉效果上是页面跳转了，但实际上是组件切换了，页面并没有刷新，消失的组件会被销毁，新的组件会被创建并挂载

#### 路由工作模式

- history模式：利用`HTML5`的`History API`，通过`pushState`和`replaceState`方法，改变浏览器地址栏的路径而不会引起页面刷新
- 优点：url美观，没有`#`符号
- 缺点：需要后端支持，刷新页面会出现`404`错误

  ```ts
  const router = createRouter({
    history:createWebHistory(),
    routes:[...]
  })
  ```

- hash模式：利用浏览器对`#`后面内容不发送给服务器的特性，通过监听`hashchange`事件，实现路由切换
- 优点：兼容性好，不需要后端支持
- 缺点：url中会出现`#`符号，在`SEO`(搜索)优化方面不如`history`模式
  
  ```ts
  const router = createRouter({
    history:createWebHashHistory(),
    routes:[...]
  })
  ```

#### to的两种写法

- 字符串写法：`to="/home"`
- 对象写法：

  ```ts
    <RouterLink :to="{path:'/home'}"></RouterLink>
    <RouterLink :to="{name:'zhuye'}"></RouterLink>
  ```

#### router-link 与 router-view 组件
- router-link:只是路由导航的触发器
- router-view:是具体的渲染出口
- 两者通过`router`实例关联在一起
- 想要一对多展示，命名视图、嵌套路由

  ```ts
    import Header from '@/components/Header.vue'
    <router-view name="header" />
    <router-view name="main" />
  ```
  
  ```ts
    {
      path: '/',
      components: {
        header: Header,
        main: LayoutMain,
      },
      children: [
      {
        path: '/',
        component: ProfileContent
      },
    ]
    }
  ```

#### query参数与params参数
- query参数：通过`?`传递参数，参数以键值对形式存在，多个参数用`&`连接
  - 获取方式：`this.$route.query.参数名`或`useRoute().query.参数名`
  - 示例：`/home?name=sam&age=18`

## Vue 2 基础

### 初识 Vue

1. 先创建一个 Vue 实例，且要传入配置对象。
2. root 容器内代码依然符合 HTML 规范，只不过多了 Vue 语法。
3. root 容器内代码会被 Vue 接管，被称为 Vue 模板。
4. 容器和实例是一一对应的。
5. 真实开发只有一个 Vue 实例，配合组件使用。
6. `{{}}` 称为插值表达式，里面可以写 JS 表达式，可以读取 `data` 中的数据。
7. 一旦 `data` 中数据发生变化，页面中用到该数据的模板位置会自动更新。

### Vue 模板语法

1. 插值语法：`{{}}`、`v-text`、`v-html`
   - 功能：用于解析标签体内容。
2. 指令语法：`v-xxx`
   - `v-bind`：动态绑定属性。
   - `v-on`：绑定事件。
   - `v-if`：条件渲染。
   - `v-for`：列表渲染。
   - `v-model`：双向数据绑定。
   - ...
   - 功能：用于解析标签（包括属性、内容、事件）。

### Vue 中有 2 种数据绑定的方式

1. 单向绑定（`v-bind`）：数据只能从 `data` 到页面。
2. 双向绑定（`v-model`）：数据不仅能从 `data` 到页面，还可以从页面流向 `data`。
   - 备注：
     1) 双向绑定一般用于表单类元素（如：`input`、`select` 等）。
     2) `v-model:value` 可简写为 `v-model`，因为其默认收集的就是 `value` 值。

### el 和 data 的补充说明

1. 创建 Vue 实例时，`el` 和 `data` 可以不写。
2. 如果不写 `el`，则需要手动调用 `$mount` 方法挂载。
3. `data` 有两种写法：对象式和函数式。
   - 对象式：适用于单个实例。
   - 函数式：适用于组件（组件可复用，必须每个组件实例都有自己独立的数据）。
     - 注意：`data` 函数中必须 `return` 一个对象。
     - 注意：不要使用箭头函数，否则 `this` 将指向 `window`。

### MVVM 模型

MVVM 模型：

1. M：模型（Model）——`data` 中的数据。
2. V：视图（View）——模板代码。
3. VM：视图模型（ViewModel）——Vue 实例。

观察发现：

1. `data` 中所有的属性，最后都出现在了 `vm` 身上。
2. `vm` 身上所有的属性及 Vue 原型上所有属性，在 Vue 模板中都可以直接使用。

### 数据代理

#### JS 中的数据代理

通过一个对象代理对另一个对象的属性操作：

```js
Object.defineProperty(person, 'name', {
  value: 'sam',
  enumerable: true, // 控制属性是否可以枚举，默认 false
  writable: true, // 控制属性是否可改写，默认 false
  configurable: true, // 控制属性是否可删除，默认 false
})
```

#### Vue 中的数据代理

- 通过 `vm` 对象来代理 `data` 对象中的属性，方便操作 `data` 中的数据。
- 通过 `Object.defineProperty()` 把 `data` 对象中所有属性添加到 `vm` 上。
- 为每个添加到 `vm` 上的属性都添加 getter 和 setter。
- 在 getter/setter 内部去操作 `data` 中的对应属性。

```
vm {
  data  ->  _data  ->  name
}
  读属性      添加响应式
```

### 事件处理

- 使用 `v-on:xxx` 或 `@xxx` 绑定事件，其中 `xxx` 是事件名。
- 事件的回调需要配置在 `methods` 对象中，最终会在 `vm` 上。
- `methods` 中的函数不要用箭头函数，否则 `this` 就不是 `vm` 了。
- `methods` 中的函数由 Vue 管理，`this` 的指向是 `vm` 或组件实例对象。
- `@click="demo"` 和 `@click="demo($event)"` 效果一致，但后者可以传参（`$event` 是事件对象）。

### 事件修饰符

- 可连续书写：
  1. `prevent`：阻止默认事件。
  2. `stop`：阻止事件冒泡。
  3. `once`：事件只触发一次。
  4. `capture`：使用事件的捕获模式（默认冒泡）。
     - 捕获阶段：由外向内。
     - 冒泡阶段：由内向外。
  5. `self`：只有 `event.target` 是当前元素时才触发事件。
  6. `passive`：事件的默认行为立即执行，无需等待回调执行完毕（移动端如 `@wheel` 常用）。

### 键盘事件

1. Vue 常用按键别名：
   - 回车 => `enter`
   - 删除 => `delete`（捕获“删除”和“退格”键）
   - 退出 => `esc`
   - 空格 => `space`
   - 换行 => `tab`（特殊，必须配合 `keydown` 使用）
   - 上 => `up`；下 => `down`；左 => `left`；右 => `right`
2. 未提供别名的按键，可使用原始 `key` 值，注意转为 `kebab-case`。
3. 系统修饰键（用法特殊）：`ctrl`、`alt`、`shift`、`meta`
   - 配合 `keyup`：按下修饰键的同时再按下其他键，随后释放其他键，事件才被触发。
   - 配合 `keydown`：正常触发事件。
4. 也可以使用 `keyCode` 指定具体按键（不推荐）。
5. 可自定义：`Vue.config.keyCodes.自定义键名 = 键码`。

### 计算属性 computed

计算属性：

1. 定义：要用的属性不存在，要通过已有属性计算得来。
2. 原理：底层借助 `Object.defineProperty` 的 getter/setter。
3. get 函数执行时机：
   - 初次读取时执行一次。
   - 当依赖的数据发生改变时再次调用。
4. 优势：与 `methods` 相比，内部有缓存机制（复用），效率更高，调试更方便。
5. 备注：
   - 计算属性最终会出现在 `vm` 上，直接读取即可。
   - 若需要“可写”计算属性，必须提供 `set`，且 `set` 中应引起依赖数据的改变。

### 监视属性 watch

1. 当被监视的属性变化时，回调函数自动调用，进行相关操作。
2. 监视的属性必须存在，才能进行监视！
3. 写法：
   - 在 `new Vue` 时传入 `watch` 配置。
   - 通过 `vm.$watch` 监视。

### 深度监视（侦听）

1. Vue 的 `watch` 默认不监测对象内部值的改变（仅一层）。
2. 配置 `deep: true` 可以监测对象内部值改变（多层）。

备注：

- Vue 自身可以监测对象内部值的改变，但 Vue 提供的 `watch` 默认不可以。
- 使用 `watch` 时根据数据结构决定是否采用深度监视。

### computed 和 watch 的区别

1. `computed` 能完成的功能，`watch` 基本都可以完成。
2. `watch` 能完成的功能，`computed` 不一定能完成，例如：`watch` 可以进行异步操作。

两个重要的小原则：

1. 被 Vue 管理的函数，最好写成普通函数，这样 `this` 的指向才是 `vm` 或组件实例对象。
2. 不被 Vue 管理的函数（定时器、Ajax 回调、Promise 回调等异步操作），最好写成箭头函数，这样 `this` 的指向仍是 `vm` 或组件实例对象。

### 样式绑定

```html
<div id="root">
  <!-- 绑定 class 样式——字符串写法，适用于：样式的类名不确定，需要动态指定 -->
  <div class="basic" :class="mood" @click="changeMood">1</div>

  <!-- 绑定 class 样式——数组写法，适用于：要绑定的样式个数不确定、名字也不确定 -->
  <div class="basic" :class="classArr">2</div>

  <!-- 绑定 class 样式——对象写法，适用于：要绑定的样式个数确定、名字也确定，但要动态决定用不用 -->
  <div class="basic" :class="classObj" @click="changeObj">3</div>
</div>
```

#### 字体

`:style="{ fontSize: fsize + 'px' }"`

提示：`font-size` 在绑定对象中需写为小驼峰的 `fontSize`。

### 条件渲染

1. `v-if`
   - 写法：
     1) `v-if="表达式"`
     2) `v-else-if="表达式"`
     3) `v-else`
   - 适用于：切换频率较低的场景。
   - 特点：不展示的 DOM 元素会被直接移除。
   - 注意：`v-if` 可以和 `v-else-if`、`v-else` 一起使用，但要求结构不能被打断。
2. `v-show`
   - 写法：`v-show="表达式"`
   - 适用于：切换频率较高的场景。
   - 特点：不展示的 DOM 元素未被移除，仅使用样式隐藏。
3. 备注：使用 `v-if` 时，元素可能无法获取到；使用 `v-show` 一定可以获取到。

总结：

- 频繁切换显示用 `v-show`。
- 条件复杂且切换不频繁（需销毁/重建、释放资源）用 `v-if`。

### 列表渲染

- `v-for` 指令：
  1. 用于展示列表数据。
  2. 语法：`v-for="(item, index) in 数据源" :key="唯一标识符"`（不写 `key` 时默认使用 `index`）。
  3. 可遍历：数组、对象、字符串、指定次数。
  4. 注意：`key` 的值必须唯一且稳定，推荐使用数据的 `id` 属性，而不是 `index`。

`key` 的作用：

1. 作为虚拟 DOM diff 算法的依据，提高渲染效率。
2. 进行虚拟 DOM 比较时：
   - 虚拟 DOM 不同，则新的覆盖旧的。
   - 虚拟 DOM 一样，则复用真实元素。

#### Vue 的 Diff 算法

- 核心目标：减少对真实 DOM 的操作，尽可能找出可复用的点，只移动、更新、销毁必要的部分。
- 旧列表与新列表对比：利用指针从两端开始比较。
  - `key` 相同：复用并更新内容。
    - 可能问题：`key` 相同使 Vue 认为是相同的旧节点，但 `input` 的 `value` 值实际是浏览器内部的值，可能导致错误匹配。
  - `key` 不同：认为是不同节点，此时 Vue 执行：销毁旧节点、创建新节点、插入到相应位置。

加分题：React / Vue 中 `key` 的作用（内部原理）：

1. 虚拟 DOM 中 `key` 的作用：用于在新旧虚拟 DOM 对比时，快速定位可复用节点。
2. 对比规则：
   - 找到相同 `key`：
     1) 若虚拟 DOM 中内容没变，直接使用之前的真实 DOM。
     2) 若内容变化，生成新的真实 DOM，替换页面中之前的真实 DOM。
   - 未找到相同 `key`：创建新的真实 DOM 并渲染到页面。
3. 用 `index` 作为 `key` 的问题：
   - 逆序添加/删除等破坏顺序操作，会产生没有必要的真实 DOM 更新（效率低）。
   - 若结构中包含输入类 DOM，可能产生错误 DOM 更新（界面有问题）。
4. 选择 `key`：
   - 最好使用数据的唯一标识，如 `id`、手机号、身份证号、学号等。
   - 若仅用于展示、且无逆序操作，使用 `index` 作为 `key` 也可。

#### 监测数据变化的原理

1. Vue 通过 `Object.defineProperty()` 给 `data` 中每个属性添加 getter 和 setter。
2. 读取属性时触发 getter，进行依赖收集。
3. 修改属性时触发 setter，通知所有依赖进行更新。

`data` 数据的响应式原理：

1. 数据劫持（加工）：通过 `Object.defineProperty()` 给 `data` 中每个属性添加 getter 和 setter（通过 `observe` 函数间接处理 `data`，避免直接递归造成内存泄漏）。
2. 依赖收集：在 getter 中收集依赖，记录哪些组件使用了该属性。
3. 派发更新：在 setter 中通知所有依赖进行更新。

#### `Vue.set()`

- 对象上 `undefined` 的属性，Vue 默认不显示；可用：

```js
Vue.set(obj, 'newProp', value)
// 或者
vm.$set(obj, 'newProp', value)
```

局限性：

1. 只能给对象添加响应式属性，不能给数组添加响应式属性。
2. 只能添加一层响应式，不能添加深层响应式（只在 `data` 的第一层属性上有效）。

#### 响应原理（数组与对象）

- 对象：给属性直接加 getter 和 setter。
- 数组：通过修改原型链方法，拦截会改变数组内容的方法。
  - 包裹 7 个变异方法：`push`、`pop`、`shift`、`unshift`、`splice`、`sort`、`reverse`。
  - 注意：直接通过索引修改数组不会触发视图更新。

### 收集表单数据

- 若：`<input type="text"/>`，则 `v-model` 收集的是 `value` 值。
- 若：`<input type="radio"/>`，`v-model` 收集的是 `value` 值，且需给标签配置 `value` 值。
- 若：`<input type="checkbox"/>`
  1) 未配置 `value`，收集的是 `checked`（布尔）。
  2) 配置了 `value`：
     - `v-model` 初始值非数组：收集 `checked`（布尔）。
     - `v-model` 初始值是数组：收集由 `value` 组成的数组。

`v-model` 的三个修饰符：

- `lazy`：失去焦点再收集数据。
- `number`：将输入字符串转为有效数字。
- `trim`：过滤输入首尾空格。

### 内置指令

- `v-text`：设置元素的 `textContent` 属性。
- `v-html`：设置元素的 `innerHTML` 属性。
  - 注意：使用 `v-html` 时，务必确保内容安全，避免 XSS 攻击。
- `v-show`：控制元素的 `display` 样式属性。
- `v-cloak`：解决闪烁问题，配合 CSS 使用，只有在元素被编译完成后才会显示。

```css
[v-cloak] {
  display: none;
}
```

- `v-once`：只渲染元素和组件一次，之后数据变化不再更新。
- `v-pre`：让 Vue 跳过该元素和子元素的编译过程（用于静态内容较多的场景以提高性能）。

### 生命周期

1. 又名：生命周期回调函数、生命周期函数、生命周期钩子。
2. 定义：在 Vue 实例生命周期的不同阶段自动调用的函数。
3. 生命周期函数的名字不可更改，但函数的具体内容由开发者编写。
4. 生命周期函数中的 `this` 指向 `vm` 或组件实例对象。

创建、挂载阶段：

- `beforeCreate`：实例初始化之后，数据观测（data observer）和事件配置（event/watcher）之前调用。
- `created`：实例创建完成后立即调用。此时已完成数据观测、属性与方法的运算、watch/event 的回调，但尚未开始挂载，`$el` 不可见。
- `beforeMount`：在挂载开始之前调用，相关的 `render` 函数首次被调用。
- `mounted`：`el` 被新创建的 `vm.$el` 替换，并挂载到实例上去之后调用。

更新阶段：

- `beforeUpdate`：数据更新时调用，发生在虚拟 DOM 重新渲染和打补丁之前；此处更改状态不会触发额外渲染。
- `updated`：由于数据更改导致的虚拟 DOM 重新渲染和打补丁之后调用；此时可执行依赖 DOM 的操作，但尽量避免再次改动状态以免产生更新循环。

销毁阶段：

- `beforeDestroy`：实例销毁之前调用；关闭定时器、取消订阅等清理工作宜在此进行。
- `destroyed`：实例销毁后调用；所有绑定解开、事件监听移除、子实例均被销毁。

常用钩子：

1. `mounted`：发送 Ajax 请求（也可在 `created`）、启动定时器、绑定自定义事件、订阅消息等初始化操作。
2. `beforeDestroy`：清除定时器、解绑自定义事件、取消订阅消息等收尾工作。

关于销毁 Vue 实例：

1. 销毁后借助 Vue 开发者工具看不到任何信息。
2. 销毁后自定义事件会失效，但原生 DOM 事件依然有效。
3. 一般不会在 `beforeDestroy` 中操作数据，因为即便操作也不会再触发更新流程。

