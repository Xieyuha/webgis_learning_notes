# 简单是结果，复杂是过程

## html

URL本质:获取资源的地址字符串

## css

### 单位

#### 绝对单位

	px

#### 相对单位

| 单位     | 相对对象                       | 含义             | 举例                        |
| ------- | --------------------------     | --------------- | ------------------------- |
| **em**  | 当前元素的 `font-size`          | 1em = 当前字体大小    | 若父级 16px，则 2em = 32px     |
| **rem** | 根元素 `<html>` 的 `font-size`  | 1rem = 根字体大小    | 响应式常用（默认16px）             |
| **ex**  | 当前字体的 x 高度               | 不常用             | 英文字体排版用                   |
| **ch**  | “0” 字符宽度                    | 用于输入框等等宽字体布局    | `width: 20ch` 表示能放 20 个字符 |
| **%**   | 父元素尺寸                      | 相对于父级属性（宽高、字体等） | `width: 50%`              |

建议用法：

em → 局部缩放（组件内部）

rem → 全局响应式（随根字体变化）

% → 容器适应布局

#### 视口单位

| 单位                  | 相对对象                   | 含义              |
| ------------------- | ---------------------- | --------------- |
| **vw**              | viewport width         | 1vw = 视口宽度的 1%  |
| **vh**              | viewport height        | 1vh = 视口高度的 1%  |
| **vmin**            | viewport min           | 1vmin = 较小边的 1% |
| **vmax**            | viewport max           | 1vmax = 较大边的 1% |
| **svh / lvh / dvh** | 新一代单位（稳定 / 大 / 动态视口高度） | 用于移动端适配键盘伸缩场景   |

px定尺寸，em局部调，rem根控全，vw/vh随窗变

### box-shadow:盒子阴影
- 语法: box-shadow: h-offset v-offset blur spread color inset;
- h-offset:水平偏移量，必需
- v-offset:垂直偏移量，必需
- blur:模糊距离，可选
- spread:扩展距离，可选
- color:阴影颜色，可选
- inset:内阴影，可选

### border-radius:圆角
- 语法: border-radius: top-left top-right bottom-right bottom-left;
- 也可以单独设置某个角的圆角: border-top-left-radius:10px;

### flexbox布局
- display:flex; //设置为flex布局
- flex-direction:row|row-reverse|column|column-reverse; //主轴方向，默认row(水平从左到右)
- justify-content:flex-start|flex-end|center|space-between|space-around; //主轴对齐方式
- align-items:flex-start|flex-end|center|stretch; //侧轴对齐方式
- flex-wrap:nowrap|wrap|wrap-reverse; //是否换行，默认nowrap不换行
#### 其他常用属性与技巧

- align-content: 控制多行（wrap 后）的整行在侧轴上的分配，仅在有换行时生效。值：flex-start、flex-end、center、space-between、space-around、stretch。
- align-self: 覆盖单个项的侧轴对齐（auto | flex-start | flex-end | center | baseline | stretch）。
- gap / row-gap / column-gap: 控制子项间距，flex 支持，替代 margin 间隔更直观。
- order: 控制视觉顺序（整数，默认 0）。注意：不要用 order 改变语义性的阅读顺序或键盘导航。
- flex（简写）: flex: <grow> <shrink> <basis>;
	- 示例：
		- flex: 1 → 等价于 1 1 0%（均分剩余空间）
		- flex: 0 0 auto → 固定大小依赖内容或 basis
		- flex: 0 1 200px → 基础为 200px，可收缩
- flex-grow / flex-shrink / flex-basis:
	- grow：当有剩余空间时按比率扩展
	- shrink：空间不足时按比率收缩
	- basis：主轴上的初始大小（可用百分比/px）
##### 常见布局套路：
	- 居中单元素：display:flex; justify-content:center; align-items:center;
	- 等高列：父元素 align-items:stretch（默认）
	- 均分列：子项 flex:1;
	- 固定头部、可滚动内容：容器列方向，头部 flex:0，主体 flex:1; overflow:auto;
##### 换行与多行分布：
	flex-wrap:wrap + align-content 控制多行的分布与间距。
##### 常见坑与优化：
	- flex 子项默认 min-width:auto 可能导致溢出，必要时设 min-width:0 或 overflow:hidden。
	- 动画尽量用 transform/opacity，避免频繁触发布局重排。
- 嵌套 flex：子容器同样可以设为 flex，注意主轴/侧轴方向的相互影响。
- 可访问性与语义：
	- DOM 顺序决定键盘导航和屏幕阅读器顺序，尽量不只用 order 改变顺序以免破坏可访问性。
	- 保持可聚焦元素在语义顺序上正确。
- 调试技巧：
	- 在开发阶段给 flex 项加不同背景色/outline 帮助观察布局。
	- 使用浏览器 DevTools 的 flex 辅助线查看主轴/侧轴分布。
- 兼容性：
	- 现代浏览器对 flex 支持良好，IE11 有若干差异（flex-basis 为 auto/百分比等行为不同），如需兼容请加测试或备用样式。
##### 小结：
掌握 `flex-grow/shrink/basis`、`align-*/justify-content`、`gap` 与 `wrap`，能覆盖绝大多数一维布局场景。

## js
- ArrayBuffer:只读的、空间连续的、定长字节数组
- proxy与denfinProperty区别(实际非同一层级，但vue2 3 的使用有不同)
 	- object(基本方法)->function(也是对象) ->constructor(对象，支持内部方法construct 来new实例)。proxy可以重新定义基本方法
 	- vue2使用defineProperty
- 拦截现有属性读写，使用set get，但有缺陷比如length
		vue3用proxy，全拦截
### 模块化
```ts
	//默认暴露
	export default function(){
        console.log()
	}
	//分别暴露
	export function getSum(){
        
	}
```
### 数组新方法(ES2022)
- arr.at(index)//支持负数索引，返回对应位置的元素 
- arr.toReversed()//不改变原数组，返回一个新反转后的数组
- arr.with(index,value)//不改变原数组，返回一个新数组，index位置替换为value
- arr.toSpliced(start,deleteCount,...items)//不改变原数组，返回一个新数组，从start位置删除deleteCount个元素，并插入items元素
- arr.toFiltered(item=>item.age>18)//不改变原数组，返回一个新数组，包含所有age大于18的元素

## vue
	组件、hooks、类、模块的联系
- 模块化定义代码的边界和依赖关系
- 页面由 UI 驱动，长期维护依靠组件化组织视图
- 组件内部，数据由类和reactive(/响应式)对象
- hooks/composables对逻辑行为与副作用进行抽离，以 函数 形式实现复用
