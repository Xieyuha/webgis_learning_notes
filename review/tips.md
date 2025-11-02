#简单是结果，复杂是过程
##html
URL本质:获取资源的地址字符串
##css
###box-shadow:盒子阴影
    语法: box-shadow: h-offset v-offset blur spread color inset;
    h-offset:水平偏移量，必需
    v-offset:垂直偏移量，必需
    blur:模糊距离，可选
    spread:扩展距离，可选
    color:阴影颜色，可选
    inset:内阴影，可选

###border-radius:圆角
    语法: border-radius: top-left top-right bottom-right bottom-left;
    也可以单独设置某个角的圆角: border-top-left-radius:10px;

###flexbox布局
    display:flex; //设置为flex布局
    flex-direction:row|row-reverse|column|column-reverse; //主轴方向，默认row(水平从左到右)
    justify-content:flex-start|flex-end|center|space-between|space-around; //主轴对齐方式
    align-items:flex-start|flex-end|center|stretch; //侧轴对齐方式
    flex-wrap:nowrap|wrap|wrap-reverse; //是否换行，默认nowrap不换行
##js
	ArrayBuffer:只读的、空间连续的、定长字节数组
	proxy与denfinProperty区别(实际非同一层级，但vue2 3 的使用有不同)
		object(基本方法)->function(也是对象) ->constructor(对象，支持内部方法construct 来new实例)。proxy可以重新定义基本方法
		vue2使用defineProperty
	拦截现有属性读写，使用set get，但有缺陷比如length
		vue3用proxy，全拦截
###数组新api
    不改变原数组的方法：
    arr.toSorted((a,b)=>a.age-b.age)//不改变原数组，返回一个新排序后的数组
    arr.toReversed()//不改变原数组，返回一个新反转后的数组
    arr.with(index,value)//不改变原数组，返回一个新数组，index位置替换为value
    arr.toSpliced(start,deleteCount,...items)//不改变原数组，返回一个新数组，从start位置删除deleteCount个元素，并插入items元素
    arr.toFiltered(item=>item.age>18)//不改变原数组，返回一个新数组，包含所有age大于18的元素