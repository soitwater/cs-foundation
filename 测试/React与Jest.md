# React与Jest

## 新建项目
- create-react-app 名字
- npm run eject
- 新建`jest.config.js`,将`package.json`中的`jest`部分剪切至`jest.config.js`
  ```js
  module.exports = {
    "roots": [
      "<rootDir>/src"
    ],
    "collectCoverageFrom": [
      // 需要算入代码覆盖率的文件
      "src/**/*.{js,jsx,ts,tsx}",
      // 忽略的文件
      "!src/**/*.d.ts"
    ],
    "setupFiles": [
      // 测试前需要做的事
      "react-app-polyfill/jsdom"
    ],
    "setupFilesAfterEnv": [
      // 测试环境建立后做的事情
      "<rootDir>/src/setupTests.js"
    ],
    "testMatch": [
      // 需要测试的文件
      "<rootDir>/src/**/__tests__/**/*.{js,jsx,ts,tsx}",
      "<rootDir>/src/**/*.{spec,test}.{js,jsx,ts,tsx}"
    ],
    // 在nodejs环境模拟DOM
    "testEnvironment": "jest-environment-jsdom-fourteen",
    "transform": {
      // 在测试文件中，引入下列文件（符合正则的文件）时，将对引入文件执行的转换
      "^.+\\.(js|jsx|ts|tsx)$": "<rootDir>/node_modules/babel-jest",
      "^.+\\.css$": "<rootDir>/config/jest/cssTransform.js",
      "^(?!.*\\.(js|jsx|ts|tsx|css|json)$)": "<rootDir>/config/jest/fileTransform.js"
    },
    "transformIgnorePatterns": [
      // 引入下列模块时，对其忽略转化
      "[/\\\\]node_modules[/\\\\].+\\.(js|jsx|ts|tsx)$",
      "^.+\\.module\\.(css|sass|scss)$"
    ],
    "modulePaths": [
      // 全局（默认）模块的位置
      "E:\\nodejs\\node_modules"
    ],
    "moduleNameMapper": {
      // css样式一般不需要测试，identity-obj-proxy是第三方模块，用于在引入css文件时
      "^react-native$": "react-native-web",
      "^.+\\.module\\.(css|sass|scss)$": "identity-obj-proxy"
    },
    "moduleFileExtensions": [
      "web.js",
      "js",
      "web.ts",
      "ts",
      "web.tsx",
      "tsx",
      "json",
      "web.jsx",
      "jsx",
      "node"
    ],
    "watchPlugins": [
      // 运行npm run test时，会执行的文件
      "jest-watch-typeahead/filename",
      "jest-watch-typeahead/testname"
    ]
  }
  ```
- 运行`npm run test`,执行`src`文件夹下的测试文件

## 测试React组件(enzyme)
- github搜索`enzyme`,如`https://github.com/enzymejs/enzyme`,enzyme也支持测试dom
- enzyme官方文档`https://enzymejs.github.io/enzyme/`
- `npm i --save-dev enzyme enzyme-adapter-react-16`
- 在测试文件
  ```js
  import React from 'react'
  import App from '../App.js';
  import Enzyme, { shallow } from 'enzyme';
  import Adapter from 'enzyme-adapter-react-16';
  Enzyme.configure({ adapter: new Adapter() });

  it("shallow ==>", () => {
    // shallow 浅渲染 - 只关注<App />组件, 不关注<App />的子组件
    const wrapper = shallow(<App />);
    // 测试页面内容(根据快照),页面内容不能更改
    expect(wrapper).toMatchSnapshot();
    // 测试dom
    expect(wrapper.find('.App').length).toBe(1)
    // 测试组件的prop
    expect(wrapper.find('.App').prop('title')).toBe('App')
    expect(wrapper.find('[title="App"]').prop('title')).toBe('App')
    expect(wrapper.find('[title="App"]')).toExist()
  })
  ```
- `enzyme`使用`.simulate()`模拟事件
- `enzyme`使用`.update()`更新组件(多用于`jest.mock('axios')`时)

## jest-enzyme
- 使用jest-enzyme改进enjyme(更简洁的api/匹配器)
- 官网`https://www.npmjs.com/package/jest-enzyme#toexist`
- cnpm i -D jest-enzyme
- `jest.config.js`中
  ```js
  {
    "setupFilesAfterEnv": ['./node_modules/jest-enzyme/lib/index.js'],
  }
  ```
- 在测试文件使用新api
  ```js
  expect(wrapper.find('.App')).toExist()
  ```

## 简化测试代码
- 一些初始化引入的测试工具不应该在每个测试文件的头部都重复声明引入
  * 创建`util/setup.js`
    ```js
    import Enzyme, { shallow } from 'enzyme';
    import Adapter from 'enzyme-adapter-react-16';
    Enzyme.configure({ adapter: new Adapter() });
    ```
  * 在`jest.config.js`
    ```js
    {
      "setupFilesAfterEnv": [
        // <rootDir> 表示项目根目录
        "<rootDir>/src/setupTests.js"
      ]
    }
    ```

## 代码覆盖率
- 在`package.json`
  ```json
  {
    "scripts": {
      "coverage": "node scripts/test.js --coverage --watchAll=false"
    },
  }
  ```

## 测试Redux
- 一般是BDD，先写代码，再写测试
- 因为引入了redux,在测试文件中也需要添加redux引用
  ```js
  import { Provider } from 'react-redux';
  import store from '@/store/createStore';
  it(`test redux`, () => {
    const wrapper = mount(
      <Provider store={store}><TestComponent /></Provider>
    )
  })
  ```


## 参考
- [别人的听课笔记(Dell lee的前端测试课)](https://www.dazhuanlan.com/2019/11/08/5dc4590109202/?__cf_chl_jschl_tk__=175d81ae7dc4536611eb27fc07cfd1b8bbe0d4ff-1584427427-0-ARRAsEZBMOgTxlMbespYW0dy4JG6eRlA5p0J029YMSWGQWjUoSGwVzrgmd-cRlbYHJ8G8zHrYubkD5DUj7ACEyYdvIBsbNzWhnmr8v_70rOJBEVmYMyGmEe8M3ETJmrbvy-VdpNH5_eCRk5jLN63bcvp50HCgVxUcBsWfCsWLjxqpzCvyrJS1bBeeZG0sR11exvSAH_E4VxoVARFqmAe8NJZkW4PD2wDJfA5D-QcHBWdr3bnEtQP8fx5xnErXRUV2I92fXREFDa7MP3dX9z4YnuPRl0jEgkqN8B6eAKkV5IUVFAhU2j2n1CA_fPpERPBcA)