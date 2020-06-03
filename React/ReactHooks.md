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
  * 核心目的还是`可复用`

## 使用规则
- 在React函数中使用`Hooks`
- 只在最顶层使用
  ```js
  function Form() {
    const [name, setName] = useState('Mary')
    // 不是最顶层使用（目的是确保调用顺序）,且`if`判断改变了 hooks 的调用顺序(下面的 if 写法是错误的)
    if(!name) {
      const [age, setAge] = useState('12');
    }
    const [surname, setSurname] = useState('Poppins');
  }
  ```

## 类型
- useState  useReducer
- useEffect  useLayoutEffect
- useMemo  useCallback
- useContext

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
  * 函数式更新能辅助完成小小的优化（如果初始 `State` 创建比较昂贵时，可以传一个函数给 useState）
    ```js
    function Table(props) {
      // createRows() 每次渲染都会被调用
      const [rows, setRows] = useState(createRows(props.count));
      // ...
    }
    
    function Table(props) {
      // createRows() 只会被调用一次
      const [rows, setRows] = useState(() => createRows(props.count));
      // ...
    }
    ```

## useReducer
- useState 的替代方案，它接收一个 `(state, action) => newState` 的 `reducer` 处理函数，并返回当前的 `state` 和 配套的 `dispatch` 方法。使用方法与 `redux`非常相似。
- 写法
  ```js
  const initialState = {count: 0};
 
  function reducer(state, action) {
    switch (action.type) {
      case 'increment':
        return {count: state.count + 1};
      case 'decrement':
        return {count: state.count - 1};
      default:
        throw new Error();
    }
  }
  
  function Counter(props) {
    const [state, dispatch] = useReducer(reducer, initialState);
    return (
      <>
        Count: {state.count}
        <button onClick={() => dispatch({type: 'decrement'})}>-</button>
        <button onClick={() => dispatch({type: 'increment'})}>+</button>
      </>
    );
  }
  ```

## useCallback && useMemo
- 参数与`useEffect`一致
- 由于:
  * (1)调用`setState`，就会触发组件的重新渲染，无论前后的`state`是否不同;
  * (2)父组件更新，子组件也会自动的更新
  * 在类组件`class`中是使用`shouldComponentUpdate(newPops, newState)`对比前后的`props``state`决定是否需要更新
  * 在hooks中则是根据`useMemo``useCallback`监听的数据是否变化决定是否更新
- `useMemo`和`useCallback`都会在组件第一次渲染的时候执行，之后会在其依赖的变量发生改变时再次执行
- `useMemo`返回缓存的变量，`useCallback`返回缓存的函数
- useMemo例子
  ```js
  import React from 'react';
  
  export default function WithoutMemo() {
    const [count, setCount] = useState(1);
    const [val, setValue] = useState('');

    function expensive() {
      console.log('compute');
      let sum = 0;
      for (let i = 0; i < count * 100; i++) {
        sum += i;
      }
      return sum;
    }

    return <div>
      <h4>{count}-{val}-{expensive()}</h4>
      <div>
        <button onClick={() => setCount(count + 1)}>+c1</button>
        <input value={val} onChange={event => setValue(event.target.value)}/>
      </div>
    </div>;
  }
  ```
  * 这里创建了两个state，然后通过expensive函数，执行一次昂贵的计算，拿到count对应的某个值。
  * 可以看到：无论是修改count还是val，由于组件的重新渲染，都会触发expensive的执行(能够在控制台看到，即使修改val，也会打印)；
  * 但是这里的昂贵计算只依赖于count的值，在val修改的时候，是没有必要再次计算的。
  * 在这种情况下，我们就可以使用useMemo，只在count的值修改时，执行expensive计算：
  * 把`expensive`修改为：
    ```js
    const expensive = useMemo(() => {
      console.log('compute');
      let sum = 0;
      for (let i = 0; i < count * 100; i++) {
        sum += i;
      }
      return sum;
    }, [count])
    ```
- `useCallback`使用场景
  * 有一个父组件，其中包含子组件，子组件接收一个函数作为props；通常而言，如果父组件更新了，子组件也会执行更新；但是大多数场景下，更新是没有必要的，我们可以借助useCallback来返回函数，然后把这个函数作为props传递给子组件；这样，子组件就能避免不必要的更新
  * 所有依赖本地状态或props来创建函数，需要使用到缓存函数的地方，都是useCallback的应用场景
- `useCallback`写法
  * 代码
    ```js
    import React, { useState, useCallback, useEffect } from 'react';
    function Parent() {
      const [count, setCount] = useState(1);
      const [val, setVal] = useState('');

      const callback = useCallback(() => {
        return count;
      }, [count]);
      return <div>
        <h4>{count}</h4>
        <Child callback={callback}/>
        <div>
          <button onClick={() => setCount(count + 1)}>+</button>
          <input value={val} onChange={event => setVal(event.target.value)}/>
        </div>
      </div>;
    }
    
    function Child({ callback }) {
      const [count, setCount] = useState(() => callback());
      useEffect(() => {
        setCount(callback());
      }, [callback]);
      return <div>
        {count}-{(+new Date())}
      </div>
    }
    ```
  
## useEffect
- 用于模拟生命周期
- 一般不提倡在函数组件主体内（这里指在 React 渲染阶段）改变 DOM、添加订阅、设置定时器、记录日志以及执行其他包含副作用的操作
- `useEffect(didUpdate)`接受一个参数`didUpdate`,它表示那些可能产生副作用的操作
- `useEffect`接受两个参数，第一个是函数(与生命周期回调相关)，第二个是监听的数据(array类型)(数据变化后)
  * 如果第二个参数是一个空数组，表示没有依赖项
    - 回调函数：只在组件挂载的时候执行一次，相当于 componentDidMount()
    - 返回值函数：只在组件卸载的时候执行一次，相当于 componentWillUnmount()
  * 如果第二个参数有值
    - 回调函数：除了具有 componentDidMount()，还当 数组内的变量发生变化时执行 componentDidUpdate()
    - 返回值函数：除了具有 componentWillUnmount()，还当 数组内的值发生变化时执行 componentWillUpdate()
  * 注意第二个参数是`浅比较`，传入引用类型无意义
  * 注意useEffect 第一个参数不能是 `async 异步函数`，因为它总是返回一个 `Promise`，这不是我们想要的
- 与生命周期相关的写法
  ```js
  useEffect(() => { // 每次渲染后执行此函数，获取到的值是最新的,（挂载后+跟新后），相当于 componentDidMount() + componentDidUpdate()
    console.log("Effect after render", count);
      return () => { // 每次执行useEffect前，先执行此函数，获取到的数据是更新之前的值,相当于`卸载前+更新前` componentWillUnmount() + componentWillUpdate()
        console.log("remove last", count);
      }
  });
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

## useLayoutEffect
- 它与 useEffect 的用法完全一样，作用也基本相同
- 唯一的不同在于执行时机，它会在所有的 `DOM 变更`之后同步调用` effect`
- useEffect 不会阻塞浏览器的绘制任务，它会在页面更新之后才执行。
- 而 `useLayoutEffect` 跟 `componentDidMount` 和 `componentDidUpdate` 的执行时机(调用阶段)一样，会阻塞页面渲染，如果当中有耗时任务的话，页面就会卡顿。
- 大多数情况下`useEffect` 比 `class 的生命周期函数`性能更好，我们应该优先使用`useEffect`,再尝试`useLayoutEffect`。

## useContext


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
- [react-hooks总结](https://www.cnblogs.com/V587Chinese/p/12778842.html)