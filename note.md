# VUE从入门到精通

### Vue原理

给一个对象添加属性的时候,可以用Object.defineProperty()这个方法来进行添加,Vue也是利用这个原理进行属性的监听

```html
<body>
    <div id="app">
    	
    </div>
</body>
<script>
	var app = document.querySelect("#app");
    var obj = {};
    obj.defineProperty(obj,"name",{
        set(value){
            console.log("我被设置了",value);
        },
        get(){
            console.log("我被获取了");
        }
    })
</script>
```

### MVVM

Vue就是基于MVVM模式的一种框架，中间的vm对视图（View）和Module（模型）进行双向数据绑定

### 事件绑定注意

在事件绑定中，调用方法时加括号和不加括号的区别：当加括号时候，Vue会把操作权交给你，所以这时候想在方法中获取事件对象如果没传值的话就会undefine，当不加括号的时候，Vue认为自己该操作，所以就会把事件对象当做默认的一个参数传到被调用的方法中。

```html
<body>
    <div id="app">
        <input type="text" @input="handleInput"/>
        <button @click="handleAdd()">添加</button>
    </div>
</body>
<script type="text/javascript">
    var vm = new Vue({
        el:"#app",
        data:{},
        methods:{
            handleAdd:function(evt){
                console.log(111,evt);   //打印结果：111 undifine
            },
            handleInput:function(evt){
                console.log(2222222, evt);  //打印结果：111 InputEvent对象
            }
        }
    })
</script>

```

### Class属性为对象或数组

见代码注释：

```html
<style>
    .aa{color: red;}
    .bb{font-size: 40px;}
    .cc{font-weight: bold;}
</style>
</head>
<body>
    <div id="app">
        <div :class="classArr">动态切换class-对象写法</div>
        <div :class="classObj">动态切换class-对象写法</div>
    </div>

</body>
<script type="text/javascript">
    var vm = new Vue({
        el:"#app",
        data:{
            classObj:{
                "aa":true,
                "bb":true,
                "cc":false          //true为添加该属性，false为不添加
            },
            classArr:["aa","bb","cc"]   //只要在数组中的值都会被添加，对样式进行增加或移除，只需要对数组进行操作就行
        }
    })
</script>

```

### v-for知识

#### 对象遍历

用v-for也可以进行对象的遍历，v-for=” value,key,index in obj”

其中，遍历出来的item是obj的value，index是obj的key

数组的本质也是对象，只不过item是每一项，index为索引值

```html
<body>
    <div id="app">
        <ul>
            <li v-for="value,key,index in obj">
            	{{value}}----{{key}}----{{index}}
            </li>
        </ul>
    </div>
</body>
<script>
	var vm = new Vue({
        el:"#app",
        data:{
            obj:{
                name:"kerwin",
                age:18,
                location:"Beijing"
            }
        }
    })
</script>
```

#### key值

作用：跟踪每个节点的身份，从而重用和重新排序现有元素

理想的key值是每项都有的且唯一的id。(Item.id)



设置虚拟dom是为了能够在对比之后找到哪些节点是可以复用的，而设置虚拟dom又不足以找到快速对比的方法，所以在长列表中必须设置一个有效的key值。这样通过一个虚拟的dom和key值的有效设定才能够用最快速的方法找到哪些节点需要更新，哪些节点需要删除，从而进行下一步真实dom的修改。这也是VUE为什么渲染的这么快，为什么能提高性能的原因。



为什么不用真实dom进行操作呢？原因是真实dom里面的东西太多！做如下实验：

### 数组更新检测

- 使用以下方法操作数组，可以被检测到变动

push()、pop()、shift()、unshift()、splice()、sort()、reverse()

- 无法检测变动的方法，解决的方法时用新数组替换旧数组

filter()、concat()、slice()、map()

- 无法检测以下变动的数组

Vm.items[indexOfItem] = newValue   //vm.dataList[0] = “kerwin”

**解决方法：**

​	1、splice方法  arr.splice(0,1,”kerwin”) 		 //将数组的第一个值改为kerwin

​	2、Vue.set(example1.items, indexOfItem, newValue)

### 事件处理

当进行事件处理的时候，不写括号可以默认将事件对象传过去，那如果想传值又想获取事件对象呢？那就用$event来代替事件对象，这个方法时万能方法，如下边的@click="handleAdd($event,2)"，把$event当做参数传过去，在handleAdd中就可以获取了，既能获取想要传的值2，又能获取事件对象。

```html
<body>
		<div id="app">
			{{value}}
			<button type="button" @click="handleAdd($event,2)">增加</button>
		</div>
		
	</body>
	<script type="text/javascript">
		var vm = new Vue({
			el:"#app",
			data:{
				value:1
			},
			methods:{
				handleAdd:function(evt,val){
					console.log(evt.target);
					this.value=this.value+val;
				}
			}
		})
	</script>
```

### 事件修饰符

**阻止冒泡** 

```html
<ul @click="handleUlClick">
    <li @click.stop="handleLiClick">1111111</li>
</ul>
```

**阻止默认行为**

```html
<a href="https://www.baidu.com" @click.prevent>点击</a>
```

**点击自己的时候才触发（模态框中可以应用到）**

```html
<div id="overlay" @click.self="isShow=false">
    <div>
    	用户名：<input type="text">
        密  码：<input type="password">
    </div>
<div>
```

**只允许点击一次**

```html
<button @click.once="handleClick()">抽奖</button>
```

**v-model对多选框进行绑定**

v-model对多选框进行绑定时，是以数组进行存储的，必须在标签中添加value属性，要不Vue不知道值是什么，同时，v-model绑定的是同一个数组

```html
<body>
    <div id="app">
        <input type="checkbox" v-model="checkGroup" value="Vue"/>Vue
        <input type="checkbox" v-model="checkGroup"/ value="React">React
        <input type="checkbox" v-model="checkGroup"/ value="jQuery">JQuery
        <button type="button" @click="handleSubmit()">提交</button>
    </div>

</body>
<script type="text/javascript">
    var vm = new Vue({
        el:"#app",
        data:{
            checkGroup:[]
        },
        methods:{
            handleSubmit:function(){
                console.log("提交",this.checkGroup);
                this.checkGroup = [];
            }
        }
    })
</script>
```

对于多选框只绑定一个值的情况，比如【记住我】这个功能，可以将该值设置为一个布尔值

```html
<body>
    <div id="app">
        <h2>记住我</h2>
        <input type="checkbox" v-model="isRem"/>记住我
        <button type="button" @click="handleLogin()">登录</button>
    </div>
</body>
<script type="text/javascript">
    var vm = new Vue({
        el:"#app",
        data:{
            isRem:false
        },
        methods:{
            handleLogin:function(){
                console.log("登录",this.isRem);
            }
        }
    })
</script>
```

单选按钮的绑定，是绑定一个字符串，会将radio元素的value值进行绑定

```html
<body>
    <div id="app">
        <h2>单选radio</h2>
        <input type="radio" v-model="favlang" value="Vue"/>Vue
        <input type="radio" v-model="favlang" value="React"/>React
        <input type="radio" v-model="favlang" value="jQuery"/>jQuery
        <button type="button" @click="handleSelect()">提交</button>
</body>
<script type="text/javascript">
    var vm = new Vue({
        el:"#app",
        data:{
            favlang:""
        },
        methods:{
            handleSelect:function(){
                console.log("最喜欢",this.favlang);
            }
        }
    })
</script>
```

### 表单修饰符

| 修饰符  | 说明         |
| :------ | ------------ |
| .lazy   | 懒更新       |
| .number | 转为数字格式 |
| .trim   | 去掉首尾空格 |

```html
<body>
    <div id="app">
        用户名：<input type="text" v-model.lazy="username"/>{{username}}
        密码：<input type="password" v-model.trim="password"/>{{password}}
        年龄：<input type="number" v-model.number="age"/>{{age}}
        <button type="button" @click="handleSubmit()">提交</button>
    </div>

</body>
<script type="text/javascript">
    var vm = new Vue({
        el:"#app",
        data:{
            username:"",
            password:"",
            age:18
        },
        methods:{
            handleSubmit(){
                console.log(this.username);
                console.log(this.password);
                console.log(this.age);
            }
        }

    })
</script>
```

### 计算属性与方法的区别

| 计算属性computed                                             | 方法methods                                           |
| ------------------------------------------------------------ | ----------------------------------------------------- |
| 必须要有return值，这点是不能取代methods的                    | 不一定要有return值，也可能只是逻辑处理，没有返回值    |
| 有缓存。computed比较聪明，多个地方调用的话如果值没改变就只计算一遍 | 没有缓存。methods是地地道道的老实人，每次调用都会执行 |

### mixins

将一个obj对象混入到一个Vue实例中，obj对象中的属性就都可以用了，提高代码的重用性，就像Java中的继承一样，把obj继承过来就可以用里边的属性了。但是，如果我Vue实例中有这个方法咋办？不用担心，我自己Vue中同名的方法优先级更高。如下边的handleClick()。

```html
<body>
    <div id="app">
        <button type="button" @click="handleClick1()">Click1</button>
        <button type="button" @click="handleClick2()">Click2</button>
        {{total}}
    </div>

</body>
<script type="text/javascript">

    const obj = {
        methods:{
            handleClick(){
                console.log("handleClick---obj");
            },
            handleClick1(){
                console.log("handleClick1");
            },
            handleClick2(){
                console.log("handleClick2");
            }
        },
        computed:{
            total(){
                return "computed 111111111";
            }
        }
    }
    var vm = new Vue({
        el:"#app",
        mixins:[obj],	//将上边的obj混入到这个Vue实例中，obj对象中的属性就都可以用了，提高代码的重用性，用数组的方式进行混入
        data:{

        },
        methods:{
			handleClick(){
                console.log("handleClick---Vue");
            }
        }

    })
</script>
```

### fetch

why?XMLHttpRequest是一个设计粗糙的API，配置和调用方式非常混乱，而且基于事件的异步模型写起来不友好。

那些专家也认识到了这一点，所以又重出江湖，重新定义了一个新标准就是fetch，所以说fetch是属于W3C的新标准，可以直接用，不需要导入任何库。

**get方式**

```javascript
fetch("https://autumnfish.cn/api/joke/list?num=3")
    .then(res=>{
        console.log(res);
        return res.json();
    }).then(res=>{
        console.log(res);
    })
```

**post方式一**

```javascript
fetch("**",{
    method:"post",
    headers:{
        "Content-Type":"application/x-www-form-urlencoded"
    },
    body:"name=kerwin&age=18",
    credentials:'include'		//如果需要带cookie，需要加上这个属性
})
.then(res=>res.json()).then(res=>{console.log(res)})
```

**post方式二**

```javascript
fetch("**",{
    method:"post",
    headers:{
        "Content-Type":"application/json"
    },
    body:JSON.stringify({
        name:"kerwin",
        age:18
    })
})
.then(res=>res.json()).then(res=>{console.log(res)})
```

注意：fetch请求默认是不带cookie的，需要设置fetch(url,{credentials:'include'})，axios和jQuery都会默认带着cookie

> https://unpkg.com/axios/dist/axios.min.js

几个API接口

>  https://autumnfish.cn/api/joke/list?num=3
>
> https://autumnfish.cn/api/user/reg

### axios

**get请求**

```javascript
axios.get("https://autumnfish.cn/api/joke/list?num=3")
					.then(res=>{
						console.log(res);
						this.jokes = res.data.jokes;
					}).then(err=>{
						console.log(err);
					})
```

**post请求**

在进行post请求时，只需要在第二个参数中传对应格式的参数，axios会自动转换成对应的请求体

```javascript
// "Content-Type":"application/x-www-form-urlencoded"
axios.post("**","name=kerwin&age=18").then(res=>{console.log(res)});


// "Content-Type":"application/json"
axios.post("**",{
	name:"kerwin",
	age:18
}).then(res=>{console.log(res)}).then(err=>{console.log(err)})
```

### 组件

注意：

1. 起名字：js驼峰，html连接符-
2. dom片段没有代码提示，没有高亮显示（Vue单文件组件可以解决）
3. css只能写成行内式（单文件组件可以解决）
4. template只能包含一个根节点
5. 组件是件孤岛，无法【直接】访问外面组件的状态和方法（需要通过间接的组件通信方式来交流）
6. 自定义组件的data必须是一个函数

### 组件通信

#### 父传子

父传子的时候，是通过属性来传值，在子组件中添加props属性

```javascript
//一般形式
props:["mytitle","myshow"]

//属性验证形式
props:{
    mytitle:String,
    myshow:Boolean
}

//默认值形式
props:{
    mytitle:{
   		type:String,
        default:"默认值"
    },
    myshow:{
        type:Boolean,
        default:true
    }
}
```

#### 子传父

子传父是通过在子组件上添加一个类似于监听的事件，当子组件触发这个事件的时候，相应的执行父组件中的方法，子组件也可以将值传到父组件中。

在下面这个例子中，父组件在子组件上放了一个【myevent】的监听，当子组件通过【this.$emit("myevent",this.money);】触发的时候，父组件就执行相应的【handleEvent】方法，并且获取到了子组件传过来的【money】值。

```html
	<body>
		<div id="app">
			parent
			<child @myevent="handleEvent"></child>
		</div>
	</body>
	<script type="text/javascript">
		Vue.component("child",{
			data(){
				return{
					money:1000000
				}
			},
			template:`
				<div style="background:yellow">
					child<button @click="handleClick()">按钮</button>
				</div>
			`,
			methods:{
				handleClick(){
					this.$emit("myevent",this.money);
				}
			}
		})
		var vm = new Vue({
			el:"#app",
			data:{
			},
			methods:{
				handleEvent(money){
					console.log("父组件11111111111111",money);
				}
			}
		})
	</script>
```

#### ref

ref放在标签上，拿到的是原生dom节点。

如下例子，ref放在div上，然后通过this.$refs可以获取所有添加了ref属性的标签，然后在通过.aaa找到这个div，接着再通过.innerHTML获取到该div中的值

```html
<body>
		<div id="app">
			<div ref="aaa">11sdf11</div>
			<button @click="handleClick()">click</button>
		</div>
		
	</body>
	<script type="text/javascript">
		
		var vm = new Vue({
			el:"#app",
			data:{
			},
			methods:{
				handleClick(){
					console.log(this.$refs.aaa.innerHTML);
				}
			}
		})
	</script>
```

ref放在标签上，拿到的是组件对象，可以用于父子组件通信，但是不推荐这个用法，这个方法太暴力！又能获取又能修改

```html
<body>
		<div id="app">
			<child ref="child"></child>
			<button @click="handleClick()">click</button>
		</div>
		
	</body>
	<script type="text/javascript">
		
		Vue.component("child",{
			data(){
				return{
					childName:"1111111111111111111"
				}
			},
			template:`
				<div>{{childName}}</div>
			`
		})
		var vm = new Vue({
			el:"#app",
			data:{
			},
			methods:{
				handleClick(){
					console.log(this.$refs.child.childName);		//获取子组件的值
					this.$refs.child.childName = "222222222222222"	//设置子组件的值
				}
			}
		})
	</script>
```

#### 中间人模式

只适用于简单的场景，稍微复杂一点就不好使用了，很麻烦，原理就是用子传父和父传子

#### bus通信

中央事件总线

原理：一个订阅者，一个发布者，就类似于我们在微信中订阅的公众号一样，我们订阅某个公众号之后，当该公众号发布文章时，我们这边就能收到

订阅的时候用bus.$on("kerwin",()=>{})，发布的时候用bus.$emit("kerwin",data)

注意：$on() 和$emit() 必须用同一个bus，即同一个Vue对象！

```html
	<body>
		<div id="app">
			<button @click="getData()">ajax</button>
			<filmlist v-for="item in dataList" :film="item" :key="item.id"></filmlist>
			<detail></detail>
		</div>
	</body>
	<script type="text/javascript">
		
		var bus = new Vue();			//中央事件总线
        
        //影片列表组件
		Vue.component("filmlist",{
			template:`
				<div style="border:1px solid #f40;width:200px">
					<img :src="film.img.replace('w.h\/','')" style="width:200px;"/>
					<h3>{{film.nm}}</h3>
					<button @click="getDetail()">详情</button>
				</div>
			`,
			props:["film"],
			methods:{
				getDetail(){
					bus.$emit("kerwin",this.film.star);		//触发自定义的kerwin事件，并将参数传过去
				}
			}
		})
		
        //影片详情组件
		Vue.component("detail",{
			
			template:`
				<div style="border:1px solid #f40;position:fixed;right:0;top:0;">
					<h3>{{star}}</h3>
				</div>
			`,
			data(){
				return{
					star:""
				}
			},
			mounted(){
				bus.$on("kerwin",(data)=>{			//在mounted生命周期函数中已经订阅，所以当发布者发布消息的时候就能立马收到
					console.log(111,data);
					this.star = data;
				})
			}
		})
		
		var vm = new Vue({
			el:"#app",
			data:{
				dataList:[],
			},
			methods:{
				getData(){
					axios.get("film.json")
					.then(res=>{
						console.log(res.data);
						this.dataList = res.data.movieList;
					})
				}
			}
		})
	</script>
```



### 动态组件

动态组件用Vue预留的<component is="home|list|shopcar"></component>来显示，他是个墙头草，is属性的值传什么，他就显示什么，如前面的传home、list、shopcar其中任意一个值，那他就显示对应的组件。



进行切换的时候，如果不想让原来的组件内容消失，可以加个<keep-alive></keep-alive>将<component><component>包起来。

```html
	<body>
		<div id="app">
            <keep-alive>
				<component :is="which"></component>
            </keep-alive>
			<footer>
				<ul>
					<li @click="which = 'home'">首页</li>
					<li @click="which = 'list'">列表</li>
					<li @click="which = 'shopcar'">购物车</li>
				</ul>
			</footer>
		</div>
	</body>
	<script type="text/javascript">
		
		Vue.component("home",{
			template:`
				<div>首页
        			<input type="text" />
        		</div>
			`
		})
		Vue.component("list",{
			template:`
				<div>列表</div>
			`
		})
		Vue.component("shopcar",{
			template:`
				<div>购物车</div>
			`
		})
		
		var vm = new Vue({
			el:"#app",
			data:{
				which:"home"
			}
		})
	</script>
```

### 插槽slot

插槽的一个理念就是混入父组件的内容到子组件中，在子组件中需要预留出来一个位置给父组件用。

没有名字的插槽叫单个插槽，有名字的插槽叫具名插槽



父组件模板的内容在父组件作用于内编译，子组件模板的内容在子组件作用域内编译。说白了，就是可以把父组件中的内容显示在子组件中，但是该内容还是属于父组件，可以由父组件进行控制，更加灵活通过子传父和父传子进行交互通信。

```html
	<body>
		<div id="app">
			<h1>插槽-slot</h1>
			<child>
				<div slot="hello">你好</div>
				<div slot="world">世界</div>
			</child>
		</div>
	</body>
	<script type="text/javascript">
		Vue.component("child",{
			template:`
				<div>
					child
					<slot name="hello"></slot>
					<slot name="world"></slot>
				</div>
			`
		})
		var vm = new Vue({
			el:"#app",
			data:{}
		})
	</script>
```

在新版的Vue中，通过v-slot这个指令来标明插槽，但是注意，v-slot只能用在组件中或者<template></template>中，不能用在普通标签上例如div。

此外，v-slot:a 这个指令可以简写成#a，如下面的例子中v-slot:left可以简写成#left

```html
	<body>
		<div id="app">
			<navbar>
				<template v-slot:left>
					<button>左边<i>icon</i></button>
				</template>
				<template v-slot:right>
					<button>左边<i>icon</i></button>
				</template>
			</navbar>
		</div>
	</body>
```

### 生命周期

生命周期函数也叫钩子函数，每个组件都有这八个生命周期函数

beforeCreate()

在这个周期中，Vue只是让这个组件初始化他的一些函数，状态等，但是此时这些依赖还没有做呢，状态的拦截还都没有做呢， 所以在这个周期中我们也不会做什么事情，在这个周期中我们访问状态，会发现访问不到，得到的是undefined。

created()

在这个周期中可以做一些初始化的操作，可以让我们对状态做一些修改，计算的工作。

beforeMount()

发生在dom挂载之前，此时还无法访问dom，还没有挂载好

mounted()

此时dom已经挂载完成了，可以访问dom，可以监听事件，可以ajax，可以设置定时器

updated()

获取到更新后的dom，依赖于dom操作的库，需要知道状态更新完，就可以在这里执行

destroyed()

在这里可以做一些取消定时器的操作，也可以做一些window事件的解绑操作等等

### 指令

之前的v-for、v-bind、v-if 等等都是Vue给定义的指令，我们也可以来定义自己的组件

通过Vue.directive()来定义指令，第一个参数是指令的名字，不加"v-"，如下边的"v-hello"，定义的时候只需要写hello

两个生命周期函数，第一个是inserted，dom元素插入的时候触发，第二个是update，dom元素更新的时候触发。

每个生命周期函数可以传两个参数，第一个参数是dom元素本身，第二个参数是指令传过来的值，如下边的传过来得color值。

```html
	<body>
		<div id="app">
			<div v-hello="color">111111</div>
			<div v-hello="'blue'">111111</div>
			<div v-hello="'green'">111111</div>
		</div>
		
	</body>
	<script type="text/javascript">
		Vue.directive("hello",{
			inserted(el,binding){
				console.log("插入",el);
				console.log(binding);
				el.style.color = binding.value;
			},
			
			update(el,binding){
				console.log("更新",el);
				console.log(binding);
				el.style.color = binding.value;
			}
		})
		var vm = new Vue({
			el:"#app",
			data:{
				color:"red"
			}
			
		})
		
	</script>
```

通过这个例子可以看到，inserted和update两个函数中的代码都是差不多的，都是把传过来的参数赋了一下值，Vue也给提供了一种简便的方式，如下：

```javascript
Vue.directive("hello",function(el,binding){
    el.style.color = binding.value
})
```

### nextTick

this.$nextTick是Vue提供的一个黑魔法，他在这里呢，能给你一个时间点，什么时间点呢？什么时候dom插入完成了，他会给你提供一个回调函数，你用这个的话就能实现一些特别需要依赖dom实例化完之后干的一些事，比如进行new Swiper的初始化

```html
<body>
		<div id="app">
			
			<div class="swiper-container kerwin">
				<div class="swiper-wrapper">
					<div class="swiper-slide" v-for="item,index in dataList">
						{{item}}
					</div>
				</div>
				<div class="swiper-pagination"></div>
			</div>
		</div>
		
	</body>
	<script type="text/javascript">
		
		var vm = new Vue({
			el:"#app",
			data:{
				dataList:[]
			},
			mounted(){
				setTimeout(()=>{
					this.dataList = ["aa","bb","cc","dd"]
					
					this.$nextTick(function(){
						console.log("我执行的比update都晚(๑′ᴗ‵๑)，并且只执行一次哦")
						var swiper = new Swiper('.swiper-container',{
							loop:true,
							pagination: {
							  el: '.swiper-pagination',
							  clickable: true,
							}
						})
					})
				},2000)
			},
			updated(){
				console.log("我是updated，我执行了。。。")
			}
		})
		
	</script>
```



### 虚拟dom

在Vue中，虚拟dom呀，其实是用js对象来模拟一个真实的dom节点，跟diff算法搭配，方便对比，最后找到哪些节点该复用，哪些节点不能复用，这是虚拟dom的一个解释

### 过滤器

相当于一个加工厂，将前面输入的结果进行处理，然后返回处理后的结果，有全局的和局部的，支持链式调用，和linux中的管道符一样

```html
	<body>
		<div id="app">
			<div style="border:1px solid #f40;width:200px" v-for="film in dataList">
				<img :src="film.img | handleImgSrc" style="width:200px;"/>
				<h3>{{film.nm}}</h3>
			</div>
		</div>
	</body>
	<script type="text/javascript">
		var vm = new Vue({
			el:"#app",
			data:{
				dataList:[],
			},
			mounted(){
				axios.get("film.json")
				.then(res=>{
					console.log(res.data);
					this.dataList = res.data.movieList;
				})
			},
			filters:{
				//通过过滤器将图片的src进行过滤，然后再返回，相当于一个加工厂
				handleImgSrc(path){
					return path.replace("w.h\/","");
				}
			}
		})
	</script>
```

### Vue cli

！安装Vue cli脚手架之前请先安装node

#### npm与yarn

https://cloud.tencent.com/developer/article/1617955

这两个都是包管理工具，yarn比npm的优势

- 加入了缓存机制，离线安装，速度更快
- 加入算法校验包完整性，更安全
- 使用详细、简洁的锁文件格式和明确的安装算法，Yarn 能够保证在不同系统上无差异的工作，更可靠。

```
nrm ls            //查看源
nrm use taobao	  //切换为淘宝源

yrm ls			  //查看源
yrm user taobao   //切换为淘宝源
```

#### 创建项目

```
执行如下命令，电脑上只需要安装一次就可以
npm install -g @vue/cli
查看vue cli版本
vue --version
创建项目
vue create .			//在当前目录中下载依赖的包
vue create myapp		//在当前目录下创建myapp目录，进入到myapp目录中下载依赖的包

启动项目
yarn serve
```

### 单页面vs多页面

|                     | 单页面应用SPA                                                | 多页面应用MPA                                |
| ------------------- | ------------------------------------------------------------ | -------------------------------------------- |
| 组成                | 一个外壳页面和多个页面片段组成                               | 多个完整页面构成                             |
| 资源共用（css，js） | 共用，只需要在外壳部分加载                                   | 不公用，每个页面都需要加载                   |
| 刷新方式            | 页面局部刷新或更改                                           | 整页刷新                                     |
| url模式             | a.com/#/pageone<br />a.com/#/pagetwo                         | a.com/pageone.html<br />a.com/pagetwo.html   |
| 用户体验            | 页面片段间的切换快，用户体验良好                             | 页面切换加载缓慢，流畅度不够，用户体验比较差 |
| 转场动画            | 容易实现                                                     | 无法实现                                     |
| 数据传递            | 容易                                                         | 依赖url传参，或者cookie，localStorage等      |
| 搜索引擎优化（SEO） | 需要单独方案，实现比较困难，不利于SEO检索，可利用服务器端渲染（SSR优化） | 实现方法简易                                 |
| 适用范围            | 高要求的体验度，追求界面流畅的应用                           | 适用于追求高度支持搜索引擎的应用             |
| 开发成本            | 较高，常需借助专业的框架                                     | 较低，但页面重复代码多                       |
| 维护成本            | 相对容易                                                     | 相对复杂                                     |

### Vue路由

Vue路由就是让url路径和每个要显示的组件关联起来，当用户访问不同的url时，可以给用户展示对应的内容

#### 一级路由配置

