



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

几个API接口

>  https://autumnfish.cn/api/joke/list?num=3
>
> https://autumnfish.cn/api/user/reg

### axios

> 官网地址：https://github.com/axios/axios#readme

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

**axios拦截器**

> https://github.com/axios/axios#interceptors

可以让你在请求之前干点什么事，请求完成之后再干点什么事。有什么用呢？在【卖座电影】项目中，为了提高用户体验，我们需要在每个请求之前给用户一个提示“加载中...”，可以用vant组件库的Toast来实现，这里就可以用到axios的拦截器，

```js
// 添加一个请求拦截器
axios.interceptors.request.use(function (config) {
    // Do something before request is sent
    return config;
  }, function (error) {
    // Do something with request error
    return Promise.reject(error);
  });

// 添加一个响应拦截器
axios.interceptors.response.use(function (response) {
    // Any status code that lie within the range of 2xx cause this function to trigger
    // Do something with response data
    return response;
  }, function (error) {
    // Any status codes that falls outside the range of 2xx cause this function to trigger
    // Do something with response error
    return Promise.reject(error);
  });
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

#### 路由原理

hash路由：location.hash切换    window.onhashchange 监听路径的切换

history路由：history.pushState切换   window.onpopstate 监听路径的切换

#### 一级路由配置

location.href可以获得完整的访问路径

location.hash可以获得hash值

通过在router目录下的index.js文件进行路由的配置

```javascript
import Vue from 'vue'
import VueRouter from 'vue-router'
import Film from "../views/Film.vue"
import Cinema from "../views/Cinema.vue"
import Center from "../views/Center.vue"

Vue.use(VueRouter)    //在Vue中全局注册路由

//定义一个变量下边用
const routes = [
  {
    path:"/film",
    component:Film
  },
  {
    path:"/center",
    component:Center
  },
  {
    path:"/cinema",
    component:Cinema
  }
]


//实例化一个VueRouter对象
const router = new VueRouter({
  routes:routes
})


//将该对象导出去，在main.js中需要导入该模块
export default router

```

#### 声明式导航

```html
	<nav>
        <ul>
            <router-link to="/film" tag="li" active-class="kerwinactive">电影</router-link>
            <router-link to="/cinema" tag="li" active-class="kerwinactive">影院</router-link>
            <router-link to="/center" tag="li" active-class="kerwinactive">我的</router-link>
        </ul>
    </nav>
```

#### 重定向

需要在router目录下的index.js文件进行路由的配置

下面这条路由表示将任意的请求重定向到/film路径，优先级是最低的，当所有路由都匹配不到的时候才匹配这条路由

```
  {
    path:"*",
    redirect:"/film"
  }
```

#### 嵌套路由

一级路由下边包含另一层路由

```javascript
  {
    //一级路由
    path:"/film",
    component:Film,
    //嵌套路由
    children:[
      {
        path:"nowplaying",      //简写方式，注意不加前面的/
        component:Nowplaying
      },
      {
        path:"/film/comingsoon",	//完整写法，从一级开始写到当前
        component:Comingsoon
      },
      {
        path:"",					//因为已经进入到/film路径了，所以将路径写为空然后直接重定向到/film/nowplaying，这样打开页面直接跳到这儿
        redirect:"/film/nowplaying"
      }
    ]
  },
```

#### 编程式导航

声明式导航就是通过a链接href属性来跳

编程式导航就是通过location.href来跳



直接通过this.$router对象来实现跳转

```javascript
	methods:{
        handleClick(filmId){
            console.log(filmId);
            // location.href=`#/detail`
            // location.href=`#/detail${filmId}`
            this.$router.push(`/detail/${filmId}`)		//通过编程式导航跳转到/detail/{id}页面
        }
    }
```

#### 动态路由

当通过编程式导航跳转到detail页面时，有携带过来的电影ID，此时就需要动态路由来进行处理

在router目录下的index.js文件进行配置。

/detail/:filmId表明这是一个动态路由，通过" : "来占位，此路由可以匹配/detail/111、/detail/222、/detail/333、/detail/444等路由

如果写成/detail/:filmId/a，则表示匹配/detail/111/a、/detail/222/a、/detail/333/a等路由

```
  {
    path:"/detail/:filmId",
    component:Detail
  },
```

接下来就来到detail页面，在这个页面中我们需要获取到这个动态id才行啊，可以通过location.hash来获取，当然，Vue肯定也给我们想到了

在detail页面中，可以直接通过this.$route来获取路径对象，注意是$route而不是路由对象$router。通过这个对象我们可以获取到参数

this.$route.params.filmId

注意哦，filmId就是上面我们创建动态路由时传的那个参数昂！

```javascript
 mounted(){
        console.log("利用传过来的动态ID","向后端发送ajax请求");
        console.log(this.$route.params.filmId);
    }
```



#### 路由命名

在编程式导航中，我们通过this.$router.push(/detail/${filmId})来进行跳转，此外，我们还可以给路由起名字，然后直接通过

this.$router.push({name:"路由名字",params:{filmId:id}})来进行跳转

首先在router目录下的index.js文件中进行配置，在这里我们将这个路由的名字起为"kerwinDetail"，这个是可以随便起的

```javascript
 {
    path:"/detail/:filmId",
    component:Detail,
    name:"kerwinDetail"
  },
```

然后进行路由的跳转

```javascript
    methods:{
        handleClick(filmId){
            console.log(filmId);  
            this.$router.push({
            	name:"kerwinDetail",
            	params:{
            		filmId:filmId
            	}
            })
        }
    }
```

#### query方式传参

如果不想用动态路由的方式，也可以有另外一种选择，query方式进行参数传递。

首先，在路由中进行路由的设置

```
 {
    path:"/detail",
    component:Detail
  },
```

这样看就和普通的路由没啥区别，也不是动态路由

在传参的时候要写成如下的格式

```javascript
 methods:{
        handleClick(filmId){
            console.log(filmId);
            this.$router.push(`/detail?filmId=${filmId}`)
        }
    }
```

在detail页面中接收参数的时候就成了在query中获取了

```javascript
 mounted(){
        console.log("利用传过来的动态ID","向后端发送ajax请求");
        console.log(this.$route.query.filmId);
    }
```

#### 路由模式

Vue给提供了两种路由模式，一种是带#号的，这种叫做hash模式，也是默认的模式

另外一种是history模式，需要在index.js配置，如下：

```javascript
const router = new VueRouter({
  mode:"history",
  routes:routes
})
```

`vue-router` 默认 hash 模式 —— 使用 URL 的 hash 来模拟一个完整的 URL，于是当 URL 改变时，页面不会重新加载。

如果不想要很丑的 hash，我们可以用路由的 **history 模式**，这种模式充分利用 `history.pushState` API 来完成 URL 跳转而无须重新加载页面。

```js
const router = new VueRouter({
  mode: 'history',
  routes: [...]
})
```

当你使用 history 模式时，URL 就像正常的 url，例如 `http://yoursite.com/user/id`，也好看！

不过这种模式要玩好，还需要后台配置支持。因为我们的应用是个单页客户端应用，如果后台没有正确的配置，当用户在浏览器直接访问 `http://oursite.com/user/id` 就会返回 404，这就不好看了。

所以呢，你要在服务端增加一个覆盖所有情况的候选资源：如果 URL 匹配不到任何静态资源，则应该返回同一个 `index.html` 页面，这个页面就是你 app 依赖的页面。

#### 路由拦截

全局路由守卫

适用多个组件需要做拦截操作的情况，比如个人中心、订单、金额、卡片等页面

在router目录下的index.js进行路由拦截的配置

```js
//实例化一个VueRouter对象
const router = new VueRouter({
  routes:routes
})

//全局路由守卫
router.beforeEach((to, from, next)=>{
  const auth = ["/center","/order","/money","/card"]

  if(auth.includes(to.fullPath)){
    console.log("验证token")
    if(!localStorage.getItem("token")){
      next("/login")   //next("/ligon")加参数表示跳转到指定的地址
    }else{
      next()		
    }
  }else{
    next()			//next()不加参数表示直接放行
  }
})


//将该对象导出去，让main.js用
export default router
```

局部路由守卫

适用于单个组件需要被拦截的情况，比如只有个人中心需要被拦截，那就只在Center组件中进行配置进行

在views目录下的Center.vue中配置如下：

```js
//局部路由守卫
<script>
export default {

    beforeRouteEnter(to,from,next){
		if(!localStorage.getItem("token")){
          next("/login")   //next("/ligon")加参数表示跳转到指定的地址
        }else{
          next()		   //next()不加参数表示直接放行
        }
    },
    mounted(){
        
    }
}
</script>
```

#### 路由懒加载

单个页面中，对路由懒加载的需求还是多一点的

当我们把单页面应用的项目写完之后，有一百多个组件，这时候项目打包只生成两个个js文件，

一个app.js，我们写的组件中的js都在这里，另一个是chunk-vendors.js，这里面是放的vue以及其他依赖的js代码

这样用户打开页面的时候就需要把1M多的js下载完才能看到页面，这样的用户体验是很不好的，所以最好进行代码片段分割，也就是懒加载，把每个组件打包成对应的一个js文件，需要这个组件的时候再动态的去加载，这样用户打开首页的时候只需要加载首页需要的js就行，大大提高打开网页的速度。

方法如下：

在router目录下的index.js中进行配置，假设我们需要把Login组件设置成懒加载

```js
import Vue from 'vue'
import VueRouter from 'vue-router'
//import Login from "../views/Login.vue"           //在这里就不需要加载Login.vue了，
import Center from "../views/Cente.vue"
import Cinema from "../views/Cinema.vue"

Vue.use(VueRouter)    //在Vue中注册路由

//定义一个变量下边用
const routes = [
  {
    path:"/center",
    component:Center
  },
  {
    path:"/cinema",
    component:Cinema
  },
  {
    path:"/login",
    component:()=>import("../views/Login.vue")			//在这里进行Login.vue的动态加载
  }
]
```

此外也可以按组进行代码的分割，比如我们想要把Login和Center放到一个"kerwin-group"组里边，这样Login.vue和Center.vue就会被编译到一个js文件中。

```js
import Vue from 'vue'
import VueRouter from 'vue-router'
//import Login from "../views/Login.vue"           //在这里就不需要加载Login.vue了，
//import Center from "../views/Cente.vue"
import Cinema from "../views/Cinema.vue"

Vue.use(VueRouter)    //在Vue中注册路由

//定义一个变量下边用
const routes = [
  {
    path:"/login",
    component:()=>import(/* webpackChunkName: "kerwin-group" */ "../views/Login.vue")			//在这里进行Login.vue的动态加载
  },
  {
    path:"/center",
    component:()=>import(/* webpackChunkName: "kerwin-group" */ "../views/Center.vue")
  },
  {
    path:"/cinema",
    component:Cinema
  }
]
```

### 反向代理

我们通过ajax向后端获取数据的时候有三种情况

1、后端配置好cors，我们可以通过axios去获取数据，这需要后端在响应头中配置好`Access-Control-Allow-Origin:*` 允许任何地址访问

```js
//我们的请求地址[魅力惠]：http://www.mei.com/appapi/home/eventForH5
methods:{
    getData(){
      axios.get("http://www.mei.com/appapi/home/eventForH5").then((res)=>{
        console.log(res.data)
      })
    }
  }
```

2、后端没有配置cors，我们要自己用代理去请求数据，Vue提供给我们了方法，就是在vue.config.js中进行配置

我们的浏览器去跨域请求数据的时候，出于安全的考虑，会阻止我们跨域发送请求，但是服务器和服务器之间不会，vue.config.js就是利用了node。

当我们向目标地址发送请求时，很明显这是跨域的，无法成功，这时候只要我们配置了反向代理，那么我们的请求将会发送给node，然后node帮我们去请求目标地址，他们中间是不会有跨域限制的，然后node再将请求到的数据发送给我们的浏览器，这样就顺利完成了我们的请求。

在vue.config.js中配置如下：

```js
//我们的请求地址[猫眼电影]：https://m.maoyan.com/ajax/movieOnInfoList?token=&optimus_uuid=D8ECA0F06EE311EB8EA95FD7FC141CDDF355EB6FA6294E6C9A0DBA65F41D99E6&optimus_risk_level=71&optimus_code=10

module.exports = {
    devServer:{
        proxy:{
            '/ajax':{			//表示只要我们发送的请求中有/ajax,就会被代理，然后node就会帮我们做处理
                target:"https://m.maoyan.com",
                changeOrigin:true
            }
        }
    }
}
```

```js
methods:{
    getData(){
      let url = `/ajax/movieOnInfoList?token=&optimus_uuid=D8ECA0F06EE311EB8EA95FD7FC141CDDF355EB6FA6294E6C9A0DBA65F41D99E6&optimus_risk_level=71&optimus_code=10`
      axios.get(url).then((res)=>{
        console.log(res.data)
      })
    }
  }
```



> 参考vue.config.js官方文档  https://cli.vuejs.org/zh/config/#devserver-proxy

3、虽然目标网站已经配置好了cors，但是为了不让数据被别人获取，所以又在请求投中设置了几个字段，要求请求的时候带上，这时候就需要我们自己去找到这几个特殊的字段，并配置好。

```js
methods:{
    //我们的请求地址[卖座电影]：https://m.maizuo.com/gateway?cityId=440300&pageNum=1&pageSize=5&type=1&k=9350393    
    getData(){
      // axios的完整写法，里边支持我们设置请求头headers
      axios({
        url:"https://m.maizuo.com/gateway?cityId=440300&pageNum=1&pageSize=5&type=1&k=9350393",
        headers:{
          'X-Client-Info': '{"a":"3000","ch":"1002","v":"5.0.4","e":"1612578131297645528580097"}',
          'X-Host': 'mall.film-ticket.film.list'
        },
        // method:"get"			//axios默认的请求方法就是get，不写这个也可以
      }).then((res)=>{
        console.log(res);
      })
    }
  }
```

### Vuex

#### 同步（组件通信）

适用于将整个项目的共享状态放到Vuex中，由他来集中管理。更细节来说，他能够帮我们承担非父子通信的能力，但绝对不是只能做非父子通信

如何用？以我们的【卖座电影】项目为例：我们需要在CIty组件中选择对应的城市，然后在Cinema组件中将选中城市的影院数据显示出来。这其中就涉及到了组件通信。

首先在router目录下的index.js中进行配置

```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

// 创建Store对象
export default new Vuex.Store({
  // state表示状态，这里设置了两个状态，一个cityId，一个cityName
  state: {
    cityId:'310100',
    cityName:'北京'		//初始值我们设置的是“北京”
  }
})

```

记得需要在项目的主配置文件main.js进行注册

```js
import Vue from 'vue'
import App from './App.vue'
import router from './router/index.js'
import store from './store'		//引入store

Vue.config.productionTip = false

new Vue({
  router,   //全局注册，可以通过this.$router访问到
  store,    //可以通过this.$store访问到
  render: h => h(App)
}).$mount('#app')
```

接着我们在CIty组件中进行状态的修改

```js
handleChangePage(name){
    //将当前选中的城市ID和城市名字传出去
    this.$store.state.cityName = name		//进行状态的修改,比如这里我们修改为“上海”
    this.$router.back()
}
```

最后在Cinema组件中获取值的时候可以看到值已经被修改了

```js
<van-nav-bar title="影院" @click-left="onClickLeft" @click-right="onClickRight">
    <template #left>
        {{$store.state.cityName}}		//这里获取到的值就是“上海”
        <van-icon name="arrow-down" color="#000" size="10"/>
    </template>
    <template #right>
        <van-icon name="search" size="18" />
    </template>
</van-nav-bar>
```

上面的例子能看出来，公共资源很方便，大家可以随时去拿，随时去修改他，但是如果不加以严格管控，就会出现不经意或恶意的破坏，一旦出现了问题，我们可能调查谁导致的，就很难了。那么，如何严格有效的把控我们的公共办公环境呢？有一种非常好的方案就是如果你要开放办公，没有问题，但是要在办公区域安装一个摄像头，这个摄像头就是mutations。

访问的话没有问题，修改的话，切记切记，不要直接去修改，那么怎么修改呢？可以通过 `this.$store.commit()` 提交到我们的store中去修改，也就是说，我们把所有的修改都集中在store中去修改，这样即使有错误，那么错误也只会发生在store中。

这里的commit方法是一个固定的方法，第一个参数传入的是方法名字，第二个参数传的是值，比如 `this.$store.commit('changeCityName',name)` 表示调用$store中mutations中的changeCityName这个方法，并将name值传过去。

然后在store目录下的index.js中进行配置mutations

```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

export default new Vuex.Store({
  state: {
    cityId:'310100',
    cityName:'北京'
  },
  //集中式的修改状态
  mutations: {
    changeCityName(state,name){
      state.cityName = name
    }
  }
})

```

注意：$store只是存在内存中，每次刷新页面都会恢复为初始的值

在【卖座电影】项目中，还有一个场景也能用到Vuex的状态管理，那就是底部的Tabbar导航栏，并不是所有页面都显示这个导航栏，在影片详情页中就不需要显示这个Tabbar，我们可以设置一个全局的状态来管控Tabbar的显示。

首先在store目录下的index.js中添加控制Tabbar显示与否的状态

```js
state: {
    cityId:'310100',
    cityName:'北京',
    isTabbarShow:true		//控制Tabbar显示与否的状态，初始值为true，表示默认是显示的
}
```

当点击某一个电影后，跳转到详情页，这时候会加载Detail组件，当该组件挂载完之后，我们将值设为false，这里可以直接设置`this.$store.state.isTabbarShow=false` 但是，正如上面说的那样，不能让他们随意修改，得交给中央，让中央进行改变，所以这里用`this.$store.commit("hide")` ，当离开影片详情页后，Detail就得销毁了，在销毁之前，将值设为true，让Tabbar可见，

```js
mounted(){
    //通过$state中的isTabbarShow让底部导航栏隐藏
    this.$store.commit("hide")
    //...
},
//在详情页销毁之前记得要把底部导航栏的状态设为true
beforeDestroy(){
     this.$store.commit("show")
}
```

在mutations中定义这两个方法

```js
//集中式修改状态
mutations: {
    //...
    //这里不需要传值过来，只需要将state传过来就行
    hide(state){
    	state.isTabbarShow = false
    },
    show(state){
    	state.isTabbarShow = true
    }
}
```

#### 异步（后端数据快照）

想象这样一个场景，两个组件A和B都需要向后端请求数据，巧的是这两个组件请求的数据是一样的，那么有必要让每个组件都向后端请求吗？有没有更好的办法？Vuex的异步提供了解决办法。他的思路是当A请求数据时，先交给Vuex，让他去向后端请求，Vuex请求完之后给A组件，同时Vuex中也保存了一份数据，这时候B给Vuex发请求，说我要xxx数据，请你给我去向后端请求一下，Vuex说我这就有，直接给你了。这样数据就直接给B了，不用再向服务器发请求了。

在【卖座电影】项目中，Cinema组件需要请求某一个城市的电影院信息，影院搜索组件也需要请求相同的数据，这样，就可以用Vuex来实现。

![vuex异步流程图](https://vuex.vuejs.org/vuex.png)



### BetterScroll

> 官方地址：​https://better-scroll.github.io/docs/zh-CN/guide/

一款滚动插件，用于列表太长的显示

```node
# 安装
yarn add better-scroll

# 引入
import BetterScroll from "better-scroll"
```

使用方法比较简单，只需要在需要滚动的盒子上添加一个类，然后初始化这个类就可以用，在这里我们需要初始化cinema这个盒子

```html
<template>
    <div class="cinema" :style="{height:height}">
        <ul>
            <li v-for="item in dataList" :key="item.cinemaId">
                {{item.name}}
                <p>{{item.address}}</p>
            </li>
        </ul>
    </div>
</template>
```

当数据请求完成并且dom挂载完成之后，开始初始化BetterScroll插件，这里用到$nextTick()这个函数，这个函数在dom渲染完成之后执行

```js
data(){
    return{
        dataList:[],
        height:0
    }
},
mounted(){

    //这里
    this.height = document.documentElement.clientHeight-50+"px";
    
    //用封装完axios的http模块进行数据请求
    http({
        url:'/gateway?cityId=310100&ticketFlag=1&k=6714633',
        headers:{
            'X-Host': 'mall.film-ticket.cinema.list'
        }
    })
        .then(res=>{
        console.log(res)
        this.dataList = res.data.data.cinemas

        //当数据请求完成并且dom挂载完成之后，开始初始化BetterScroll插件，这里用到$nextTick()这个函数，这个函数在dom渲染完成之后执行
        this.$nextTick(()=>{
            new BetterScroll(".cinema",{
                //添加滚动条
                scrollbar:{
                    fade:true
                }
            });
        })
    })      
}
```

如果需要添加滚动条，需要修正一下滚动条的位置，在.cinema盒子上添加position属性，值为relative

```css
.cinema{
    overflow: hidden;
    position: relative;		//添加这个属性修正以滚动条的位置
}
```

### 小技巧

数组合并的方法，利用ES6中的展开运算符

```js
var a=[1,2,3]
var b=[4,5,6]
var c = [...a,...b]
```



