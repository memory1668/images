# 准备工作
1. 生成打包报告
2. 第三方库启用 CDN
3. Element-UI 组件按需加载
4. 路由懒加载
5. 首页内容定制
# 上线过程
## 数据库
1. 通过sqlyog创建数据库mydb
2. 通过文件mydb.sql还原数据库
## 后台接口
1. npm install安装所有依赖
2. 修改mysql配置文件的密码为888888, 配置文件的路径vue_api_server/config/default.json
3. ==开启https==，添加下面的语句
```
const https = require('https')
const fs = require('fs')
const options = {
     cert: fs.readFileSync('1_www.kangjie.club_bundle.crt'),
     key: fs.readFileSync('2_www.kangjie.club.key')
}
https.createServer(options, app).listen(8888)
```

3. pm2 start app.js --name vue_api_server启动项目后台
## 前端web服务器
1. 创建空目录vue_shop_server
2. npm init -y初始化node项目
3. 安装express,compression
4. ==修改上线环境的入口文件main-prod.js, 修改axios的基地址==

```
// 上线的域名要改成服务器域名或主机号
axios.defaults.baseURL = 'https://www.kangjie.club:8888/api/private/v1/'
```
否则会报如下错误

![image](http://ruankj.gitee.io/image_bed/yd/ERR_CONNECTION_REFUSED.png)

5. 拷贝打包生成的dist目录到vue_shop_server
6. 新建文件app.js
```
const express = require('express')
const compression = require('compression')
const https = require('https')
const fs = require('fs')

// 创建 web 服务器
const app = express()
// 使用gzip压缩,注意写在托管静态资源前
app.use(compression())
// 托管静态资源
app.use(express.static('./dist'))
// 导入ssl证书
const options = {
    cert: fs.readFileSync('1_www.kangjie.club_bundle.crt'),
    key: fs.readFileSync('2_www.kangjie.club.key')
}
// 启动 web 服务器
// app.listen(8088, () => {
// console.log('web server running at http://127.0.0.1:8088')
// })
// https协议默认是443端口
https.createServer(options, app).listen(443)
```
6. pm2 start app.js --name vue_shop_server 启动web服务器
7. 本地浏览器打开https://www.kangjie.club


