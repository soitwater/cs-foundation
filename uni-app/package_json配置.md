# package_json配置
- 参考：https://uniapp.dcloud.io/collocation/pages?id=tips-tabbar
```json
{
  // “page” 的第一项会是小程序的启动页
	"pages": [{
		"path": "pages/index/index",
		"style": {
      // 局部页面的名称
			"navigationBarTitleText": "uni-ui基础项目"
		}
	}    ,{
            "path" : "pages/write/write",
            "style" : {}
        }
        ,{
            "path" : "pages/my/my",
            "style" : {}
        }
    ],
	"globalStyle": {
		"navigationBarTextStyle": "white",
    // 小程序名字
		"navigationBarTitleText": "uni-app",
		"navigationBarBackgroundColor": "#007AFF",
		"backgroundColor": "#FFFFFF",
    // 允许下拉
    "enablePullDownRefresh":true
	},
  // 如果应用是一个多 tab 应用，可以通过 tabBar 配置项指定 tab 栏的表现，以及 tab 切换时显示的对应页
  // tabbar 的页面展现过一次后就保留在内存中，再次切换 tabbar 页面，只会触发每个页面的onShow，不会再触发onLoad
  // 尽量不放在顶部(因为除微信小程序外的其他小程序不一定支持)
  "tabBar": {
    // tab 上的文字默认颜色
    "color": "#707070",
    // tab 上的文字选中时的颜色
    "selectedColor": "#DE533A",
    // tab 的背景色
    "backgroundColor": "#ffffff",
    "borderStyle": "#000000",
    // 可选值："bottom"(限微信) "top"
    "position": "bottom",
    "fontSize": "10px",
    "iconWidth": "24px",
    // icon 与 文字的间距
    "spacing": "3px",
    // tabBar 默认高度
    "height": "50px",
    // Object类型，中间按钮，仅在 list 项为偶数时有效
    "midButton": {
      "width": "80px",
      "height": "50px",
      "text": "我",
      "iconPath": "",
      "iconWidth": "",
      "backgroundImage": ""
    },
    // list 是一个数组，只能配置最少2个、最多5个 tab，tab 按数组的顺序排序
    "list": [
      {
        "pagePath": "pages/index/index",
        "text": "文章",
        "iconPath": "static/comment.png",
        "selectedIconPath": "static/comment.png"
      },
      {
        "pagePath": "pages/index/index",
        "text": "写作"
      }
    ]
  },
  // 启动模式配置，仅开发期间生效，用于模拟直达页面的场景，如：小程序转发后，用户点击所打开的页面（因为小程序不能通过输入url到达其他页面）
  "condition": { //模式配置，仅开发期间生效
    "current": 0, //当前激活的模式（list 的索引项）
    "list": [{
        "name": "swiper", //页面名称
        "path": "pages/component/swiper/swiper", //启动页面，必选
        "query": "interval=4000&autoplay=false" //启动参数，在页面的onLoad函数里面得到。
      }, {
        "name": "test",
        "path": "pages/component/switch/switch"
      }
    ]
  }
}
```