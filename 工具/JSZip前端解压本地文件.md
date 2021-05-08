# JSZip前端解压本地文件
- 前置工作
  ```js
  cnpm i -S jszip
  ```
- 引入
  ```js
  import JSZip from 'jszip';
  ```
- 解压
  ```js
  export async function unzipPackage(file) {
    let zip = new JSZip();
    let name = '';
    return new Promise(async (resolve, reject) => {
      zip.loadAsync(file)
      .then((res) => {
        for (let key in res.files) {
          // 不是目录
          if (!res.files[key].dir) {
            // 是json文件
            if (/\.(json)$/.test(res.files[key].name)) {
              res.file(res.files[key].name).async('string')
                .then(content => {
                  console.log('content 是文本内容');
                  resolve();
                });
            }
          }
        }
      })
      .catch(err => {
        reject('错误?!')
        console.warn('1519 unzip error:\n', err);
      });
    });
  }
  ```

## 参考
- [JSZip压缩与解压的基本使用](https://www.jianshu.com/p/cbe494ebff7e)