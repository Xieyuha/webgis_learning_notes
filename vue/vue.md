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
	1.单向绑定(v-bind)：数据只能从data面。
	2.双向绑定(v-model)：数据不仅能从data页面，还可以从页面流向data。
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
		2.vm身上所有的属性 及 Vue原型上所有属性，在Vue模板中都可以直接使用。
###数据代理
    ####js中数据代理
    通过一个对象代理对另一个对象的属性操作
    object.defineProperty(person,'name',{
        value:'sam',        
        enumerable:true,    //控制属性是否可以枚举，默认false
        writable:true,      //控制属性是否可改写，默认false
        configurable:true   //控制属性是否可删除，默认false
    })
    ####Vue中数据代理
    通过vm对象来代理data对象中的属性，可以方便操作data中的数据
        通过Object.defineProperty()把data对象中所有属性添加到vm上。
        为每个添加到vm上的属性都添加getter和setter
        在getter/setter内部去操作data中的对应属性
    vm{             vm{                 vm{
        data    ->      _data       ->      name
    }               }                   }
                读属性            添加响应式
###事件处理
	事件的基本使用：
		1.使用v-on:xxx 或 @xxx 绑定事件，其中xxx是事件名；
		2.事件的回调需要配置在methods对象中，最终会在vm上；
		3.methods中配置的函数，不要用箭头函数！否则this就不是vm了；
		4.methods中配置的函数，都是被Vue所管理的函数，this的指向是vm 或 组件实例对象；
		5.@click="demo" 和 @click="demo($event)" 效果一致，但后者可以传参；
        6.$event是关键字，传入事件对象e
###事件修饰符
    可以连续写
    1.prevent:阻止默认事件
    2.stop:阻止事件冒泡
    3.once:事件只触发一次
    4.capture:使用事件的捕获模式，默认冒泡
        捕获阶段：由外向内
        冒泡阶段：由内向外
    5.self:只有event.target是当前操作的元素时才触发事件
    6.passive:事件1的默认行为立即执行，无需等待事件回调执行完毕
        移动端使用，@wheel
###键盘事件
    1.Vue中常用的按键别名：
		回车 => enter
		删除 => delete (捕获“删除”和“退格”键)
		退出 => esc
		空格 => space
		换行 => tab (特殊，必须配合keydown去使用)
		上 => up
		下 => down
		左 => left
		右 => right
	2.Vue未提供别名的按键，可以使用按键原始的key值去绑定，但注意要转为kebab-case（短横线命名）
	3.系统修饰键（用法特殊）：ctrl、alt、shift、meta
		(1).配合keyup使用：按下修饰键的同时，再按下其他键，随后释放其他键，事件才被触发。
		(2).配合keydown使用：正常触发事件。
	4.也可以使用keyCode去指定具体的按键（不推荐）
	5.Vue.config.keyCodes.自定义键名 = 键码，可以去定制按键别名
###计算属性
计算属性：
	1.定义：要用的属性不存在，要通过已有属性计算得来。
	2.原理：底层借助了Objcet.defineproperty方法提供的getter和setter。
	3.get函数什么时候执行？
		(1).初次读取时会执行一次。
		(2).当依赖的数据发生改变时会被再次调用。
	4.优势：与methods实现相比，内部有缓存机制（复用），效率更高，调试方便。
	5.备注：
		1.计算属性最终会出现vm上，直接读取即可。
		2.如果计算改，那必须写set响应修改，且set中要引起计算时依赖的数据发生改变。