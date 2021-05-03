# element-ui

## 国际化
- [官方文档](https://element.eleme.cn/2.6/#/zh-CN/component/i18n#guo-ji-hua)

## 按需引入
- 方法一
  * npm install babel-plugin-component -D
  * npm install babel-preset-es2015 --save-dev
  * npm i @babel/preset-env -D
  * 在项目根目录创建`.babelrc`
    ```js
    {
      "presets": [["@babel/preset-env", { "modules": false }]],
      "plugins": [
        [
          "component",
          {
            "libraryName": "element-ui",
            "styleLibraryName": "theme-chalk"
          }
        ]
      ]
    }
    ```
  * 重启项目
  * 在`main.js`
    ```js
    import {Tooltip, Button,  Cascader, Checkbox, ColorPicker, Table, TableColumn} from 'element-ui'
    import 'element-ui/lib/theme-chalk/index.css'
    
    Vue.use(Tooltip)
    Vue.use(Button)
    Vue.use(Cascader)
    Vue.use(Checkbox)
    Vue.use(ColorPicker)
    Vue.use(Table)
    Vue.use(TableColumn)
    ```
- 方法二
  * 在vue-cli3.0创建完项目后`vue add element`
  * 再依次选泽`Import on demand` -> `zh-CN`
  * 在项目根目录找到`src/plugins/element.js`
  * 在 `main.js`处引入`import './plugins/element.js'`
  * 注意element-ui的版本，可能存在过低的现象

## 主题定制
- Element-ui支持主体定制,例如改变主题颜色之类的。

## 表格
- `el-table-column`里写入`type='index'`自动排序(编号)
- `el-table-column`里的`prop`的值，是允许这么写的`detail.profile.name`
- 为第一行添加样式
  ```html
  <el-table
    v-loading="loading"
    :data="table.list"
    :header-cell-style="{ 'border-bottom': '1px #aaa solid', 'border-top': '1px solid #aaa' }"
  >
    <el-table-column prop="invitee_phone" label="Invitee Phone" width="180" align="center" />
    <el-table-column prop="invited_time" label="Invitation Time" width="140" align="center" />
    <el-table-column prop="reg_time" label="Registration Time" width="140" align="center" />
    <el-table-column prop="self_approve" label="Personal Certification" width="150" align="center" />
    <el-table-column prop="merchant_approve" label="Merchant Certification" width="150" align="center" />
  </el-table>
  ```
- 固定表头(表格内容有滚动条)
  ```html
  <el-table
    height="250">
  </el-table>
  ```
- 表头居中
  * `<el-table-column prop="date" label="日期" align="center">`
- 为单元格添加`classname`
  ```html
  <el-table :cell-style="isCenter" :header-cell-class-name="headerStyle"></el-table>
  <script>
  export default {
    methods: {
      headerStyle({row, rowIndex}) {
        return 'table-th' // 返回一个类名, 需要在css写好样式
      },
      isCenter({row, column, rowIndex, columnIndex}) { // 样式是直接在js中写好的
        if ((rowIndex === 0 || rowIndex === 1 || row.abstract) && columnIndex === 1) {
          return 'text-align:left'
        }
      }
    }
  }
  </script>
  <style>
    .table-th {
      text-align: center;
    }
  </style>
  ```
- 表格内容过长时使用省略符号
  ```
  <el-table-column :show-overflow-tooltip="true">
  </el-table-column>
  ```
- 表单验证
  * [参考](https://blog.csdn.net/qq_36437172/article/details/88123550)
  * [官方参考](https://element.eleme.cn/2.6/#/zh-CN/component/form)
  * 写法
    - ```html
      <el-form :model="form" :rules="rules" ref="form" class="form-inline" ></el-form>
      <!-- model 与 ref 必须一致-->
      ```
    - ```js
      // inviter_id 是 与 el-form-item 里的 prop 的值 一致的
      // <el-form-item prop="inviter_id"></el-form-item>
      rules: { 
        inviter_id: [{
          required: true,
          trigger: 'blur',
          validator: function(rule, value, callback) {
            if (isNaN(Number('' + value))) {
              callback(new Error('Please enter a number.'))
            }
          }
        }]
      }
      ```
- 表单验证的`*`号的位置
  ```css
  .el-form-item.is-required:not(.is-no-asterisk) .el-form-item__label-wrap>.el-form-item__label:before, .el-form-item.is-required:not(.is-no-asterisk)>.el-form-item__label:after {
    content: '*';
    color: #F56C6C;
    margin-right: 4px;
  }

  .el-form-item.is-required:not(.is-no-asterisk) .el-form-item__label-wrap>.el-form-item__label:before, .el-form-item.is-required:not(.is-no-asterisk)>.el-form-item__label:before {
    content: '';
    color: #F56C6C;
    margin-right: 4px;
  }
  ```
- 表格合并
  ```js
  <el-table
    class="common-table"
    :data="tableData"
    height="600"
    :span-method="objectSpanMethod"
    border
  >
    <el-table-column prop="module" align="center" label="模块"></el-table-column>
    <el-table-column prop="permission" label="功能权限">
      <template slot-scope="scope">
        <el-checkbox v-model="checkboxVal[scope.$index]">{{scope.row.permission}}</el-checkbox>
      </template>
    </el-table-column>
  </el-table>

  data() {
    return {
      tableData: [
        { id: 1, module: "运营看板", permission: "概览" },
        { id: 1, module: "运营看板", permission: "客诉分析" },
        { id: 1, module: "运营看板", permission: "设备画像" },
        { id: 1, module: "运营看板", permission: "用户画像" },
        { id: 2, module: "客户定制", permission: "客户列表" },
        { id: 2, module: "客户定制", permission: "APP列表" },
        { id: 2, module: "客户定制", permission: "APP更新" },
        { id: 2, module: "客户定制", permission: "内容列表" },
        { id: 2, module: "客户定制", permission: "APP语言列表" },
        { id: 2, module: "客户定制", permission: "APP语言资源" },
        { id: 2, module: "客户定制", permission: "APP语言代码" },
        { id: 3, module: "设备系统", permission: "设备列表" },
        { id: 3, module: "设备系统", permission: "固件升级" },
        { id: 3, module: "设备系统", permission: "表盘列表" },
        { id: 3, module: "设备系统", permission: "表盘资源" },
        { id: 3, module: "设备系统", permission: "表盘分类" },
        { id: 3, module: "设备系统", permission: "设备语言列表" },
        { id: 3, module: "设备系统", permission: "设备语言资源" },
        { id: 3, module: "设备系统", permission: "设备语言代码" },
        { id: 4, module: "后台配置", permission: "常见问题列表" },
        { id: 4, module: "后台配置", permission: "常见问题分组" },
        { id: 4, module: "后台配置", permission: "H5模板" },
        { id: 4, module: "后台配置", permission: "消息配置" },
        { id: 5, module: "售后支持", permission: "用户反馈列表" },
      ],
      checkboxVal: [],
      spanArr: [], // 第 i 个元素表示当前的 tableData[i] 他会占据几行, 0 表示不显示
    }
  }

  mounted: {
    this.gormatTableData(this.tableData);
    this.checkboxVal = new Array(this.tableData.length).fill(false);
  }

  methods: {
    objectSpanMethod({ row, column, rowIndex, columnIndex }) {
      if (columnIndex === 0) {
        const _row = this.spanArr[rowIndex];
        const _col = _row > 0 ? 1 : 0;
        return {
          rowspan: _row,
          colspan: _col,
        };
      }
    },
    gormatTableData(data) {
      let spanArr = this.spanArr;
      // pos 表示当前的 spanArr 的索引(会占据多行的那个位置)
      let pos = 0;
      for (let i = 0; i < data.length; i++) {
        if (i === 0) {
          // tableData[0] 必然占据一行
          spanArr.push(1);
          pos = 0;
        } else {
          // 判断当前元素与上一个元素是否相同
          if (data[i].id === data[i - 1].id) {
            spanArr[pos] += 1;
            spanArr.push(0);
          } else {
            this.spanArr.push(1);
            pos = i;
          }
        }
      }
    },
  }
  ```

  
## NavMenu
- handleSelect 点击切换页面
- 高度:直接在`<el-menu-item class="right-tab__item" index="1"></el-menu-item>`,再在`css`中写好`.right-tab__item`样式

## 时间选择器
- new Date(2019, 10, 10, 10, 10), 月份其实是 11 月,其他和看到的一样
- 限制时间最多只能选择到今天
  ```html
  <el-date-picker
    :picker-options="pickerOptions"
    type="daterange"
  />
  <script>
  export default {
    data() {
      return {
        pickerOptions: {
          disabledDate(time) {
            return time.getTime() >= Date.now() // 起始时间是过去,截至时间是今天
            return time.getTime() < Date.now() // 起始时间是今天,截至时间是是未来
          }
        }
      }
    }
  }
  </script>
  ```

## 分页器
- 一个常见的bug：分页器的搜索条件应该和表单的搜索条件分开,例如：分页器切换页面时提交的搜索条件,应该和表单form双向绑定的数据分开
```html
<el-pagination
  @size-change="handleSizeChange"
  @current-change="handleCurrentChange"
  :page-sizes="[8, 10, 20, 30]"
  :page-size="10"
  layout="total, sizes, prev, pager, next, jumper"
  :total="page.totalPage"
>
</el-pagination>
```

## 按钮
- disabled
  ```js
  < el-button type = "primary" :disabled = " closeIsDisabled " @click = " closeBoxCabin(cabinParam, '2' ) " > 关仓 </ el-button >
  ```

## 弹框自定义
```js
this.$msgbox({ // search成功则： 弹窗提示跳转
   message: h('div', null, [
     h('div', { style: 'color: #409EFF' }, [
       h('span', null, 'Ad already exists, click the button "Jump" to jump'),
     ]),
   ]),
   showCancelButton: true,
   confirmButtonText: 'Jump',
   cancelButtonText: 'Cancel',
}).then(() => {
   this.$router.push({
    path: '/ads-weight-allocation/detail',
    query: {
      type: 'details',
      id: data.advert_id
    }
  }) 
})
```

## element-ui 修改样式的方法
* 去掉scoped
* 建立一个公用样式文件，覆盖ElementUI的样式，并全局引入
* 如果使用了css预处理器，可以了解下深度作用选择器
* 通过父选择器选择到当前class，然后进行覆盖

- [官方](https://element.eleme.cn/2.6/#/zh-CN/component/quickstart)
- [vue 按需引入element-ui时遇到的坑](https://blog.csdn.net/f056917/article/details/88789648)
- [vue按需引入Element UI的方法](https://www.cnblogs.com/gitByLegend/p/10960504.htmlv)
- [关于element-ui表格使用的一些方法](https://www.cnblogs.com/steamed-twisted-roll/p/9120924.html)


## 表单验证 resetFields	
- 对整个表单进行重置，将所有字段值重置为初始值并移除校验结果
- 要求
  * `form`要设置`ref`，调用`this.$refs[formName].resetFields()`
  * `el-form-item`上设置`prop`字段，表单`rule验证`和`resetfields()`清理的都是`prop`绑定的字段	
  * 重置为初始值是在表单的`mounted`生命周期之后,若在`mounted`直接赋值,需要`this.$nextTick(() => {})`

### clearValidate	
- 移除表单项的校验结果。
- 传入待移除的表单项的`prop`属性或者`prop`组成的数组，如不传则移除整个表单的校验结果`Function(props: array | string)`

### 参考
- [官方文档](https://element.eleme.cn/2.13/#/zh-CN/component/form#form-item-attributes)