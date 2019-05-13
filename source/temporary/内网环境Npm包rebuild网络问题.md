

### bufferutil

```javascript

package.json

"scripts": {
    "install": "prebuild-install || node-gyp rebuild",
    "test": "mocha && prebuild-ci"
}

```

    node-gyp 需要的 Python 2.7.15

    node-gyp 编译时候需要 NodeJs 源码来提供头文件, 所以它会先尝试下载 NodeJs 头文件  , from　https://nodejs.org　


npm install --verbose 查看详情