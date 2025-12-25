# 一些细节
## watch
返回的是停止监听函数
# Vue3 响应式原理

## 1. Vue2 vs Vue3
- Vue2: defineProperty 逐属性劫持，无法劫持新增/删除属性
- Vue3: Proxy 对象级劫持，功能更强、覆盖更广

## 2. 两条响应式路线
### ref
- 基于 getter/setter
- 适用于基本类型
- 包对象时内部转换为 reactive

### reactive
- 基于 Proxy + Reflect
- 管整个对象和数组
- 深度响应式也是访问时递归代理

## 3. 依赖收集结构
targetMap (WeakMap)
  target → depsMap (Map)
    key → dep (Set of effects)

## 4. track / trigger
- track: 读取属性时收集依赖
- trigger: 修改属性时触发依赖更新

## 5. 为什么这样设计？
- WeakMap: 自动释放无引用的响应式对象
- Map: 根据 key 精准区分依赖
- Set: 去重 effect

## 6. 总结
ref：单值、getter/setter 实现响应式
reactive：对象、Proxy 实现响应式
Vue3 的响应式核心：track + trigger

### 底层原理（补充）

- ref：通过 ES6 getter/setter 劫持 `.value` 实现响应式
- reactive：通过 Proxy + Reflect 劫持对象属性读写
  - Proxy 负责拦截 get/set
  - Reflect 负责按原生规则执行真正的 get/set（保证原型链、this、返回值行为一致）

### 依赖收集数据结构

- 使用 WeakMap → Map → Set 三层结构：
  - `targetMap: WeakMap<object, Map<key, Set<effect>>>`
  - 第一层按“目标对象”分组，第二层按“属性 key”分组，第三层是依赖该属性的 effect 集合

### effect（副作用函数）

- `effect(fn)`：把一个普通函数变成“响应式副作用”
  - 执行时设置 `activeEffect`，fn 内部访问到的响应式属性会通过 track 记录依赖
  - 属性变化时，trigger 会从依赖 Set 中找到这些 effect 并重新执行
- 实现细节：
  - 使用 effect 栈（effectStack）支持嵌套 effect，保证 `activeEffect` 始终指向当前执行的那个
  - 支持 `scheduler` 选项，用于自定义 effect 触发时的调度策略（例如批量更新）
- computed / watch：
  - computed 是基于 effect 的 lazy + 缓存封装
  - watch 是基于 effect 监听某个 getter 中用到的响应式数据
