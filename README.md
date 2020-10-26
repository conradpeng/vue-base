# 概念知识

## mvvm

<br>

> 即  `Model - View - ViewModel `

- Model 指数据访问层，即数据模型，通常是完整的json数据，是后端应用通过ajax传递至前端的应用程序

- View 即视图，用户可以只管看到的结构布局和UI

- ViewModel 即视图模型，内置绑定器，其本质是DOM监听与DATA绑定的结合，进行dom操作，因此数据变化的同时，页面也会同步刷新变化

## SPA

- 多个页面公用一个视图，通过传递不同的数据，使页面显示不同的结构与内容

## 生命周期
    
1. 生命周期钩子函数

    ``` javascript
    //初始化后执行
    created: function(){}
    //修改并更新渲染后执行 (data 中的数据变化时)
    updated: function(){}
    ```


<br>

# 基本功能

## options

- 实例化

    ``` javascript
    Vue.config.productionTip = false;//关掉提示你目前是开发版的警告信息
    let vm = new Vue({
        //绑定容器
        el: '#app'

        //数据
        data(){
            return {

            }
        }
    })
    ```
- 定义数据

    ``` javascript
    data(){
        return {
            //定义数字
            num: 23,
            //定义字符串
            type: 'vue test',
            //定义数组
            userList: [
                {
                    name:'张三',
                    age:23, 
                    sex:'男'
                },
                {
                    name:'李四',
                    age:25,
                    sex:'男'
                },
                {
                    name:'小花',
                    age:33,
                    sex:'女'
                }
            ],
            //定义对象
            userListObj: {
                name:'张三', age:23, sex:'男'
            },

            userHtml:'<p>fsdf</p>'
        }
    }
    ```

## 文本插值

- 文本插值

    双花括号进行文本插值<br>
    模版插值不能作用域 HTML 元素的属性上，使用 `v-bind` 指令<br>
    在模板语法中，支持 `JavaScript` 表达式，比如：运算、函数等,不支持语句
    ``` html
    <div id="app"> {{message}} </div>

    <div>{{num + 1}}</div> 
    <div>{{true ? '真' : '假'}}</div> 
    <div>{{message.split('')}}</div>
    ```

- html渲染 `v-html`
    > 注意不要给用户的数据 提供 `v-html`插值， 会造成xss攻击
    ``` html
    <div>
        {{userHtml}}
    </div> 

    ↓↓↓

    <p>fsdf</p>
    ```

    ``` html
    <div v-html="userHtml"></div>

    ↓↓↓

    fsdf
    ```

## 指令

- 循环 `v-for`
    ``` html
    <!-- v-for数组遍历 -->
    <ul>
        <li v-for="user,index in userList">
        {{index}}--{{user.name}}:{{user.age}}
        </li>
    </ul>

    <!-- v-for遍历对象 -->
    <ul>
        <li v-for="user,key,index in userListObj" :key="index">
        {{index}}--{{key}}:{{user}}
        </li>
    </ul>
    <!-- 可以使用template进行区块渲染 -->
    <ul>
        <template v-for="city in cities">
            <li>{{city.name}}</li>
            <li>--------</li>
        </template>
    </ul
    ```
    ``` html
    <ul>
        <li>0--张三:23</li>
        <li>1--李四:25</li>
        <li>2--小花:33</li>
    </ul>
    <ul>
        <li>0--name:张三</li>
        <li>1--age:23</li>
        <li>2--sex:男</li>
    </ul>
    ```
- 条件 `v-if` `v-show` <br>
    `v-if` 是直接不渲染这一段html <br>
    `v-show` 则是 `display` 隐藏这一段html<br>
    `v-show` 不支持 `<template>` 和 `v-else`
    ``` html
    <div v-if="showDiv">ssss</div>
    <div v-show="showDiv">dddd</div>
    <button @click="showDivBt()">aaaa</button>
    ```

    ``` javascript
    methods: {
        showDivBt(){
            this.showDiv = !this.showDiv;
        }
    },
    ```
    也可以使用 `if-else` 语句
    ``` html
    <div v-if="flag"> {{message}} </div> 
    <div v-else-if="flag === false"> else-if </div>
    <div v-else> else </div>
    ```

- `v-for` 和 `v-if` 结合时的优化
    ``` html
    <!-- for的优先级高于if，因此这种写法会在每次循环时都进行一次if判断 -->
    <li v-for="(item, index) in cities" :key="index" v-if="flag">{{item.name}}</li><br>
    <!-- 优化方法，使用template -->
    <template v-if="flag">
        <li v-for="item in cities">
            {{item.name}}
        </li>
    </template>
    <br>
    ```
    ``` html
    <!-- 同理，筛选不用的数据时，也会在每次循环都判断一次 -->
    <li v-for="item in cities" v-if="item.count != 1">{{item.name}}</li><br>
    <!-- 推荐使用属性计算，提前处理好想要的数据 -->
    <li v-for="item in southcities">{{item.name}}</li><br>
    ```
    ``` javascript
    data() {
        return {
            flag: true,
            cities: [
                {count: 1, name: '北京'},
                {count: 2, name: '上海'},
                {count: 3, name: '广州'}
            ]
        }
    },
    computed: {
        southcities:function(){
            const newc = JSON.parse(JSON.stringify(this.cities));
            return newc.splice(1,2);
        }
    },
    ```

- `v-bind` <br>
    因为插值无法作用于`html`属性里，所以可以使用`v-bind`来绑定数据<br>
    单向的绑定，修改页面的内容不会影响  `model` 里的值
    ``` html
    <!--任何属性都可以绑定-->
    <!-- 图片绑定 -->
    <img v-bind:src="user.img" alt="">

    <!-- 单选框绑定 -->
    男：<input type="radio" name="sex" v-bind:checked="user.sex == 1"><br>
    女：<input type="radio" name="sex" v-bind:checked="user.sex == 2"><br>

    <!-- 复选框绑定 -->
    是否记住：<input type="checkbox" name="rem" v-bind:checked="user.remember"><br>

    <!-- 下拉框绑定 -->
    <select name="" id="">
        <option v-for="shi in area" v-bind:value="shi.num" v-bind:selected="user.area == shi.num">{{shi.content}}</option>
    </select>
    ```
- 动态属性

    `v-bind:[attrName]`，通过数据对象属性赋值<br>
    `attrName` 即为属性名，在data里定义，attrName可以变化
    ``` javascript
    data:{
        color: 'red',
        key: 'class'
    }
    ```
    ```  html
    <div v-bind:[key]="color"></div>
    <div v-bind:class="color"></div>
    ```

- `v-model` <br>
    双向绑定，修改页面的内容会使 `model` 的值同步变化

    > 绑定单选框时，`v-model`的本质是判断当前的value是否与绑定的值一致，一致时选中
    ``` html
    <!-- 文本框的绑定 -->

    <!-- 单选框的绑定 -->
    男：<input type="radio" v-bind:name="'sex'+index" value="1" v-model="user.sex"><br>
    女：<input type="radio" v-bind:name="'sex'+index" value="2" v-model="user.sex"><br>
    ```
    > 绑定复选框时，`v-modle` 的本质是查看当前绑定的值是否为  `true` (非 `bool` 型会转化为 `bool` 型)，为 `true` 时选中
    ``` html
    <!-- 复选框的绑定 -->
    <input type="checkbox" v-bind:name="'rem'+index" v-model="user.remember">
    ```
    >绑定下拉框时，`v-model` 的本质是查看每一个`option` 的 `value` 值是否与绑定的值相等，如果相等，会给该 `option` 添加  `selected`
    ``` html
    <!-- 下拉框的绑定 -->
    <select name="area" v-model="user.area">
        <option v-bind:value="shi.num" v-for="shi,index in area">{{shi.content}}</option>
    </select>
    ```
    ``` javascript
    sex: [{num:1, type:"男"},{num:2, type:"女"}],
    area: [{num:1, content:"北京"},{num:2, content:"西安"},{num:3, content:"上海"}],
    userList : [
        {
            name:"张三",
            age:"20",
            sex:"1",
            img:"https://dss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=682895161,2056544872&fm=111&gp=0.jpg",
            remember: 1,
            area:"3"
        },
        {
            name:"小花",
            age:"22",
            sex:"2",
            img:"https://dss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=3999162594,1780997618&fm=111&gp=0.jpg",
            remember: 0,
            area:"2"
        }
    ],
    ```

    `v-model` 修饰符

    > `.lazy`<br>
    > 有时我们不需要数据绑定的时候逐个触发，可以使用 `.lazy` 修饰符，比如绑定 `input` 时，可以让数据随着每输入一个字符就改变，可以使其在`input` 失去焦点时再改变

    > `.number`<br>
    > 使用 `.number` 修饰符将输入的内容转换为 number 数值类型

    > `.trim`<br>
    > 使用 `.trim` 清理掉内容左右的空格

## 监听和计算属性

- 监听
    > `watch` 用于响应数据的变化，当需要在数据变化时进行异步或者其他开销较大的操作时使用比较好,可以获取该数据的新旧值，进行计算或者其他操作

    > 类似于传统 `js` 的事件响应 ( 绑定数据的改变事件 )
    ``` html
    <input type="range" name="" id="" v-model="fontSize">
    ```
    ``` javascript
    data() {
        return {
            fontSize: 23
        }
    },
    watch: {
        //侦听的方法名等于所绑定的数据名称
        fontSize: function(newValue, oldValue){
            if(newValue > 50){
                this.fontSize = oldValue
            }
        }
    },
    ```
    > 监听存在一个弊端，因为监听的方法名和数据名一致，因此不能深入监听一个对象或者数组的内部数据，此时可以添加一个计算属性，通过计算属性获取对象内部的值，再监听计算属性即可
    
- 计算属性 <br>
    >计算属性是<b> `具有缓存的`</b>，当值没有改变时，不会重新执行方法，而去使用缓存。<br>

    > 类似于传统 `js` 的事件响应 ( 想要获取的结果所依赖的数据的改变事件 ) +全局变量的做法

    >而方法则是每次访问时都会重新计算一次<br>
    方法是主动调用才会执行的
    ``` html
    <input type="text" name="firstName" id="" v-model="firstName">
    <input type="text" name="lastName" id="" v-model="lastName">
    {{fullName}}
    ```
    ``` javascript
    data() {
        return {
            firstName: "jack",
            lastName: "jho",
        }
    },
    computed: {
        //计算属性的方法名自定义
        fullName : function(){
            return this.firstName + ' ' + this.lastName;
        }
    }
    ```
    > 计算属性具有 `get` 和 `set` 两个方法，默认情况下执行 `get` 方法<br>
    > 计算属性得出的结果可以看作一个单独的数据，可以对这个数据进行重新赋值，此时就需要使用 `set` 方法去操作

    ``` html
    {{fullname}}
    <button @click="change">change</button>
    ```
    ``` javascript
    computed: {
        fullname:{
            get(){
                return this.firstname + this.lastname;
            },
            set(value){
                this.firstname = value[0];
                this.lastname = value[1];
            }
        }
    },
    methods: {
        change(){
            this.fullname = '张三'
        }
    },
    ```

- 侦听、属性计算、方法 的区别
    > 侦听是侦听一个 `data` 中的数据，当这个数据进行变化时可以获得该参数的新旧值，并进行计算或者其他操作,
    因此用于 侦听一个数据的变化，当该数据变化时，处理与其相关的数据的变化 ( 该数据影响多个数据，主动 )
    
    > 而属性计算的目的是获取一个结果，该结果由多个数据构成 ( 多个数据影响当前获取的值，被动 )

    > 方法即事件绑定或者普通函数


## 事件

- `v-on`
    > 可以绑定的事件类型和 `js` 一样，
    `vue` 支持一些事件修饰符去处理一些 `DOM` 事件细节
    只列举常用<br>
    `.stop` 防止事件冒泡<br>
    `.prevent` 阻止默认行为<br>
    `.capture` 与事件冒泡方向相反，事件捕获由外到内<br>
    `.self` 只触发自己范围内的事件，不会触发子元素<br>
    `.once` 只触发一次

    ``` html
    <button v-on:click="count++">add 1</button>
    <button v-on:click="add">add 1</button>
    <div>{{count}}</div>
    ```

    ``` javascript
    methods: {
    add: function(){
        this.count ++;
    }
    ```

    > 需要注意键盘事件，传统 `js` 的做法是获取案件的键值进行判断和处理，`vue` 中则是使用了一些修饰词 [参考](https://cn.vuejs.org/v2/guide/events.html#%E6%8C%89%E9%94%AE%E7%A0%81)
    
    > `.enter / .tab /.delete / .esc / .space / .up / .down / .left / .right`

    ``` html
    <form action="">
        <input type="text" name="name" id="" v-model="newUser.name">
        <input type="text" name="age" id="" v-model="newUser.age">
        <input type="text" name="img" id="" v-model="newUser.img" v-on:keyup.enter="onSubmit">
    </form>
    ```
    > 同样的也存在系统修饰键和鼠标修饰符 [参考](https://cn.vuejs.org/v2/guide/events.html#%E7%B3%BB%E7%BB%9F%E4%BF%AE%E9%A5%B0%E9%94%AE)

    <br>

## 过滤器

> 过滤器常用于对字符进行处理

- 全局过滤器
    ``` html
    <div id="app">
        {{user.content|msgFormat}}
    </div>
    ```

    ``` javascript
    Vue.filter('msgFormat', function(str){
        return str.replace('tmd', '****');
    });
    ```
- 私有过滤器
    ``` html
    <div id="app">
        {{user.name}}
        {{user.sex|sexFormat}}
    </div>
    ```

    ``` javascript
    filters :{
        sexFormat : function(sex){
            return sex == 1 ? '男' : '女';
        }
    }
    ```

## 自定义指令

- 全局指令
    ``` javascript
    //全局指令
    Vue.directive('red', {
        //绑定时触发，且只会触发一次 (可以理解为初始化时即绑定)
        bind: function(el, val){
            //css 中的 - 关联的属性，需要改写成驼峰式
            el.style.backgroundColor = 'red';
        },
        //被绑定的元素被插入DOM时调用
        inserted: function(el){

        },
        //元素更新时触发
        updated: function(el){

        }
    });
    ```

- 私有指令
    ``` javascript
    directives: {
        'color' : {
            bind: function(el, param){
                el.style.backgroundColor = param.value;
            },
            inserted: function(){

            }
        }
    }
    ```

- 使用
    ``` html
    <div v-red>ssss</div>
    <div v-color="'blue'">dddd</div>
    ```

## 变异方法
- 变异方法<br>

    `push()` 接收任意数量的参数，把它们逐个添加到数组末尾，并返回修改后数组的长度

    `pop()` 从数组末尾移除最后一项，减少数组的length值，然后返回移除的项

    `shift()` 移除数组中的第一个项并返回该项，同时数组的长度减1

    `unshift()` 在数组前端添加任意个项并返回新数组长度

    `splice()` 删除原数组的一部分成员，并可以在被删除的位置添加入新的数组成员

    `sort()` 调用每个数组项的toString()方法，然后比较得到的字符串排序，返回经过排序之后的数组

    `reverse()` 用于反转数组的顺序，返回经过排序之后的数组


# 样式

## 动态绑定class

- 对象绑定
    ``` html
    <div :class="{error:true}">error</div>
    <div :class="{error:isError}">error2</div>
    <div :class="errorClass">error3</div>
    <div :class="nameClass">jack</div>
    <button @click="changeType">click</button>
    ```
    ``` javascript
    data() {
        return {
            isError:false,
            errorClass:{
                error:true,
                font:true
            },
            nameClass:{
                success: false,
                warning: false,
                error: true,
                font: true
            }
        }
    },
    methods: {
        changeType: function(){
            this.nameClass.success = !this.nameClass.success;
            this.nameClass.error = !this.nameClass.error;
        }
    },
    ```
- 数组绑定
    ``` html
    <div class="warning" :class="{font:true}">warning1</div>
    <!--利用数组，可以绑定多个class-->
    <div :class="['warning', 'font']">warning2</div>
    <div :class="['warning', {font:true}]">warning3</div>
    ```

- 计算属性相结合
    > 如果有复杂的业务计算可以使用计算属性
    ``` html
    <div :class="getDivClass">试试</div>
    ```
    ``` javascript
    computed: {
        getDivClass : function(){
            // ... 一些判断 逻辑 ...
            return {
                red: true,
                'big-font': true
            };
        }
    },
    ```

## 动态绑定行内式
- 字符串写法
    ``` html
    <div :style="'color:red'">2222</div>
    <div :style="'color:red; font-size:23px'">4444</div>
    ```

- 对象写法
    ``` html
    <div :style="{color:'red'}">3333</div>
    <!--font-size 在对象中会解析错误，可以使用驼峰命名代替-->
    <div :style="{color:'red', fontSize:'23px'}">5555</div>
    <div :style="styleObj">6666</div>
    ```
    ``` javascript
    data() {
        return {
            styleObj:{
                color:'red',
                fontSize:'30px'
            }
        }
    },
    ```

- 动态绑定 
    ``` html
    <ul>
        <li v-for="i in 10">
            <!-- 字符串写法 -->
            <div :style="'font-size:'+i+'0px'">{{i}}</div>
            <!-- 对象写法 -->
            <div :style="{fontSize:i*10+'px'}">{{i}}</div>
        </li>
    </ul>
    ```

# 过度效果及动画

# vue组件

## 全局组件和私有组件

- 定义

    > 全局组件可以在所有 `vue`对象所指定的标签内使用，包括私有组件的内部
    ``` html
    <template id="public">
        <div>
            我是全局组件     
        </div>
    </template>
    ```
    ``` javascript
    //使用定义好的模板
    Vue.component('public', {
        template: '#public'
    })
    //或是直接使用 html
    Vue.component('public', {
        template: '<div>我是全局组件</div>'
    })
    ```
    > 私有组件，在全局组件中无法使用私有组件，私有组件只可以使用在定义该组件的 `vue`对象所指定的标签内部，即例子中的 `div#app` 内部<br>
    > 即只能在父组件之内使用
    ``` html
    <template id="private">
        <div>
            我是私有组件
            <!-- 私有组件中可以使用全局组件 -->
            <public></public>
        </div>
    </template>
    ```
    ``` javascript
    let vm = new Vue({
        el: "#app",
        components:{
            'private':{
                //这里使用定义好的模板，同样可以直接写html
                template: '#private'
            }
        }
    })
    ```
    > 定义时需要注意一点，组件名需要 <b>`全小写`</b>，或是用 `-` 符号连接代替驼峰命名
    ``` html
    组件名 ： 'showList'
    使用   ： <show-list></show-list>
    ```

- 使用组件
    ``` html
    <!-- 直接将定义的组件名作为标签使用 -->
    <div id="app">
        <private></private>
        <public></public>
    </div>
    ```
- 动态组件

    > 使用标签的方式添加组件不够灵活，使用动态组件，能够与数据绑定，动态的添加模板至页面中

    ``` html
    <li :class="{active: highLightName == 'writeback'}" @click="changeTag('writeback')">回复我的</li>
    <li :class="{active: highLightName == 'call'}" @click="changeTag('call')">@我的</li>
    <li :class="{active: highLightName == 'praise'}" @click="changeTag('praise')">收到的赞</li>

    <!-- 使用 component 动态调用 -->
    <component :is="highLightName"></component>

    <template id="writeback">
        <div>
            <div>回复我的</div>
            <div>回复我的消息。。。。</div>
        </div>
    </template>
    <template id="call">
        <div>
            <div>@我的</div>
            <div>@我的消息。。。。</div>
        </div>
    </template>
    <template id="praise">
        <div>
            <div>收到的赞</div>
            <div>赞的列表。。。。</div>
        </div>
    </template>
    ```

    ``` javascript
    let vm = new Vue({
        el: "#app",
        data() {
            return {
                highLightName : 'writeback'
            }
        },
        methods: {
            changeTag : function(type){
                this.highLightName = type;
            }
        },
        components: {
            'writeback': {
                template : "#writeback"
            },
            'call':{
                template : "#call"
            },
            'praise':{
                template : "#praise"
            }
        }
    })
    ```

## 组件通信

- 父组件传值给子组件 `props`

    > 子组件可以使用 `props` 来定义一个数据支撑
    ``` javascript
    components:{
        'userlistcom' : {
            template: '#userlistcom',
            props:{
                getuserlist: {
                    type: Array,
                    required: true
                }
            }
            //如果不关注数据类型和数据验证,可以直接使用
            //props:['getuserlist']
        },
    }
    ```
    > 同时将定义的数据支撑，与父组件中的数据进行绑定,子组件便可以使用父组件中的数据

    ``` html
    <userlistcom :getuserlist="userlist"></userlistcom>

    <template id="userlistcom"> 
        <div>
            <ul>
                <li v-for="user in getuserlist">
                    <img :src="user.img" alt="">
                    <span>{{user.name}}</span>
                    <span>{{user.age}}</span>
                    <span>{{user.sex == 1 ? '男' : '女'}}</span>
                </li>
            </ul>
        </div>
    </template>
    ```

    > `props` 中的值不可修改， 可以使用数据或者属性计算来解决这个问题

    `props` 提供对数据类型的验证

    ``` javascript
    props : { 
        childNum : Number, //仅仅支持数值
        childAge : [Number, String], //支持数值和字符串 
        childStr : { //设置类型和默认值 
            type : String, //[Number, String] 
            default : 'no data' //当没有 v-bind 绑定则显示
        },
        childArr : { 
            type : Array, 
            default() { //数组和对象的默认值必须用方法 
                return []; 
            },
            required : true //必须 v-bind 绑定，不能不传 },
        childObj : { 
            type : Object, 
            default() { 
                return { 
                    name : 'no name' //给对象的某个属性设置默认值 
                } 
            } 
        },
        childPrice : { 
            validator(value) { 
                return value > 90; //自定义验证，不成立报错 
            } 
        }
    }
    ```

- 子组件传递值给父组件 `自定义事件`

    > 本质是自定义一个子组件的事件同时抛出一个值，该值便是想要传递的数据，父组件在监听到该事件触发时，同时调用父组件里的方法接收该数据，同时进行处理
    ``` javascript
    components:{
        'formcom' : {
            template: '#formcom',
            data() {
                return {
                    sexlist : [
                        {num : '1', type : '男'},
                        {num : '2', type : '女'}
                    ],
                    userinfo : {}
                }
            },
            methods: {
                submit : function(){
                    //发送数据, 这里自定义了一个事件 event, 同时抛出了一个值 即新用户的数据
                    this.$emit('event', this.userinfo);
                }
            },
        }
    }
    ```

    ``` html
    <!-- 父组件监听了event这个自定义事件，同时触发了getsubmit这个方法 -->
    <formcom @event="getsubmit"></formcom>
    ```

    ``` javascript
    // 父组件的 method
    methods: {
        getsubmit : function(newuser){
            //父组件接收传值，并追加数据至 userlist 中
            newuser = JSON.parse(JSON.stringify(newuser));
            this.userlist.push(newuser);
        },
    }
    ```
    > `vue` 也提供 `.sync`修饰符去修改父组件的值，由于单向数据流的概念，让维护清晰，还是尽量避免使用
    ``` html
    <!-- sync 修饰符修改父组件原始值 -->
    <html-a v-bind:count.sync="count"></html-a>
    ```

- 父组件使用子组件的成员 ( 数据、方法、事件等 )

    > 在组件添加 `ref`属性
    ``` html
    <formcom v-on:datasubmit="getsubmit" ref="formcom"></formcom>
    <button @click="clear">清空</button>
    ```
    > 使用 `vmRefs` 来操作 
    ``` javascript
    clear : function(){
        //可以调用子组件的方法
        //this.$refs.formcom.clearForm();
        //也可以直接操作数据
        this.$refs.formcom.newuser = {};
    }
    ```

## 插槽

- 基本使用 `slot`

    有时我们需要在组件模板中定义大量重复的内容区域，可以用插槽来避免重复

    ``` html
    <div id="app">
        <!-- 组件标签不写入内容时，slot标签的内容会作为默认值显示 -->
        <html1></html1>
        <html1></html1>
        <!-- 组件内部写入动态内容后，就会被组件里的slot标签所接收 -->
        <html1>zhaosi</html1>
        <html1>hanmeimei</html1>
    </div>
    <template id="html1">
        <div>
            hello
            <slot>lilei</slot>
        </div>
    </template>
    ```

- 命名插槽 `v-slot`

    为了实现更加更多的复杂布局，系统提供了具名插槽来实现调用机制
    ``` html
    <html2></html2>
        <!-- 命名插槽无法接收没有v-slot的动态内容 -->
        <html2>zhaosi</html2>
        <!-- 如果slot规定了name，那么只有v-slot才可以传递动态内容 -->
        <html2 v-slot:username>hanmeimei</html2>
    </div>
    <template id="html2">
        <div>
            Im not ok
            <!-- 如果是命名插槽，那么只会显示两种内容，1 v-slot传递的动态内容， 2 否则就是默认内容 -->
            <slot name="username">lilei</slot>
        </div>
    </template>
    ```
    > 简写模式 `#username` = `v-slot:username`

- 作用域插槽

<br>

