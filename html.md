### 行块自闭合元素
- 行：span、a、img、input、strong
- 块：div、p、h1、ul、ol、li、table
- 自闭合：br、img、input、meta、link、hr
### 对SSG的理解
静态网站生成，指构建时预先生成静态页，将页面部署到存储服务。
- 使用模板引擎等创建静态页模板
- 通过API等获取数据填充模板，生成完整HTML页面
- 构建工具(Next.js)等对静态页进行构建，生成静态H、C、J文件。
- 将静态文件部署到服务器或CDN。
  优势：
- 加载速度快：无须每次动态渲染
- 安全性高：无后台代码数据库
- 成本低：无动态服务器
### Node与Element关系
Element继承于Node，又扩展了自己的方法。
平时使用的html上元素，即Element，是类型为ELEMENT_NODE的Node。
### 输入url到页面的画面展示的过程
1. 在浏览器地址栏中输入url
2. 浏览器先查看浏览器缓存-系统缓存-路由器缓存，如果缓存中有，直接显示页面内容。若没有，则3。
3. DNS(域名解析)，获取相应IP地址。
4. 浏览器向服务端发起tcp连接，建立tcp三次握手。
5. 握手成功，浏览器向服务端发送http请求数据包。
6. 服务器处理收到的请求，将数据返回到浏览器。
7. 浏览器收到HTTP响应。
8. 读取页面内容，浏览器渲染，解析html源码。
9. 生成DOM树，解析css样式、js交互，渲染显示页面。
- 浏览器下载html后，首先解析头部代码，进行样式表下载。
- 继续向下解析html，构建DOM树，同时进行样式下载。
- DOM树构建完成后，立即构造CSSOM树。
- 两棵树构建完毕，构建渲染树，绘制。
- 遇到内联JS代码，会阻塞DOM树构建，先执行完JS。
- 当CSS文件未下载完成，浏览器解析html遇到内联JS，会暂停JS执行，暂停html解析，直到CSS文件下载完成。
- JS会阻塞DOM生成，样式文件下载会阻塞JS执行。
### 浏览器预览待上传图片关键方法
- window.URL.cteateObejectURL
- 使用FileReader读取
### 回到顶部方案
1. 锚点
2. `document.body.scrollTop = document.documentElement.scrollTop = 0`
3. scrollTo(0,0)
4. 元素id.scrollIntoView
### 跨页面通信方案
#### 同源页面
1. **BroadCast Channel**
   创建一个广播的通信频道，当所有页面监听同一频道消息，其中一个页面通过它发送的消息就会被其他页面收到。
```
// 创建频道
const bc = new BroadcastChannel('liam');
// 将实例导出到各页面监听
bc.onmessage = function(e) {
	console.log('receive', e.data);
}
// 发送
bc.postMessage('Hello')
// 关闭
bc.close();
```
2. **Service Worker**
   是一个可以长期运行在后台的 Worker，能够实现与页面的双向通信。
3. **LocalStorage**
   原理：当 LocalStorage 变化时，会触发storage事件。
```
window.addEventListener('storage', function (e) {
    if (e.key === 'ctc-msg') {
        const data = JSON.parse(e.newValue);
        const text = '[receive] ' + data.msg + ' —— tab ' + data.from;
        console.log('[Storage I] receive message:', text);
    }
});
// 加时间戳是因为只有值改变才会触发事件
mydata.st = +(new Date);
window.localStorage.setItem('ctc-msg', JSON.stringify(mydata));
```
4. **Shared Worker**
- 多个 Tab 注册的 Shared Worker 可以实现数据共享，但它无法主动通知所有页面，要采用轮询拉取最新数据。
- 支持两种消息。post，Shared Worker收到会将数据保存下来。get，收到该消息会将保存的数据通过postMessage传给注册它到页面。
5. **IndexedDB**
   消息发送方将消息存至 IndexedDB 中；接收方（例如所有页面）则通过轮询去获取最新的信息。
6. 口口相传模式 window.open + window.opener
#### 非同源页面之间的通信
iframe
### SVG与Canvas区别
**SVG**
1. 不依赖分辨率
2. 支持事件处理器
3. 适合大型渲染的应用
4. 不适合游戏应用

**Canvas**
1. 依赖分辨率
2. 不支持事件处理器
3. 弱的文本渲染能力
4. 能以.png .jpg保存结果图
5. 适合图像密集型的游戏
### HTML5的离线存储是？
用户未联网时，可以正常访问站点或应用；用户联网时，更新用户机器的缓存文件。

**原理**

基于新建.appcache文件的缓存机制，通过文件解析清单离线存储资源。

**使用**
1. 创建与html同名的manifest文件，在页面头部加入属性
   `<html lang="en" manifest="index.manifest">`
2. 在 cache.manifest 文件中编写需要离线存储的资源
3. 在离线状态时，操作 window.applicationCache 进行离线缓存的操作
### img的srcset属性
用于设置不同屏幕密度下，img 会自动加载不同的图片。
`<img src="image-128.png" srcset="image-256.png 2x" />`
新的sercset标准，可以设置可视区域质量自动选择最小的可用图片
```
<img src="image-128.png"
     srcset="image-128.png 128w, image-256.png 256w, image-512.png 512w"
     sizes="(max-width: 360px) 340px, 128px" />
```
### js和css如何影响DOM树构建
- css不会阻塞DOM解析，但会影响js的运行。
- js会阻塞DOM树解析，最终css会影响DOM树渲染。
### DOM与BOM
- DOM是文档对象模型，主要定义处理网页内容的方法接口。
- BOM是浏览器对象模型，主要定义了与浏览器交互的方法接口。BOM的核心是window，包含location对象 navigator对象。screen对象等，且DOM的最基本对象document对象也是BOM的window的子对象。
### link与@import的区别
1. link是html标签，而@import是css提供的
2. 页面被加载时，link会同时被加载。@import引用的css要等到页面加载完再加载
3. link的样式权重高于@import的权重
### HTML5新特性
- 新增语义化标签：nav、header、footer、aside、section
- 音视频标签：audio、video
- 数据存储：localStorage、sessionStorage
- canvas、Geolocation、websocket
- historyAPI：go、forward、back、pushstate
### src与href的区别
1. href是超文本引用，指向资源位置建立文件联系
2. src目的是把资源下载到页面中
3. 浏览器解析href不会阻塞文档处理。src会阻塞文档处理
### script标签中的async与defer
**async**
会使脚本异步加载并在允许情况下执行，不会按照script标签的顺序，而是先加载完的先执行。

**defer**
浏览器会异步下载该文件并不会影响后续DOM渲染。会在文档渲染完毕后，DOMContentLoaded事件调用前执行。
### 常用meta元素
1. charset 指定文档编码
   `<meta charset="utf-8">`
2. name & content
- author 作者
- keywords 关键字
- description 整体描述
- viewport 页面视图定义
- generator 包含生成页面软件的标识符
- theme-color 主题色
3. http-equiv & content
- refresh 每多少秒刷新文档
  `<meta http-equiv="refresh" content="30">`
- X-UA-Compatible 告知浏览器以何种版本渲染界面
- Cache-Control 请求和响应遵循的缓存机制
