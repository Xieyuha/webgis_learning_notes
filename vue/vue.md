##vue3简介
###创建vue工程
###基于vite创建vue3工程
	vite项目中，index.html是项目的入口文件，在项目最外层
	加载index.html后，vite解析<script type="module" src="/src/main.ts"></script>，指向JavaScript
	vue3中是通过createApp函数创建一个应用实例
###vue3核心语法
####options和composition API
	composition API:
	1.基于**函数**的API
	2.通过setup函数配置组件
		data直接声明，方法使用函数声明
####setup
	内部**没有this**，vue3弱化了this的概念
	返回值会暴露给模板使用，可以用简写，渲染模板时直接使用
	在beforeCreate和created之前执行
	当并列使用data和setup时，data可以读取setup返回的数据，但setup不能读取data中的数据
####ref
	创建基本数据类型的响应式数据
	通过.value读取和修改数据
	.value的值才是响应式数据
	ts	name:string = ref('sam').value
####reactive
	深层次包裹源对象，创建响应式对象
	缺点：重新分配对象时，响应式失效
####使用原则
	1.若需要一个基本类型的响应式数据，必须使用ref。
	2.若需要一个响应式对象，层级不深，ref、reactive都可以。
	3.若需要一个响应式对象，且层级较深，推荐使用reactive。
####toRefs和toRef
	1.toRefs：将reactive创建的响应式对象，转换为包含ref的对象
	2.toRef：将reactive创建的响应式对象中的某个属性，转换为ref
	reactive对象的值，拿出来并带上响应式
####computed
	与vue2中computed类似，使用类似ref()包裹箭头函数
```javascript lang="ts" setup
	computed(() => {
		return xxx
	})
	computed({
		get: () => {
			return xxx
		},
		set: (value) => {
			...
		}
	})
```
	基于函数创建计算属性
	返回值return是ref响应式的
####watch








##vue2基础
###初识vue:
    1.先创建一个vue实例，且要传入配置对象
    2.root容器内代码依然符合html规范，只不过多了vu法
    3，root容器内代码会被vue接管，被称为vue模板
    4.容器和实例是一一对应的
    5.真实开发只有一个vue实例，配合组件使用
    6.{{}}称为插值表达式，里面可以写js表达式，可以读取data中的数据
    7.一旦data中数据发生变化，页面中用到的模板中引数据的地方会自动更新
###vue模板语法:
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
###Vue中有2种数据绑定的方式：
	1.单向绑定(v-bind)：数据只能从data面。
	2.双向绑定(v-model)：数据不仅能从data页面，还可以从页面流向data。
		备注：
				1.双向绑定一般都应用在表单类元素上（如：input、select等）
				2.v-model:value 可以简写为 v-model，因为v-model默认收集的就是value值。
###el和data的补充说明：
    1.创建Vue实例时，el和data可以不写
    2.如果不写el，则需要手动调用$mount方法挂载
    3.data有两种写法：对象式和函数式
        对象式：适用于单个实例
        函数式：适用于组件，因为组件可以复用，必须每个组件实例都有自己独立的数据
            注意：data函数中必须return一个对象
            注意：不要使用箭头函数，否则thiswindow
###MVVM模型
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
###计算属性computed
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

###监视属性watch
监视属性：
	1.当被监视的属性变化时, 回调函数自动调用, 进行相关操作
	2.监视的属性必须存在，才能进行监视！！
	3.监视的两种写法：
		(1).new Vue时传入watch配置
		(2).通过vm.$watch监视
###深度监视(侦听)
深度监视：
	(1).Vue中的watch默认不监测对象内部值的改变（一层）。
	(2).配置deep:true可以监测对象内部值改变（多层）。
备注：
	(1).Vue自身可以监测对象内部值的改变，但Vue提供的watch默认不可以！
	(2).使用watch时根据数据的具体结构，决定是否采用深度监视。
###computed和watch之间的区别：
	1.computed能完成的功能，watch都可以完成。
	2.watch能完成的功能，computed不一定能完成，例如：watch可以进行异步操作。
两个重要的小原则：
	1.所被Vue管理的函数，最好写成普通函数，这样this的指向才是vm 或 组件实例对象。
	2.所有不被Vue所管理的函数（定时器的回调函数、ajax的回调函数等、Promise的回调函数/即异步操作），最好写成箭头函数，
		这样this的指向才是vm 或 组件实例对象。
###样式绑定
```javascript
<div id="root">
        <!-- 绑定class样式--字符串写法，适用于：样式的类名不确定，需要动态指定 -->
        <div class="basic" :class="mood" @click="changeMood">1</div> <br/><br/>
        <!-- 绑定class样式--数组写法，适用于：要绑定的样式个数不确定、名字也不确定 -->
        <div class="basic" :class="classArr">2</div><br><br>
        <!-- 绑定class样式--**对象**写法，适用于：要绑定的样式个数确定、名字也确定，但要动态决定用不用 -->
        <div class="basic" :class="classObj" @click="changeObj">3</div>
</div>
```
####字体
:style="fontSize:fsize+'px"
    fontSize去掉-，小驼峰命名
###条件渲染
条件渲染：
	1.v-if
		写法：
			(1).v-if="表达式" 
			(2).v-else-if="表达式"
			(3).v-else="表达式"
				适用于：切换频率较低的场景。
				特点：不展示的DOM元素直接被移除。
				注意：v-if可以和:v-else-if、v-else一起使用，但要求结构不能被“打断”。
	2.v-show
		写法：v-show="表达式"
		适用于：切换频率较高的场景。
		特点：不展示的DOM元素未被移除，仅仅是使用样式隐藏掉				
	3.备注：使用v-if的时，元素可能无法获取到，而使用v-show一定可以获取到。
频繁切换显示用v-show
条件复杂且切换不频繁(需销毁/重建、释放资源)用v-if
###列表渲染
	v-for指令:
	    1.用于展示列表数据
	    2.语法:v-for="(item,index) in 数据源" :key="唯一标识符"
			不写key，默认index
	    3.可遍历:数组、对象、字符串、指定次数
		4.注意:key的值必须唯一且稳定，推荐使用数据的id属性，而不是index
	key的作用:
		1.作为虚拟DOM diff算法的依据，提高渲染效率
			进行虚拟DOM比较时：
				虚拟DOM不同，依次新的覆盖旧的
				虚拟DOM一样，真实元素复用
####vue diff算法
	核心目标: 减少对真实DOM的操作，尽可能找出可复用的点，只移动、更新、销毁必要的部分
	key:
	旧列表与新列表，利用指针从两端开始，进行比较
		key相同，复用并更新内容
			导致问题:key相同，使vue认为是相同的旧节点，但input的value值实际是浏览器内部的值，导致错误匹配
		key不同，认为是不同节点
			此时vue执行:
				-销毁旧节点
				-创建新节点
				-插入到相应位置
面试题：react、vue中的key有什么作用？（key的内部原理）			
	1. 虚拟DOM中key的作用：
		key是虚拟DOM对象的标识，当数据发生变化时，Vue会根据【新数据】生成【新的虚拟DOM】, 
		随后Vue进行【新虚拟DOM】与【旧虚拟DOM】的差异比较，比较规则如下：			
	2.对比规则：
		(1).旧虚拟DOM中找到了与新虚拟DOM相同的key：
			①.若虚拟DOM中内容没变, 直接使用之前的真实DOM！
			②.若虚拟DOM中内容变了, 则生成新的真实DOM，随后替换掉页面中之前的真实DOM。
		(2).旧虚拟DOM中未找到与新虚拟DOM相同的key
			创建新的真实DOM，随后渲染到到页面。		
	3. 用index作为key可能会引发的问题：
		1. 若对数据进行：逆序添加、逆序删除等破坏顺序操作:
			会产生没有必要的真实DOM更新 ==> 界面效果没问题, 但效率低。
		2. 如果结构中还包含输入类的DOM：
			会产生错误DOM更新 ==> 界面有问题。
	4. 开发中如何选择key?:
		1.最好使用每条数据的唯一标识作为key, 比如id、手机号、身份证号、学号等唯一值。
		2.如果不存在对数据的逆序添加、逆序删除等破坏顺序操作，仅用于渲染列表用于展示，
			使用index作为key是没有问题的。
####监测数据变化的原理
	1.Vue通过Object.defineProperty()给data中的每个属性添加getter和setter。
	2.当读取属性时，触发getter，从而进行依赖收集。
	3.当修改属性时，触发setter，从而通知所有依赖进行更新。
	data数据的响应式原理：
		1.数据劫持(加工)：通过Object.defineProperty()给data中的每个属性添加getter和setter。
			通过observe函数间接处理data对象，防止直接defineProperty()造成递归内存泄漏
		2.依赖收集：在getter中收集依赖，即记录哪些组件使用了该属性。
		3.派发更新：在setter中通知所有依赖进行更新。 
####vue.set()
	对象上undefined的属性，vue会不显示
	Vue.set(obj, 'newProp', value)
	或者
	vm.$set(obj, 'newProp', value)
	局限性：
		1.只能给对象添加响应式属性，不能给数组添加响应式属性。
		2.只能添加一层响应式，不能添加深层响应式。即只在data的第一层属性上有效。
####响应原理
	对象:给属性直接加getter和setter
	数组:修改原型链方法，拦截会改变数组内容的方法
		包裹7个变异方法:
			push pop shift unshift splice sort reverse
		注意:直接通过索引修改数组不会触发视图更新
###收集表单数据
	收集表单数据：
			若：<input type="text"/>，则v-model收集的是value值，用户输入的就是value值。
			若：<input type="radio"/>，则v-model收集的是value值，且要给标签配置value值。
			若：<input type="checkbox"/>
					1.没有配置input的value属性，那么收集的就是checked（勾选 or 未勾选，是布尔值）
					2.配置input的value属性:
							(1)v-model的初始值是非数组，那么收集的就是checked（勾选 or 未勾选，是布尔值）
							(2)v-model的初始值是数组，那么收集的的就是value组成的数组
			备注：v-model的三个修饰符：
							lazy：失去焦点再收集数据
							number：输入字符串转为有效的数字
							trim：输入首尾空格过滤
###内置指令
	v-text: 设置元素的textContent属性
	v-html: 设置元素的innerHTML属性
		注意：使用v-html时，务必确保内容安全，避免XSS攻击。
	v-show: 控制元素的display样式属性
	v-cloak: 解决闪烁问题，配合css使用,只有在元素被编译完成后才会被显示
		[v-cloak] {
  			display: none;
		}
	v-once: 只渲染元素和组件一次，之后数据变化不再更新
	v-pre: vue跳过该元素和子元素的编译过程	//提高性能,用于静态内容较多的场景
###生命周期
	1.又名：生命周期回调函数、生命周期函数、生命周期钩子
	2.定义：在Vue实例生命周期的不同阶段自动调用的函数
	3.生命周期函数的名字不可更改，但函数的具体内容是程序员根据需求编写的
	4.生命周期函数中的this指向vm或组件实例对象
	挂载阶段：
		beforeCreate: 实例初始化之后，数据观测(data observer)和事件配置(event/watcher)之前被调用。
		created: 实例创建完成后被立即调用。在这一步，实例已完成以下的配置：数据观测(data observer)、属性和方法的运算、watch/event事件回调。然而，挂载阶段还没有开始，$el属性目前不可见。
		beforeMount: 在挂载开始之前被调用：相关的render函数首次被调用。
		mounted: el被新创建的vm.$el替换，并挂载到实例上去之后调用该钩子。
	更新阶段：
		beforeUpdate: 数据更新时调用，发生在虚拟DOM重新渲染和打补丁之前。你可以在这个钩子中进一步地更改状态，这不会触发附加的重渲染过程。数据是新的，但页面还没有更新。
		updated: 由于数据更改导致的虚拟DOM重新渲染和打补丁之后调用。在这个钩子中，你可以执行依赖于DOM的操作。然而，在大多数情况下，你应该避免在此期间更改状态，因为这可能会导致更新无限循环。
		此时，数据、页面和DOM都已经更新完毕。
	销毁阶段：
		beforeDestroy: 实例销毁之前调用。在这一步，实例仍然完全可
		用。关闭定时器、取消订阅等清理工作最好在这一步进行。
		destroyed: Vue实例销毁后调用。调用后，Vue实例指示的所有东西都会解绑定，所有的事件监听器都会被移除，所有的子实例也会被销毁。
	常用的生命周期钩子：
		1.mounted: 发送ajax请求(created)、启动定时器、绑定自定义事件、订阅消息等【初始化操作】。
		2.beforeDestroy: 清除定时器、解绑自定义事件、取消订阅消息等【收尾工作】。
	关于销毁Vue实例
		1.销毁后借助Vue开发者工具看不到任何信息。
		2.销毁后自定义事件会失效，但原生DOM事件依然有效。
		3.一般不会在beforeDestroy操作数据，因为即便操作数据，也不会再触发更新流程了。
	
