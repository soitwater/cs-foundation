# Antd入门

## 安装
```
npm i -S antd
```

## 应用
- 注意在`index.jss`引入`import 'antd/dist/antd.css';`
- 参考`https://ant.design/docs/react/getting-started-cn`

## 使用(按需加载)
- `npm run eject`暴露出`webpack`配置文件
- 安装
  ```shell
  cnpm i babel-plugin-import --save-dev
  cnpm i antd --save
  ```
- 在`package.json`中的`babel`下的`persets`后写入
  * ` "plugins":[["import", {"libraryName": "antd", "style": "css"}]] `
- 使用
  * `import {Button} from 'antd' `

## form
### input
- antd的input组件获取其value的方式
  * this.inputElement.state.value(`this.inputElement`是通过`ref`获取的)
  * `e.target.value`
- 在`input组件`的`onChange`事件中使用`setState`时获取`state`的值慢一拍,原因是`setState`是异步完成的,获取`state`时其实还没执行`setState`
- `input`输入一个字符后会立即失焦,无法输入下一个字符,原因是触发了react重渲染(原因可能是`render`中存在`if-else`分支判断、hoc、循环key不固定唯一)
### 示例
```js
import React, { Component } from 'react';
import Content from '../Home/components/content.js'
import { Form, Input, Button} from 'antd';

class Doc2Readme extends Component {
  handleSubmit(e) {
    e.preventDefault()
    this.props.form.validateFields((err, values) => {
      console.log('mf')
    })
  }
  render() {
    const { getFieldDecorator } = this.props.form
    const checkDocumentPath = {
      rules: [{
        required: true,
        message: '看这里......'
      }, {
        validator: (rule, value, cb) => {
          if (!value) {
            cb('看这里......')
          } else {
            cb()
          }
        }
      }]
    }

    return (
      <Content>
        <Form layout="inline"  onSubmit={this.handleSubmit.bind(this)}>
          <Form.Item label="文件夹路径">
            {getFieldDecorator('documentPath', checkDocumentPath)(<Input type="text" style={{width:'500px'}} placeholder="请输入文件夹路径" />)}
          </Form.Item>
          <Form.Item>
            <Button size="middle" type="primary" htmlType="submit" style={{ marginRight: '10px' }}>
            </Button>
            <Button size="middle">重置</Button>
          </Form.Item>
          <div style={{border: "1px solid #ccc", transform: 'translateY(20px)', borderRadius: '6px', padding:'10px', minHeight: '500px'}}>转换结果...</div>
        </Form>
      </Content>
    )
  }
}

// Form.create 后的组件才有 this.props.form
// 才能使用 getFieldDecorator
export default Form.create()(Doc2Readme)
```
### 双向绑定
- 以前写一堆`onChange`的重复代码实现数据更新，现在使用`Form.create()(组件)`以及`const { getFieldDecorator } = this.props.form`实现数据绑定
- 初始值`{getFieldDecorator('myId', {initialValue:'初始值'})(<Input type="text" />)}`
- 获取表单的值
  ```js
  this.getFieldsValue = this.props.form.getFieldsValue();//获得表单所有控件的值
  // 或者
  this.props.form.getFieldValue('myId') // 获得指定控件的值, myId 是 getFieldDecorator 中的
  ```
### 表单全部验证
  ```js
  this.props.form.validateFields((err, values) => {
   
  })
  ```
- 设置表单的值
  ```js
    this.props.form.setFields({
      "myId": {
        value: 'xxx',
      }
    })
  ```
- 重置整个表单为默认值`this.props.form.resetFields()`


## 重绘
- `state`值改变时,`react`才会去检测是否`update`视图;局部变量`let`或者`const`改变时,即使`component`是根据他们来渲染的,react也不会`update`
- 因此假如不希望触发重渲染,组件所绑定的数据应该用局部变量

## 参考
- [ANTD 官方文档](https://ant.design/docs/react/introduce-cn)
- [ANTD-Form 解析](https://blog.csdn.net/GuanJdoJ/article/details/83306931)
- [ANTD-Form 官方文档](https://ant.design/components/form-cn/#this.props.form.getFieldDecorator(id,-options))