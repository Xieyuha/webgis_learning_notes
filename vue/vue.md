#vue3简介
#创建vue工程
##基于vite创建vue3工程
vite项目中，index.html是项目的入口文件，在项目最外层
加载index.html后，vite解析<script type="module" src="/src/main.ts"></script>，指向JavaScript
vue3中是通过createApp函数创建一个应用实例
#vue2基础
##初识vue:
    1.先创建一个vue实例，且要传入配置对象
    2.root容器内代码依然符合html规范，只不过多了vu法
    3，root容器内代码会被vue接管，被称为vue模板
    4.容器和实例是一一对应的
    5.真实开发只有一个vue实例，配合组件使用
    6.{{}}称为插值表达式，里面可以写js表达式，可以读取data中的数据
    7.一旦data中数据发生变化，页面中用到的模板中引数据的地方会自动更新
##vue模板语法:
    1.插值语法: {{}}  v-text v-html
        功能：用于解析标签体内容
    2.指令语法: v-xxx
        v-bind:  动态绑定属性
        v-on:    绑定事件
        v-if     条件渲染
        v-for    列表渲染
        v-model  双向数据绑定
        ...
        功能：用于解析标签（包括属性、内容、事件）
##Vue中有2种数据绑定的方式：
	1.单向绑定(v-bind)：数据只能从dat面。
	2.双向绑定(v-model)：数据不仅能从da页面，还可以从页面流向data。
		备注：
				1.双向绑定一般都应用在表单类元素上（如：input、select等）
				2.v-model:value 可以简写为 v-model，因为v-model默认收集的就是value值。
##el和data的补充说明：
    1.创建Vue实例时，el和data可以不写
    2.如果不写el，则需要手动调用$mount方法挂载
    3.data有两种写法：对象式和函数式
        对象式：适用于单个实例
        函数式：适用于组件，因为组件可以复用，必须每个组件实例都有自己独立的数据
            注意：data函数中必须return一个对象
            注意：不要使用箭头函数，否则thiswindow
##MVVM模型
	MVVM模型
		1. M：模型(Model) ：data中的数据
		2. V：视图(View) ：模板代码
		3. VM：视图模型(ViewModel)：Vue实例
	观察发现：
		1.data中所有的属性，最后都出现在了vm身上。
		2.vm身上所有的属性 及 Vue原型上所有属性，在V模板中都可以直接使用。
		