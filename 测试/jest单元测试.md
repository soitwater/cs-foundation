# jest单元测试

## 例子
- 安装`cnpm i -S jest`
- 在`package.json`中
  * `jest`会自动识别3种文件（范围是整个项目）
    - .test.js结尾的文件
    - 以.spec.js结尾的文件
    - 放到__tests__ 文件夹中的文件
  * `npm run coverage`会生成代码覆盖率报告(项目下生成`coverage\lcov-report\index.html`)
  * 不推荐`npm run test`这种方式调用jest,而推荐`npx jest`这种方式
  ```json
  {
    "script": {
      "test": "jest",
      "coverage": "jest --coverage"
    }
  }
  ```
- 创建文件
  ```js
  // math.js
  function add(a, b) {
    return a + b;
  }

  function minus(a, b) {
    return a - b;
  }

  // 必须加上这块代码,因为jest是需要nodejs支持的
  // 加try避免被浏览器报错
  try {
    module.exports = {
      add, minus
    }
  } catch (e) { }
  ```
  ```js
  // math.test.js
  const math = require('./math.js')
  const { add, minus } = math

  describe("一组测试", () => {
    test("test 1+1", () => {
      expect(add(1, 1)).toBe(2)
    })

    test('test 1-1', () => {
      expect(minus(1, 1)).toBe(2)
    })
  })
  ```

## 匹配器
- `expect(minus(1, 1)).toBe(2)`中的`toBe()`就是匹配器
- toBe() 相当于`===`
- toEqual() 相当于`==`
- toBeNull()
- toBeUndefined()
- toBeDefined() // 非undefined
- toBeTruthy()
- toBeFalsy()
- not.toBeFalsy() // 即toBeTruthy()
- toBeGreaterThan(num) // 比num值大
- toBeLessThan(num)
- toBeGreaterThanOrEqual()
- toBeCloseTo(num) // 因为 js 中`0.1 + 0.2 不等于 0.3`, 用于在这种情形下替代 toBeEqual()
- toMatch(正则表达式) // 对字符串执行正则匹配
- toContain() // 希望数组内包含
- not.toThrow() // 希望不要抛出异常

## 命令行
- 运行`npx jest --coverage --watchAll`——改变文件内容后保存,jest将自动执行`jest`
- 按`w`显示更多
- 按`f`只再次执行失败用例 (先按`f`进入模式,再去改动代码)
- 按`o`只再次执行已改变的文件(需使用`git`) (先按`o`进入模式,再去改动代码)

## 异步代码测试
- 测试回调结果
  ```js
  // fetchData.js
  const axios = require('axios');
  export function fetchData(fn) {
    axios.get('http://localhost:8081')
      .then(res => {
        fn(res.data)
      })
  }
  ```
  ```js
  // fetchData.test.js
  import { fetchData } from './fetchData.js'
  // 添加参数done,并在回调中添加done()
  test('fetchData', (done) => {
    fetchData((data) => {
      expect(data).toEqual({ msg: 456 })
      done()
    })
  })
  ```
- 测试单纯的Promise
  ```js
  // fetchData.test.js
  const axios = require('axios');
  export function fetchData() {
    return axios.get('http://localhost:8081')
  }
  ```
  ```js
  import { fetchData } from './fetchData.js'
  test('fetchData', () => {
    return fetchData().then((res) => {
      expect(res.data).toEqual({ msg: 456 })
    })
  })
  ```
- 测试Promise的resolve返回值
  ```js
  // fetchData.js
  const axios = require('axios');
  export function fetchData() {return axios.get('http://localhost:8081')}
  ```
  ```js
  // fetchData.test.js
  import { fetchData } from './fetchData.js'
  test('fetchData', async () => {
    return await expect(fetchData()).resolves.toMatchObject({ data: { msg: '456' } })
  })
  ```
- 测试404
  ```js
  // koa
  let Koa = require('koa');
  let app = new Koa()

  app.use(async (ctx) => {
    // ctx.body = {msg: '456'}
    ctx.status = 404
  })
  app.listen(8081, () => { console.log(new Date()) })
  ```
  ```js
  // fetchData.test.js
  import { fetchData } from './fetchData.js'
  test('fetchData', () => {
    // 下面的语句要求必须跑expert语法
    //（因为我们要求网站404时是true,fetchData().catch在网站不是404时不会执行catch(),此时也是true）
    expect.assertions(1);
    return fetchData().catch(e => {
      expect(e.toString().indexOf('404') > -1).toBe(true)
    })
  })

  // 或者(更简洁)
  import { fetchData } from './fetchData.js'
  test('fetchData', () => {
    return expect(fetchData()).rejects.toThrow();
  })
  ```
- 只测试某个用例（用于调试测试用例）
  ```js
  test.only("注意only修饰符", () => {})
  ```

## 钩子函数
- 相当于vue的生命周期函数
- 钩子函数多用于对`class`实例的测试
- 例子
  ```js
  export default class Num {
    constructor() { this.number = 0 }
    addOne() { this.number += 1 }
    addTwo() { this.number += 2 }
    minusOne() { this.number -= 1 }
    minusTwo() { this.number -= 2 }
  }
  ```
  ```js
  import Num from './num.js'

  describe('test num class', () => {
    let num = null
    // 测试开始前（这里的测试指包围此`beforeAll`的`describe`）
    beforeAll(() => { console.log('beforeAll'); })
    // 每一测试用例开始前
    beforeEach(() => { num = new Num(); console.log('beforeEach'); })
    // 每一测试用例结束后
    afterEach(() => { console.log('afterEach'); })
    // 测试结束后
    afterAll(() => { console.log('afterAll'); })
    describe('test add mothods', () => {
      test('add 1', () => { num.addOne(); expect(num.number).toBe(1) })
      test('add 2', () => { num.addTwo(); expect(num.number).toBe(2) })
    });
    describe('test minus mothods', () => {
      test('miuns 1', () => { num.minusOne(); expect(num.number).toBe(-1) })
      test('miuns 2', () => { num.minusTwo(); expect(num.number).toBe(-2) })
    })
  })
  ```

## mock
- `jest.fn()`功能：测试函数的参数,测试函数返回值,改变函数内部实现(只测试接口是否可用,不测试接口返回值)等
- 有时需要测试ajax请求等,但网络响应太慢等。因此可以把ajax请求换成(mock成)我们自定义的函数
  ```js
  // 被测文件
  export let runCallback = (fn) => {fn()}
  ```
  ```js
  // 测试文件
  import { runCallback } from './num.js'

  test('test runCallback', () => {
    // jest.fn(): 让ject生成一个函数
    const func = jest.fn()
    // 设定函数 func 的返回值是'no'
    func.mockReturnValue('no')
    runCallback(func)
    runCallback(func)

    // 测试函数`runCallback`是否被调用
    expect(func).toBeCalled()
    // 测试函数`runCallback`是否被调用2次
    expect(func.mock.calls.length).toBe(2)
    // 测试函数`runCallback`的传入参数是否为 'yes'
    expect(func.mock.calls[0]).toEqual('yes')
    // 测试函数`runCallback`第一次调用的返回结果的值是`def`
    expect(func.mock.results[0].value).toBe('def')
  })
  ```
- 测试某接口是不是通的
  * 一般前端测试接口只需要测接口是否可用,而接口的返回数据这些是不管的
  * mock其实是不管被测函数的具体实现的,只管函数是否被调用。特定的返回值可以手动返回
  * `jest.fn(() => {})`相当于重写被测函数的具体实现
  * `jest.fn.mockImplementationOnce(() => {})`效果同`jest.fn(() => {})`,但更适用于创建复杂行为的模拟功能的时候,这样多个函数调用产生不同的结果的时候
    ```js
    const myMockFn = jest
      .fn()
      .mockImplementationOnce(cb => cb(null, true))
      .mockImplementationOnce(cb => cb(null, false));
    // 第一次的调用
    myMockFn((err, val) => console.log(val)); // > true
    // 第二次的调用
    myMockFn((err, val) => console.log(val)); // > false
    ```
- mock一个模块
  ```js
  // 一个模块通常有多个方法暴露,一个个单独 jest.fn() 耗费时间, jest.mock() 可以解决这个问题
  jest.mock('模块名字或文件路径');
  // 相当于
  exports.fetchData = jest.fn();
  exports.add = jest.fn();
  exports.subtract = jest.fn();
  exports.multiply = jest.fn();
  ```
  ```js
  // 用法（测试文件）：上面的被mock的方法引入测试文件中
  jest.mock('./func.js');
  let fetchData = require('./func').fetchData;
  test('should fetch data', () => {
    fetchData.mockResolvedValue({ name: 'sam' });
    return fetchData().then(res => {
      expect(res).toEqual({ name: 'sam' })
    })
  })
  ```
- 下面3种mock的写法作用相同
  ```js
  jest.fn(cb => cb(null, true))
  jest.fn().mockImplementationOnce(cb => cb(null, true))
  jest.spyOn(对象, 对象上的一个方法名).mockImplementationOnce(cb => cb(null, true))
  ```
  ```js
  // spyOn的好处是可以监听函数是否被调用而不mock函数
  const math = require('./func');

  test('should call add', () => {
    function callMath(a, b) {
      return math.add(a + b);
    }
    const addMock = jest.spyOn(math, 'add');
    callMath(1, 2);
    expect(addMock).toBeCalled();  // toBeCalled, 就是函数有没有被调用。
  })
  ```

## mock timer
- 对部分需要延时的函数，如`setTimeout(() => {}, 5000)`,我们测试时不一定需要真的等待5秒
- 写法
  ```js
  // demo.js
  export default (cb) => {
    setTimeout(() => {
      cb();
      setTimeout(() => {
        fn();
      }, 2000);
    }, 1000)
  }
  ```
  ```js
  // demo.test.js
  import timer from './demo.js';
  beforeEach(() => {
    // 每个test前,jest的时间重设置为虚拟时间
    jest.useFakeTimers();
  })
  test('test timer', () => {
    const fn = jest.fn();
    timer(fn);
    // 快进1000毫秒
    jest.advancedTimersByTime(1000);
    // 函数已经被调用过
    expect(fn).toHaveBeenCalledTimers(1);
    // 继续快进1000毫秒(累加性质)
    jest.advancedTimersByTime(1000);
    expect(fn).toHaveBeenCalledTimers(1);
  })
  ```

## 对DOM的测试
- jest虽然在nodejs环境，但自身实现了一套DOM的api，故可以在jest操作dom

## Jest配置
- 执行`npx jest --init`,有以下选项,最后生成`jest.config.js`
  * 运行环境
  * 测试覆盖率
  * 每次测试后自动清除

## snapshot
- 项目中经常有一些配置文件。比如
  ```js
  export const generateConfig  = () => {
    return {
      server: 'http://localhost',
      port: '8080'
    }
  }
  ```
- 那测试它的测试用例可以这样写
  ```js
  import { generateConfig } from './snapshot.js'
  test('测试 generateConfig', () => {
    expect(generateConfig()).toEqual({
      server: 'http://localhost',
      port: '8080'
    })
  })
  ```
- 当配置项不断增加的时候，就需要不断去更改测试用例。那么我们可以这样写测试用例：
  ```js
  import { generateConfig } from './snapshot.js'
  
  test('测试 generateConfig', () => {
    expect(generateConfig()).toMatchSnapshot()
  })
  // toMatchSnapshot() 会为expect 的结果做一个快照并与前面的快照做匹配。（如果前面没有快照那就保存当前生成的快照即可）
  ```
- 按`u`可以更新快照（先按`w`，当有多个快照报错时，可以选`i` 一个个更新快照）
- 有一种情况，假设配置项中有随机数或者当前时间，如下。
  ```js
  export const generateConfig  = () => {
    return {
      server: 'http://localhost',
      port: '8080',
      time: new Date()
    }
  }
  ```
  那case 中snapshot 就需要使用expect.any() 了，如下
  ```js
  import { generateConfig } from './snapshot.js'
  test('测试 generateConfig', () => {
    expect(generateConfig()).toMatchSnapshot({
      time: expect.any(Date)
    })
  })
  ```
- 之前使用snapshot 的时候，都会生成一个`__snapshot__`的文件。但它也支持`行内snapshot`。要使用行内snapshot ，我们需要在项目中安装`prettier`
  ```s
  npm install prettier@1.18.2
  ```
  然后，我们改一下测试用例
  ```js
  import { generateConfig } from "./snapshot.js";
  test("测试 generateConfig", () => {
    expect(generateConfig()).toMatchInlineSnapshot({
      time: expect.any(Date)
    });
  });
  ```
  运行测试用例，会发现测试用例代码变成如下了。快照被保存到了测试代码中 
  ```js
  import { generateConfig } from "./snapshot.js";
  test("测试 generateConfig", () => {
    expect(generateConfig()).toMatchInlineSnapshot(
      {
        time: expect.any(Date)
      },
      `
      Object {
        "port": "8080",
        "server": "http://localhost",
        "time": Any<Date>,
      }
    `
    );
  })
  ```



## 参考
- [jest官网](https://jestjs.io/)
- [jest-snapshot知识](https://blog.csdn.net/purple_lumpy/article/details/101110321)
- [jest-mock](https://www.baidu.com/link?url=A7B15zB260yb5J1v_ZGoL6oxhQNkRSbFcmH5dkxNjjBXaImt7GkCRbKI8PFcshKaI73Ozxqo8G355gGNwNbnG_&wd=&eqid=e9445e8f0001a8c0000000065e6c2d6b)