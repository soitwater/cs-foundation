# ReactRouter

## 安装
- `npm i -S react-router-dom`

## 使用
- 编写一个路由组件`Router.js`
  ```js
  import React from 'react';
  import { HashRouter, Route, Switch } from 'react-router-dom';
  import TodoList from '../TodoList/TodoList.js';
  
  
  const BasicRoute = () => (
    <HashRouter>
      <Switch>
        <Route exact path="/" component={TodoList} />
      </Switch>
    </HashRouter>
  )
  
  export default BasicRoute
  ```
- 在`src/index.js`中引入`Router.js`
  ```js
  import Router from './Router/Router.js'

  ReactDOM.render(
    <Router />,
    // <TodoList />,  // 组件不再在index.js中引入了, 改为在 Router.js 中引入
    document.getElementById('root')
  );
  ```

## 使用`BrowserRouter`
```js
import { BrowserRouter, Route } from 'react-router-dom';

const BasicRoute = () => (
  <BrowserRouter>
    <Header />
    <Route exact path="/" component={Home} />
    <Route path='/login' exact component={Login}></Route>
  </BrowserRouter>
)

export default BasicRoute
```


## 路由跳转
### Redirect
```js
import { Redirect } from 'react-router'

render() {
  return (<Redirect to="/to" />)
}
```
### link to
- 写法
  ```js
  import { Link } from 'react-router-dom'
  <Link to="/child">跳转</Link>
  ```
### JS跳转
- 在 React 中正常来说只有在`<Route>`包裹的组件中才能在`this.props`属性中找到`history`对象
- 但有些情况下我们就是希望在一个没被`<Route>`包裹的组件中用到`history`对象
- 写法
  ```js
  import { withRouter } from 'react-router-dom'
  this.props.history.push('/other')
  export deafult withRouter(connect(mapState, mapDispatch)(OtherComponent))
  ```
### 路由参数-params
```js
<Route path="/detail/:id" component={detail} />

// 接收参数
// 对于类似 `http://localhost:3000/detail/2` 的路由，为了获取id的值：2，应该：
this.props.match.params.id
```
### 路由参数-query
```js
// 在router文件中配置为正常配置   
<Route path="/to" component={Child03}/>

// 在跳转时,路径为一个对象{}     
this.props.history.push({
  pathname: '/to',
  query: {
    name: 'xxx',
    age: 10
  }
})

// 使用`this.props.location.query`接收参数
```