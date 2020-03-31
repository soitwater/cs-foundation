# ReactHooks

## 为什么使用Hook
- class式
  * 组件常常需要在生命周期写重复代码,如每一个界面在`componentDidMount`取`localeStorage`的数据
  * `this`指向繁琐
- 函数式
  * 没有生命周期，没有`state`
- hooks
  * 在函数式组件的基础上，添加了`生命周期`,`React State`,`ref`,`context`等构子
  * 一个组件中可以使用多个hook，每个hook会独立存在，内部状态不会共用。每次更新组件时，hook会按顺序从上到下执行

## 使用规则
- 在React函数中使用`Hooks`
- 只在最顶层使用
  ```js
  function Form() {
    const [name, setName] = useState('Mary')
    // 不是最顶层使用（目的是确保调用顺序）,且`if`判断改变了 hooks 的调用顺序
    if(!name) {
      const [age, setAge] = useState('12');
    }
    const [surname, setSurname] = useState('Poppins');
  }
  ```

## useState
- 例子
  ```js
  // const [like, setLike] = React.useState(0) // 0 表示变量 like 的初始值是 0
  const [obj, setObj] = React.useState({ like: 0, on: true })
  return (
    <>
      <Button onClick={() => { setObj({ like: obj.like + 1, on: obj.on }) }}>
        {obj.like} 人喜欢
      </Button>
      <Button onClick={() => setObj({ like: obj.like, on: !obj.on })}>
        {obj.on ? 'ON' : 'OFF'}
      </Button>
    </>
  )
  ```
- 函数式更新
  * 适用于新的`state`需要根据先前的`state`计算得出
    ```js
    function Counter({initialCount}) {
      const [count, setCount] = useState(initialCount);
      return (
        <>
          Count: {count}
          <button onClick={() => setCount(initialCount)}>Reset</button>
          <button onClick={() => setCount(prevCount => prevCount - 1)}>-</button>
          <button onClick={() => setCount(prevCount => prevCount + 1)}>+</button>
        </>
      );
    }
    ```
  * 结合`展开运算符`来达到`合并更新对象`的效果
    ```js
    setState(prevState => {
      // 也可以使用 Object.assign
      return {...prevState, ...updatedValues};
    });
    ```

## useEffect
- 一般不提倡在函数组件主体内（这里指在 React 渲染阶段）改变 DOM、添加订阅、设置定时器、记录日志以及执行其他包含副作用的操作
- `useEffect(didUpdate)`接受一个参数`didUpdate`,它表示那些可能产生副作用的操作
- 赋值给`useEffect`的操作会在`更新/渲染DOM后`执行
- useEffect:每次`更新/渲染DOM`都会执行
  ```js
  React.useEffect(() => { document.title = `点击了${obj.like}次` })
  ```
- useEffect:监听某些值,值改变才触发操作
  * 例子
    ```js
    const Test = () => {
      const [url, setUrl] = useState('')
      const [loading, setLoading] = useState(false)
      const [fetch, setFetch] = useState(false)

      useEffect(() => {
        setLoading(true)
        axios.get('https://dog.ceo/api/breeds/image/random')
          .then(r => {
            console.log(r.data.message)
            setUrl(r.data.message)
            setLoading(false)
          })
      }, [fetch]) // 只有当 fetch 变量改变时,才会触发 useEffect 的回调操作

      return (
        <Content>
          <Button onClick={() => { setFetch(!fetch) }}>下一张</Button>
          {loading ? <p>loading ...</p> : <img alt="dog" src={url} style={{ width: '200px' }}></img>}
        </Content>
      )
    }
    ```
## 自定义Hook
- 将组件逻辑提取到可重用的函数中
- 自定义Hook以`use`作前缀
- 例子
  ```js
  // useMouseTracker.js
  import React, { useState, useEffect } from 'react'
  const useMouseTracker = () => {
    const [pos, setPos] = useState({ x: 0, y: 0 })
    useEffect(() => {
      const updateMouse = (evt) => {
        setPos({ x: evt.clientX, y: evt.clientY })
      }
      document.addEventListener('mousemove', updateMouse)
      return () => { // 这里返回的是回调函数
        document.removeEventListener('mousemove', updateMouse)
      }
    })
    return pos
  }

  export default useMouseTracker
  ```
  ```js
  // index.js
  import useMouseTracker from './useMouseTracker.js'

  const Test = () => {
    const position = useMouseTracker()
    return <h1>{position.x}; {position.y}</h1>
  }
  ```

## 参考
- [react-hooks入门](https://segmentfault.com/a/1190000020288052)