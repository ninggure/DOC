# Redux
## 三大原则
-   单一数据源
    使用redux的程序，所有的state都存储在一个单一的数据源store内部，类似一个巨大的对象树。

-   state是只读的
    state是只读的，能改变state的唯一方式是通过触发action来修改

-   使用纯函数执行修改
    为了描述 action 如何改变 state tree ， 你需要编写 reducers。
    reducers是一些纯函数，接口当前state和action。只需要根据action，返回对应的state。而且必须要有返回。
    一个函数的返回结果只依赖于它的参数，并且在执行过程里面没有副作用，我们就把这个函数叫做纯函数。

## Redux的概念
-   action
    顾名思义，action就是动作，也就是通过动作来修改state的值。也是修改store的唯一途径。
    action本质上就是一个普通js对象，我们约定这个对象必须有一个字段type，来表示我们的动作名称。一般我们会使用一个常量来表示type对应的值。
    此外，我们还会把希望state变成什么样子的对应的值通过action传进来，那么这里action可能会类似这样子的：
    ```
        {
            type: 'TOGGLE_TODO',
            index: 5
        }
    ```

-   Reducer
    Action 只是描述了有事情发生了这件事实，但并没有说明要做哪些改变，这正是reducer需要做的事情。
    Reducer作为纯函数，内部不建议使用任何有副作用的操作，比如操作外部的变量，任何导致相同输入但输出却不一致的操作。
    如果我们的reducer比较多，比较复杂，我们不能把所有的逻辑都放到一个reducer里面去处理，这个时候我们就需要拆分reducer。
    幸好，redux提供了一个api就是combineReducers Api。

- dispatcher
    store.dispatch()是 View 发出 Action 的唯一方法。
    ```
        import { createStore } from 'redux';
        const store = createStore(fn);
        store.dispatch({
            type: 'ADD_TODO',
            payload: 'Learn Redux'
        });
    ```

-   subscribe
    Store 允许使用store.subscribe方法设置监听函数，一旦 State 发生变化，就自动执行这个函数。

-   store
    store是redux应用的唯一数据源，我们调用createStore Api创建store。

## Redux的使用
### action
-   定义action类型
    ```
        const ActionTypes = {
            ADD_NUM: "ADD_NUM",
            MINUSE_NUM: "MINUSE_NUM"
        };
    ```
-   定义action事件
    ```
        const addNum = (num) => {
            return {
                type: ActionTypes.ADD_NUM,
                payload: num
            };
        };

        const minusNum = (num) => {
            return {
                type: ActionTypes.MINUSE_NUM,
                payload: num
            };
        };
    ```
-   触发事件改变state
    ```
        store.dispatch(addNum(1));
    ```

### reducer
-   定义初始化state
    ```
        const defautlState = {
            num: 0
        }
    ```
-   定义单个reducer
    ```
        const numReducer = (state=defaultState, action) => {
            // 建议一开始深拷贝state,不改变原来的state
            let newState = JSON.parse(JSON.stringify(state));
            switch(action.type){
                case ActionTypes.ADD_NUM:
                    newState.num += action.payload;
                    break;
                case ActionTypes.MINUSE_NUM:
                    newState.num -= action.payload;
                    break;
                default: 
                    break;
            }
            // 返回新的state
            return newState
        };
    ```
-   拆分reducer
    ```
        import {combineReducer} from 'redux';
        const Reducer = combineReducers({
            reducer1,
            reducer2,
            ...
        })
    ```

### store
-   定义store
    ```
        const store = createStore(Reducer);
    ```
-   获取state
    ```
        store.getState();
        //单个store,返回回来的只有一个reducer的状态,如状态定义为{num:0},返回回来的数据格式与此一致
        const sState = store.getState();
        {
            num: xxx
        }
        //多个reducer时，会返回多个reducer的对象
        {
            reducer1: {...},
            reducer2: {...},
        }

    ```
-   订阅状态变化
    ```
        // subscribe可以配合getState实时获取state变化
        store.subscribe(()=>{
            num: store.getState()
        }); 
    ```

## Redux&React搭配使用
    Redux与React搭配使用时，需要安装react-redux,redux-thunk中间件

### store
-   添加redux-thunk
    ```
        import {createStore, applyMiddleware} from "redux";
        import thunk from "redux-thunk";
        import {rootReducer} from "./reducers";
        const store = createStore(rootReducer, applyMiddleware(thunk));
    ```
-   加入Provider
    ```
        // 添加到配置路由的配置
        import { Provider } from "react-redux";
        class App extends React.Component{
            render(){
                return (
                    <Provider store={store}>
                        <BrowserRouter>
                            <Switch>
                                {renderRoutes(routes)}
                            </Switch>
                        </BrowserRouter>
                    </Provider>
                    
                )
            }
        }
    ```

### action

-   修改action
    ```
        export const actionTest(value) => {
            return (dispatch)=>{
                dispatch({
                    type: TEST,
                    value: value
                })
            };
        }

    ```


### 组件
-   修改组件，实现UI和数据分离
    ``` 
        import { connect } from 'react-redux';
        // 实现UI和数据分离
        class Test extends Component{
            constructor(props){
                super(props);
            }
            methods = {
            
            }
            render(){
                //获取传入的state和action
                const {value, change} = this.props;
                return (
                    <div className="test">
                        <div>{value}</div>
                        <div onClick={()=>{change(22)}}>click</div>
                    </div>
                );
            }
        } 
        
        // 通过props将state和action传入
        const StateToProps = (state) => {
            return {
                value: state.TestReducer.value
            }
        };

        const DispatchToProps = (dispatch) => {
            return {
                change(value){
                    dispatch(actionTest(value));
                }
            }
        };
        export default connect(StateToProps, DispatchToProps)(Test);
    ```

### 实现组件UI数据分离(初始数据，以及更新数据源)
-   直接在action中更新state
    ```
        //外部传入数据
        //用于第一次获取数据，或是更新数据
        //在page中直接调用可获取或更新数据源，内部涉及到增删改数据，可传入属性进行判断。
        export const actionBase = (data) => {
            store.dispatch({
                type: "",
                data: data
            })
        };
        
        
    ```
