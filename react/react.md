# React
React的核心思想是:封装组件。
React大体包含下面概念:
*   组件
*   JSX
*   Virtual DOM
*   Data Flow

## React概念
-   组件

    React 应用都是构建在组件之上。
    上面的 HelloMessage 就是一个 React 构建的组件，最后一句 render 会把这个组件显示到页面上的某个元素 mountNode 里面，显示的内容就是 <div>Hello John</div>。
    props 是组件包含的两个核心概念之一，另一个是 state（这个组件没用到）。可以把 props 看作是组件的配置属性，在组件内部是不变的，只是在调用这个组件的时候传入不同的属性（比如这里的 name）来定制显示这个组件。

-   JSX

    从上面的代码可以看到将 HTML 直接嵌入了 JS 代码里面，这个就是 React 提出的一种叫 JSX 的语法，这应该是最开始接触 React 最不能接受的设定之一，因为前端被“表现和逻辑层分离”这种思想“洗脑”太久了。但实际上组件的 HTML 是组成一个组件不可分割的一部分，能够将 HTML 封装起来才是组件的完全体，React 发明了 JSX 让 JS 支持嵌入 HTML 不得不说是一种非常聪明的做法，让前端实现真正意义上的组件化成为了可能。
    好消息是你可以不一定使用这种语法，后面会进一步介绍 JSX，到时候你可能就会喜欢上了。现在要知道的是，要使用包含 JSX 的组件，是需要“编译”输出 JS 代码才能使用的，之后就会讲到开发环境。

-   Virtual DOM

    当组件状态 state 有更改的时候，React 会自动调用组件的 render 方法重新渲染整个组件的 UI。
    当然如果真的这样大面积的操作 DOM，性能会是一个很大的问题，所以 React 实现了一个Virtual DOM，组件 DOM 结构就是映射到这个 Virtual DOM 上，React 在这个 Virtual DOM 上实现了一个 diff 算法，当要重新渲染组件的时候，会通过 diff 寻找到要变更的 DOM 节点，再把这个修改更新到浏览器实际的 DOM 节点上，所以实际上不是真的渲染整个 DOM 树。这个 Virtual DOM 是一个纯粹的 JS 数据结构，所以性能会比原生 DOM 快很多。

-   Data Flow

    “单向数据绑定”是 React 推崇的一种应用架构的方式。当应用足够复杂时才能体会到它的好处，虽然在一般应用场景下你可能不会意识到它的存在，也不会影响你开始使用 React，你只要先知道有这么个概念。

## React声明周期
![1045236351-57ce24c128a2a_articlex.png](https://upload-images.jianshu.io/upload_images/13609096-1aad28dc5193b0a8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 实例化
-   getDefaultProps

    这个方法是用来设置组件默认的 props，组件生命周期只会调用一次。但是只适合 React.createClass 直接创建的组件，使用 ES6/ES7 创建的这个方法不可使用， ES6/ES7 可以使用下面方式：
    ```
        //es7
        class Component{
            static defaultProps = {}
        }

        //在外面定义es6
        Component.defaultProps
    ```
-   getInitalState
    设置state初始值
    ```
        class Component extends React.Component{
            constructor(props){
                super(props);
                this.state = {
                    render: true,
                }
            }
        }
    ```
-   componentWillMount
    组件首次渲染之前调用,这个是在render方法调用前可修改state的最后一次机会
-   render
    解析成对应的虚拟DOM，渲染成最终结果
    ```
        render(){
            return <div>内容</div>
        }
    ```
-   componentDidMount
    DOM挂载调用

### 存在期
-   componentWillReceiveProps
    当通过父组件更新子组件props时，这个方法就会被调用。
    ```
        componentWillReceiveProps(nextProps){}
    ```
-   showComponentUpdate
    是否应该更新组件，默认返回true,当返回false时，后期函数就不会调用，组件不会再次渲染
    ```
        shouldComponentUpdate(nextProps,nextState){}
    ```
-   componentWillUpdate
    字面意思组件将会更新，props 和 state 改变后必调用
    ```
        componentWillUpdate(nextProps, nextState) {
            console.log('Component WILL UPDATE!');
        }
    ```

-   componentDidUpdate
    更新DOM成功后调用
    ```
        componentDidUpdate(prevProps, prevState) {
            console.log('Component DID UPDATE!')
        }
    ```

### 销毁期
-   componentWillUnmount
    组件被销毁时调用

## React的使用
### 元素渲染
-   根节点渲染
    ```
        import ReactDOM from "react-dom";
        const element = <h1>xxxx</h1>;
        ReactDOM.render(element, document.getElementById('example'));
    ```
-   普通节点渲染
    ```
        // 方法
        function tick(){
            return <div>xxxx</div>
        }
        //类
        import React, {Component} from "react";
        class Tick extends Component{
            render(){
                return <div>xxx</div>;
            }
        }
    ```

### 组件
-   方法写法
    ```
        function HelloMessage(props){
            return <h1>Hello World!</h1>;
        }
    ```
-   类写法
    ```
        import React, {Component} from "react";
        class Welcome extends React.Component{
            render(){
                return <h1>Hello World!</h1>;
            }
        }
    ```

### 复合组件
    我们可以通过创建多个组件来合成一个组件，即把组件的不同功能点进行分离。
    
    ```
        function Name(props) {
            return <h1>网站名称：{props.name}</h1>;
        }
        function Url(props) {
            return <h1>网站地址：{props.url}</h1>;
        }
        function Nickname(props) {
            return <h1>网站小名：{props.nickname}</h1>;
        }
        function App() {
            return (
            <div>
                <Name name="菜鸟教程" />
                <Url url="http://www.runoob.com" />
                <Nickname nickname="Runoob" />
            </div>
            );
        }
    ```

### 属性和状态
-   状态
    React 把组件看成是一个状态机（State Machines）。通过与用户的交互，实现不同状态，然后渲染 UI，让用户界面和数据保持一致。
    React 里，只需更新组件的 state，然后根据新的 state 重新渲染用户界面（不要操作 DOM）。
    ```
        class Clock extends React.Component {
            constructor(props) {
                super(props);
                this.state = {date: new Date()};
            }
 
            render() {
                return (
                <div>
                    <h1>Hello, world!</h1>
                    <h2>现在是 {this.state.date.toLocaleTimeString()}.</h2>
                </div>
                );
            }
        }
        //初始化
        //在构建函数中使用
        this.state = {
            a: 1
        };

        //修改state
        //state不能直接修改
        this.setState({
            a: 2
        });

        //取值
        this.state.a
    ```

-   属性
    ```
        // 方法写法
        function HelloMessage(props) {
            return <h1>Hello {props.name}!</h1>;
        }  
        
        //类写法
        class HelloMessage extends Components{
            constuctor(props){
                super(props)
            }
            render(){
                return <h1>Hello {props.name}!</h1>;
            }
        }

        // 调用
        <HelloMessage name="xxxx"/>

        //默认props
        class HelloMessage extends React.Component {
            constuctor(props){
                super(props);
                this.props = {
                    name: "xxx"
                };
            }
            render() {
                return (
                    <h1>Hello, {this.props.name}</h1>
                );
            }
        }
    ```

-   属性验证
    15.5版本以后，已经从React.PropTypes移到prop-types库
    ```
        class MyTitle extends React.Component {
            render() {
                return (
                    <h1>Hello, {this.props.title}</h1>
                );
            }
        }
        MyTitle.propTypes = {
            title: PropTypes.string
        };
    ```
    15.4
    ```
        MyTitle.propTypes = {
            title: React.PropTypes.string
        };
    ```

### 事件处理
-   写法
    ```
        <button onClick={activateLasers}>
            激活按钮
        </button>
    ```
    方法写法
    ```
        function ActionLink() {
            function handleClick(e) {
                e.preventDefault();
                console.log('链接被点击');
            }
    
            return (
                <a href="#" onClick={handleClick}>
                点我
                </a>
            );
        }
    ```
    类写法
    ```
        class Toggle extends React.Component {
            constructor(props) {
                super(props);
                this.state = {isToggleOn: true};
            }
 
            handleClick = () => {
                this.setState(prevState => ({
                isToggleOn: !prevState.isToggleOn
                }));
            };
 
            render() {
                return (
                <button onClick={this.handleClick}>
                    {this.state.isToggleOn ? 'ON' : 'OFF'}
                </button>
                );
            }
        }
    ```
    自定义写法
    ```
        // 将方法存在一个对象里面,类似于vue
        class Toggle extends React.Component {
            constructor(props) {
                super(props);
                this.state = {isToggleOn: true};
            }

            methods = {
                handleClick: ()=>{
                    this.setState(prevState => ({
                        isToggleOn: !prevState.isToggleOn
                    }));
                },
            };
           
 
            render() {
                return (
                <button onClick={this.methods.handleClick}>
                    {this.state.isToggleOn ? 'ON' : 'OFF'}
                </button>
                );
            }
        }
    ```

### 条件渲染
    ```
        class Test extends Component{
            constructor(){
                this.state = {
                    a: 1
                };
            }
            render(){
                return <div>{this.state.a?"1":"0"}</div>
            }
        }
    ```

### 列表渲染
    ```
        class Test extends Component{
            constructor(){
                this.state = {
                    a: [1, 2, 3]
                };
            }
            render(){
                return <div>{this.state.a.map(item=>{
                    return item;
                })}</div>
            };
        }

    ```

### refs
    ```
        class MyComponent extends React.Component {
            handleClick() {
                //获取到标签对象
                this.refs.myInput
                // 使用原生的 DOM API 获取焦点
                this.refs.myInput.focus();
                //获取input输入值
                this.refs.myInput.value;
            }
            render() {
                //  当组件插入到 DOM 后，ref 属性添加一个组件的引用于到 this.refs
                return (
                <div>
                    <input type="text" ref="myInput" />
                    <input
                    type="button"
                    value="点我输入框获取焦点"
                    onClick={this.handleClick.bind(this)}
                    />
                </div>
                );
            }
        }
    ```
