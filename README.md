## 本仓库与原仓库的区别

[源仓库](https://github.com/tangramor/wx_jsapi_sign)

本仓库相较于原仓库，添加了打包为 Docker 镜像的方式。

### 获取

从 Docker Hub 下载镜像:

```bash
docker pull dingjunyao/wx_jsapi_sign:latest
```

或者是从 GitHub Packages 下载镜像:

```bash
docker pull ghcr.io/dingjunyao/wx_jsapi_sign:latest
```

或者从源码构建:

```bash
docker build -t dingjunyao/wx_jsapi_sign:latest .
```

### 使用

以下展示使用 Docker Hub 的镜像的示例。请将代码中的一些字符串替换为你自己的配置情况。

以 docker 命令行方式运行:

```bash
docker run \
    -d \
    --name qexo \
    --port PORT:3000 \
    -e COMPANY_NAME="Beijing Dream Castle Culture Co., Ltd" \
    -e PROJECT_NAME=梦之城微信控制中心 \
    -e SYSTEM_EMAIL=xxx@a-li.com.cn \
    -e CRYPTO_KEY=k4yb0ardc4x \
    -e LOCAL_SOURCE=dreamcastle \
    -e APP_ID=wx9999999999 \
    -e APP_SECRET=ad73709c6e0815c999999999999 \
    -e WHITELIST=https://4ading.com,http://localhost:4000 \
    --restart unless-stopped \
    dingjunyao/wx_jsapi_sign:latest
```

以 Docker Compose 配置文件运行:

```yaml
version: "3.9"
services:
  wx_jsapi_sign:
    container_name: wx_jsapi_sign
    image: dingjunyao/wx_jsapi_sign:latest
    ports:
      - PORT:3000
    environment:
      - COMPANY_NAME="Beijing Dream Castle Culture Co., Ltd"
      - PROJECT_NAME=梦之城微信控制中心
      - SYSTEM_EMAIL=xxx@a-li.com.cn
      - CRYPTO_KEY=k4yb0ardc4x
      - LOCAL_SOURCE=dreamcastle
      - APP_ID=wx9999999999
      - APP_SECRET=ad73709c6e0815c999999999999
      - WHITELIST=https://4ading.com,http://localhost:4000  # CORS, 留空允许全部
    restart: unless-stopped
```

其中 `APP_ID` 和 `APP_SECRET` 根据你的实际情况修改。

# wx_jsapi_sign
The Nodejs server for manage Wechat (Wexin) access_token, jsapi ticket and signature generation. 用于管理微信JS API的access_token、ticket和根据参数生成签名

使用了Redis作为数据存储，所以在运行环境中必须安装了redis服务器

## 使用方法
1. 将config.example.js拷贝为config.js，修改配置文件内的AppId和AppSecret，其他参数也可以根据自己需求修改
2. 在项目根目录下运行 npm install，安装依赖模块
3. 在项目根目录下运行 node app.js，即可按照routes.js里的路径来调用相关API了，例如：
```
http://localhost:3000/api/getWechatJsapiSign/
```

## 接口
### getWechatToken
获取微信 access token，7200秒刷新一次 ( http://mp.weixin.qq.com/wiki/15/54ce45d8d30b6bf6758f68d2e95bc627.html )

参数：需要正确设置config.js


### getWechatJsapiTicket
获取微信 JS API 所要求的 ticket，7200秒刷新一次 ( http://mp.weixin.qq.com/wiki/7/aaa137b55fb2e0456bf8dd9148dd613f.html )

参数：需要正确设置config.js


### getWechatJsapiSign
根据用户参数生成微信 JS API 要求的签名 ( http://mp.weixin.qq.com/wiki/7/aaa137b55fb2e0456bf8dd9148dd613f.html )

参数：
 * `noncestr` 必须参数，使用者自己生成的一个随机字符串，签名用的noncestr必须与wx.config中的nonceStr相同
 * `timestamp` 必须参数，使用者在调用微信 JS API 时的Unix时间戳，签名用的timestamp必须与wx.config中的timestamp相同
 * `url` 必须参数，签名用的url必须是调用JS接口页面的完整URL，其中的特殊字符，例如&、空格必须转义为%26、%20，参考：http://www.w3school.com.cn/tags/html_ref_urlencode.html
