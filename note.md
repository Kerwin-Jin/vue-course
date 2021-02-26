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
				handleClick(money){
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









