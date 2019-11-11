# Vue
## 生命周期
![1.png](https://cn.vuejs.org/images/lifecycle.png)

## 模板语法
### 文本
```
    //msg为变量
    <span>Message: {{ msg }}</span>
```
### 原始HTML
```
    //使用v-html
    <span v-html="msg"></span>
```
### 特性
```
    //使用v-bind:,可以缩写为:
    <div v-bind:id="dynamicId"></div>
```
### 事件
```
    //使用v-on:，可以缩写为@
    <button v-on:click="eventName" />
```

## 计算属性和监听器
### 计算属性
```
    var vm = new Vue({
        el: "#example",
        data: {
            message: "hello"
        },
        computed: {
            reversedMessage: function () {
                // `this` 指向 vm 实例
                return this.message.split('').reverse().join('')
             }
        }
    })
    //调用
    this.reversedMessage
```
### 监听器
```
    var watchExampleVM = new Vue({
        el: '#watch-example',
        data: {
            question: '',
            answer: 'I cannot give you an answer until you ask a question!'
        },
        watch: {
            // 如果 `question` 发生改变，这个函数就会运行
            question: function (newQuestion, oldQuestion) {
                this.answer = 'Waiting for you to stop typing...'
                this.debouncedGetAnswer()
            }
        }
    }
```

## class
### 对象语法
```
    <div v-bind:class="{ active: isActive, 'text-danger': hasError }"></div>
    
    //定义在data里面
    data: {
        classObject: {
            active: true,
            'text-danger': false
        }
    }
    <div v-bind:class="classObject"></div>

```

### 数组语法
```
    data: {
        activeClass: 'active',
        errorClass: 'text-danger'
    }
    <div v-bind:class="[activeClass, errorClass]"></div>
```

## 条件渲染
### v-if
```
    <h1 v-if="awesome">Vue is awesome!</h1>

    //v-else
    <h1 v-if="awesome">Vue is awesome!</h1>
    <h1 v-else>Oh no 😢</h1>

    //v-else-if
    <div v-if="type === 'A'">
        A
    </div>
    <div v-else-if="type === 'B'">
        B
    </div>
    <div v-else-if="type === 'C'">
        C
    </div>
    <div v-else>
        Not A/B/C
    </div>
    <template v-if="true">
        <div>
            xxx
        </div>
    </template>
    
```
### v-show
```
    //v-show不支持v-else，也不支持template
    //v-if是真正的渲染,v-show不管是什么条件都会渲染，只是进行简单的css进行切换
    <h1 v-show="ok">Hello!</h1>
```

## 列表渲染
```
    //使用v-bind:ke区分列表中的每一项为
    <ul id="example-1">
        <li v-for="(item, index) in items" :key="index">
            {{ item.message }}
        </li>
    </ul>
    var example1 = new Vue({
        el: '#example-1',
        data: {
            items: [
                { message: 'Foo' },
                { message: 'Bar' }
            ]
        }
    });
```
### 数组更新检查
    ```
        push()
        pop()
        shift()
        unshift()
        splice()
        sort()
        reverse()
    ```
## 表单输入输出
使用v-model指令与表单元素创建双向数据绑定。
### 文本
```
    <input v-model="message" placeholder="edit me">
    <p>Message is: {{ message }}</p>
```
### 多行文本
```
    <span>Multiline message is:</span>
    <p style="white-space: pre-line;">{{ message }}</p>
    <br>
    <textarea v-model="message" placeholder="add multiple lines">	</textarea>
```
###  复选框
单个复选框，绑定到布尔值
```
	<input type="checkbox" id="checkbox" v-model="checked">
	<label for="checkbox">{{ checked }}</label>
```
多个复选框，绑定到同一个数组
```
	<div id='example-3'>
      <input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
      <label for="jack">Jack</label>
      <input type="checkbox" id="john" value="John" v-model="checkedNames">
      <label for="john">John</label>
      <input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
      <label for="mike">Mike</label>
      <br>
      <span>Checked names: {{ checkedNames }}</span>
    </div>
```
### 单选按钮
```
	<div id="example-4">
      <input type="radio" id="one" value="One" v-model="picked">
      <label for="one">One</label>
      <br>
      <input type="radio" id="two" value="Two" v-model="picked">
      <label for="two">Two</label>
      <br>
      <span>Picked: {{ picked }}</span>
    </div>
```
### 选择框
单选
```
	<div id="example-5">
      <select v-model="selected">
        <option disabled value="">请选择</option>
        <option>A</option>
        <option>B</option>
        <option>C</option>
      </select>
      <span>Selected: {{ selected }}</span>
    </div>
```
多选
```
<div id="example-6">
  <select v-model="selected" multiple style="width: 50px;">
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
  <br>
  <span>Selected: {{ selected }}</span>
</div>
```
> 默认选择文本，当设置value时会获取到选中项的value


## 事件处理
### 事件处理方法
```
    var example2 = new Vue({
        el: '#example-2',
        data: {
            name: 'Vue.js'
        },
        // 在 `methods` 对象中定义方法
        methods: {
            greet: function (event) {
            // `this` 在方法里指向当前 Vue 实例
            alert('Hello ' + this.name + '!')
            // `event` 是原生 DOM 事件
            if (event) {
                alert(event.target.tagName)
            }
            }
        }
    });
    <div id="example-2">
        <!-- `greet` 是在下面定义的方法名 -->
        <button v-on:click="greet">Greet</button>
    </div>
```
### 内联处理器中的方法
```
    <div id="example-3">
        <button v-on:click="say('hi')">Say hi</button>
        <button v-on:click="say('what')">Say what</button>
    </div>
    new Vue({
        el: '#example-3',
        methods: {
            say: function (message) {
            alert(message)
            }
        }
    });

    //访问原始的DOM事件
    使用$event变量传入
    <button v-on:click="warn('Form cannot be submitted yet.', $event)">
        Submit
    </button>
    methods: {      
        warn: function (message, event) {
            // 现在我们可以访问原生事件对象
            if (event) event.preventDefault()
            alert(message)
        }
    }
```
### 事件修饰符
```
    .stop
    .prevent
    .capture
    .self
    .once
    .passive
    <!-- 阻止单击事件继续传播 -->
    <a v-on:click.stop="doThis"></a>

    <!-- 提交事件不再重载页面 -->
    <form v-on:submit.prevent="onSubmit"></form>

    <!-- 修饰符可以串联 -->
    <a v-on:click.stop.prevent="doThat"></a>

    <!-- 只有修饰符 -->
    <form v-on:submit.prevent></form>

    <!-- 添加事件监听器时使用事件捕获模式 -->
    <!-- 即内部元素触发的事件先在此处理，然后才交由内部元素进行处理 -->
    <div v-on:click.capture="doThis">...</div>

    <!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
    <!-- 即事件不是从内部元素触发的 -->
    <div v-on:click.self="doThat">...</div>
    //可以使用多个修饰符
```
### 按键修饰符
```
    <input v-on:keyup.enter="submit">
    <input v-on:keyup.page-down="onPageDown">
```
### 按键码
```
    <input v-on:keyup.13="submit">
    //全局自定义按键修饰符
    Vue.config.keyCodes.f1 = 112
```

## 组件
```
    export default {
        name: '',   //组件名
        components: {}, //使用的组件
        props: {},  //属性
        data(){
            return {}   //数据，这里要改为方法
        },
        watch: {
                        //监听
        },
        computed: {},   //计算属性
        methods: {},    //事件方法
        created () { },     //创建成功后调用
        mounted () {}       //挂载成功后调用
    }
```
### props
HTML 中的特性名是大小写不敏感的，所以浏览器会把所有大写字符解释为小写字符。这意味着当你使用 DOM 中的模板时，camelCase (驼峰命名法) 的 prop 名需要使用其等价的 kebab-case (短横线分隔命名) 命名：
```
    props: ['postTitle']
    // html中
    // 如果你使用字符串模板，那么这个限制就不存在了
    <blog-post post-title="hello!"></blog-post>
```
>   所有的prop都使得其父子prop之间形成一个单向下行绑定:父级 prop 的更新会向下流动到子组件中，但是反过来则不行。这样会防止从子组件意外改变父级组件的状态

使用data接收prop初始值
```
    props: [size],
    data(){
        return {
            dSize: this.size
        }
    }
```

使用计算属性接收prop初始值
```
    props: [size],
    computed: {
        dSize: function(){
            return this.size
        }
    }
```

### 自定义事件
```
    this.$emit('myEvent');
    <my-component v-on:my-event="doSomething"></my-component>
```

>   注意在 JavaScript 中对象和数组是通过引用传入的，所以对于一个数组或对象类型的 prop 来说，在子组件中改变这个对象或数组本身将会影响到父组件的状态。

使用第三个vue组件，利用on和emit解决跳转当前页面错误问题
```
    import Vue from "vue";
    var eventBus = new Vue();
    // 定义事件
    eventBus.on("eventName", function(path){
        this.$router.push(path);
    });
    // 调用
    eventBus.emit("eventName", path);
```


