# Gist

## Github-Weekly-WakaTime
- [参考1](https://juejin.im/post/5ebdf4135188256d5f4cfb67)
- [参考2](https://github.com/matchai/waka-box)
- [官方文档](https://wakatime.com/developers)
- 过程需要的链接
  * https://gist.github.com/
  * https://github.com/settings/tokens/new
  * https://wakatime.com/settings/profile
  * https://wakatime.com/settings/api-key

## 注意
- 如果需要调试,请手动填写下面的 `id` 与 `token` 与 `key`
  ```js
  const {
    GIST_ID: gistId,
    GH_TOKEN: githubToken,
    WAKATIME_API_KEY: wakatimeApiKey
  }
  ```
- IDE中需要安装`WakaTime`的插件
- 修改了项目`WAKA-BOX`中的`index.js`之后，需要重新`npm run build`,更新`dist/index.js`
- fork项目`WAKA-BOX`之后,需要在github中`Action`中重新创建一个workflow`new.yml`
- `wakaTime`需要系统有安装`Python`?
- 可能需要第二天才能看到Github中的`Gist`更新?