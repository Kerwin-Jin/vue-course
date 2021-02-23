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

