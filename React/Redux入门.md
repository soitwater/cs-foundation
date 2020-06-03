# Redux入门

## 安装  
```shell
npm install --save redux
npm install --save react-redux
```
另外需要安装谷歌的扩展插件`redux devtools`以及`react Developer Tools`  
```js
// 还需要配置 createState(), 允许谷歌浏览器使用 redux devtools
const store = createStore(
  reducer,
  window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()  
);
```
- `react-redux`是高阶组件,用于监听`store`的变化

## 写法
- 目录
  * store
    - index.js
    - reducer.js
- index.js
  ```js
  import { createStore } from 'redux'
  import reducer from './reducer.js'
  
  const store = createStore(
    reducer,
    window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()  
  );
  
  export default store;
  ```
- reducer.js
  ```js
  const defaultState = {
    todoList: {
      inputValue: '',
      list: ["a", "b", "c"]
    }
  }
  
  export default (state = defaultState, action) => {
    if(action.type === 'change_input_value') { // 判断 action
      const newState = JSON.parse(JSON.stringify(state)) // 复制当前的 state
      newState.todoList.inputValue = action.value
      return newState // 替换 state
    }
    return state
  }
  ```
- 在组件`TodoList.js`
  ```js
  // 引入
  import store from '../store/index.js'

  // 监听 store 的变化
  constructor() {
    ... ...
    store.subscribe(this.handleStoreChange)
  }
  handleStoreChange() { // 一旦 store 发生变化, 获取最新的 store 数据到当前组件
    this.setState(store.getState().todoList)
  }

  // 发送 action 给 redux 的 store
  handleInputChange(e) {
    const action = {
      type: 'change_input_value',
      value: e.target.value
    }
    store.dispatch(action) // 发送 action 给 store
  }
  ```

## reducer
- reducer 可以接受新的 state, 但是不允许修改 state
- reducer拆分
  ```js
  // src/store/reducer.js
  import { combineReducers } from 'redux'
  // 子reducer文件
  import headerReducer from '../common/header/store/reducer.js'
  
  export default combineReducers({
    // 映射关系，相当于 组件要获取 store 数据时， 变成了 store.state.header.属性名
    header: headerReducer
  })
  ```

## action
- `actionTypes.js`
  * 可以在`store`下新建文件`actionTypes.js`，该文件专门保存`action.type`，便于管理
  * 在`actionTypes.js`
    ```js
    export const ADD_TODOITEM = 'add_todoitem'
    export const DELETE_TODOITEM = 'delete_todoitem'
    ```
  * 在需要用到`action.type`的地方`import { ADD_TODOITEM } from './actionTypes.js' `
- 应使用`action.create`统一管理action
  * 下面这种写法不利于管理action，因为action会散布在整个代码文件中，难于debug
    ```js
    handleBtnClick() {
      const action = {
        type: 'add_todoItem'
      }
      store.dispatch(action)
    }
    ```
  * 正确的做法：
    - 在 store 下新建`actionCreators.js`
    - 写法
      ```js
      import { ADD_TODOITEM } from './actionTypes.js'
      export const getAddTodoitemAction = (value) => ({
        type: ADD_TODOITEM,
        value
      })
      ```
    - 其他需要使用`action`的代码文件再引入`actionCreators.js`中对应的方法即可`import { ADD_TODOITEM } from './actionCreators.js' `

## redux-thunk
- redux-thunk 作用
  * 有时候在组件的生命周期函数componentDidMount中做了一些Ajax数据请求，虽然没问题，但会让系统变臃肿，难以测试
  * `redux-thunk`可以让`store.dispatch(action)`中的`action`支持函数(原来的`dispatch`只支持`action`参数,用了`redux-thunk`之后还支持`方法`作为参数),把复杂的逻辑代码放在`action`中处理
  * `redux-thunk`使得`dispatch`支持`方法`作为参数,这个`方法`的参数为：
    - 第一个参数是`dispatch`,等同于`dispatch`
    - 在方法体内异步操作后的回调里是`dispatch(store.getState())`
  * 便于把业务逻辑(如AJAX请求等)放到`actionCreators.js`中去编写,简化组件测试
- 安装`cnpm i -S redux-thunk`
- 例子：将 axios的get请求从组件中抽出到actionCreators.js
  ```js
  // src/store/index.js
  import { createStore, applyMiddleware, compose } from 'redux';
  import thunk from 'redux-thunk';
  import rootReducer from './reducer.js';
  
  const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose
  const enhancer = composeEnhancers(
    applyMiddleware(thunk)
  )
  const store = createStore(rootReducer, enhancer);
  export default store
  ```
  ```js
  // src/sotre/actionCreators.js
  import axios from 'axios'
  import { INIT_LIST_ACTION } from './actionTypes.js'
  axios.defaults.baseURL = 'http://localhost:3333' 
  
  export const initListAction = (data) => ({
    type: INIT_LIST_ACTION,
    data
  })
  export const getTodolist = () => {
    return (dispatch) => { // dispatch 参数相当于原来在组件中的 store.dispatch(action)
      axios.get('/get-todolist').then((res) => {
        const data = res.data
        const action = initListAction(data) // 获取action
        dispatch(action) // 通知 reducer.js 
      })
    }
  }
  ```
  ```js
  // src/Todolist/Todolist.js
  import React, { Component } from 'react'
  import store from '../store/index.js'
  import { getTodolist } from '../store/actionCreators.js'
  componentDidMount() {
    const action = getTodolist()
    store.dispatch(action)
  }
  ```

## Redux-Saga 中间件
- Redux-Saga 学习成本更高
- 与 Redux-thunk功能类似，但react-thunk是把异步处理放在`action`中，react-saga是把异步处理放在单独的文件`saga.js`
- saga 要求函数是 generator 函数
- 写法
  ```js
  // 假设在组件内有一个异步请求
  componentDidMount() { // 即普遍写法
    const action = getInitList()
    store.dispatch(action) // 可以被 reducer.js 或者 saga.js 获取到
  }
  ```
  ```js
  // 在 src/store/index.js
  import { createStore, applyMiddleware, compose } from 'redux';
  import rootReducer from './reducer.js';
  import createSagaMiddleware from 'redux-saga'
  import TodoListSaga from './saga.js'
  
  const sagaMiddleware = createSagaMiddleware()
  const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose
  const enhancer = composeEnhancers(
    applyMiddleware(sagaMiddleware)
  )
  const store = createStore(rootReducer, enhancer);
  sagaMiddleware.run(TodoListSaga)
  
  export default store
  ```
  ```js
  // store/saga.js
  import { takeEvery, put } from 'redux-saga/effects'
  import { INIT_LIST_ACTION } from './actionTypes.js'
  import { initListAction } from './actionCreators.js'
  import axios from 'axios'
  
  function* getInitList() {
    const res = yield axios.get('/get-todolist')
    const action = initListAction(res.data)
    yield put(action) // 相当于 store.dispatch(action) // 已经向后台获取数据, 通知 reducer.js 执行后续操作
  }
  
  function* mySaga() {
    yield takeEvery(INIT_LIST_ACTION, getInitList) // 第一个参数：action.type; 第二个参数：异步操作
  } 
  
  export default mySaga
  ```

## React-Redux
- 使用
  * 在单页应用的根组件上添加
    ```js
    // src/index.js
    import { Provider } from 'react-redux'
    import store from './store/index.js'
    
    ReactDOM.render(
      (
        <Provider store={ store }>
          <Router />
        </Provider>
      ),
      document.getElementById('root')
    );
    ```
    ```js
    // 在组件中
    import { connect } from 'react-redux'
    // 组件中的 constructor 不需要 this.handleTodoitemDele = this.handleTodoitemDele.bind(this) 这种代码了
    // 在类组件中也不需要 handleTodoitemDele() {} 这种方法了
    
    // 组件中新增：
    // 表示将组件的 props 映射到 store
    const mapStateToProps = (state) => {
      return {
        // 即 组件的 this.props.inputValue === store 的 state.todoList.inputValue
        inputValue: state.todoList.inputValue,
        list: state.todoList.list
      }
    }
    // 把 store.dispatch 挂载 到 this.props 上
    const mapDispatchToProps = (dispatch) => {
      // 这里写原来在类组件中定义的方法, 现在类组件中不需要写方法了
      return {
        handleInputChange(e) {
          const action = {
            type: 'change_input_value',
            value: e.target.value
          }
          dispatch(action)
        },
        handleTodoitemDele(index) {
          const action = {
            type: 'remove_todoitem',
            value: index
          }
          store.dispatch(action)
        },
        handleBtnClick() {
          const action = {
            type: 'add_todoItem'
          }
          store.dispatch(action)
        }
      }
    }

    // export 的写法有改动
    // connet 将（ui组件 TodoList）与（store）连接，并返回一个（容器组件）
    export default connect(mapStateToProps, mapDispatchToProps)(TodoList)
    ```
- react-redux 拆分 state
  * 在 src/store/reducer.js
    ```js
    import { combineReducers } from 'redux-immutable';
    import { reducer as todolistReducer } from '../pages/todolist/store/index.js';
    import { reducer as headerReducer } from '../common/header/store/index.js';
    // 使用时是 state.get('todolist').get('list'), 这是 immutable 的语法
    const reducer = combineReducers({
      todolist: todolistReducer,
      header: headerReducer
    })
    
    export default reducer
    ```
  * 在拆分出去的组件中 pages/todolist/store/index.js
    ```js
    import reducer from './reducer';
    // 注意这里的 ES6 模块写法
    // 导入 actionCreators 其实是 import { actionCreators } from 'pages/todolist/store/index.js'
    import * as actionCreators from './actionCreators';
    import * as constants from './constants';
    
    export { reducer, actionCreators, constants };
    ```
  * 在拆分出去的组件中 pages/todolist/store/reducer.js
    ```js
    import { fromJS } from 'immutable';
    import * as constants from './constants';
    
    const defaultState = fromJS({
    	title: '',
    	content: ''
    });
    
    export default (state = defaultState, action) => {
    	switch(action.type) {
    		case constants.CHANGE_DETAIL:
    			return state.merge({
    				title: action.title,
    				content: action.content
    			})
    		default:
    			return state;
    	}
    }
    ```

## immutable
- 因为react要求除了`store`自己可以修改`state`，其他的如`reducer`或`actionCreators`等都不能修改`state`
- 将`state`变成`immutable`(永恒不变的...)对象，防止无意中被篡改
- `state`变成`immutable`对象时,不能通过`state.todolist.list`,应该
  ```js
  // 将 state 变为 immutable 对象
  import { fromJS } from 'immutable';
  const defaultState = fromJS({
    login: false
  });
  // immutable对象的修改方法(不改变原始对象,而是返回新对象)
  state.get('xx')
  state.set('xx', value)
  state.merge({
    "xx": value
  })

  (immutable对象).toJS() // 将 immutable对象 转变为 普通对象
  ```
- [一些immutable-api](https://www.jianshu.com/p/0fa8c7456c15)


## redux工作流
- ![redux工作流](../assets/redux工作流.png)