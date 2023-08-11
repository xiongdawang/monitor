
## 1. 为什么要做前端监控

- 更快发现问题和解决问题
- 做产品的决策依据
- 提升前端工程师的技术深度和广度，打造简历亮点
- 为业务扩展提供了更多可能性

## 2. 前端监控目标

### 2.1 稳定性（stability）

|错误名称|备注|
|----|----|
|JS错误|JS执行错误或者promise异常|
|资源异常|script、link等资源加载异常|
|接口错误|ajax或fetch请求接口异常|
|白屏|页面空白|

### 2.2 用户体验（experience）

|错误名称|备注|
|----|----|
|加载时间|各个阶段的加载时间|
|TTFB(time to first byte)(首字节时间)|是指浏览器发起第一个请求到数据返回第一个字节所消耗的时间，这时间包括了网络请求时间、后端处理时间|
|FP(First Paint)(首次绘制)|首次绘制包括了任何用户自定义的背景绘制，它是将第一个像素点绘制到屏幕的时刻|
|FCP(First Content Paint)(首次内容绘制)|首次内容绘制是浏览器将第一个DOM渲染到屏幕的时间，可以是任何文本、图像、SVG等的时间|
|FMP(First Meaningful Paint)(首次有意义绘制)|首次有意义绘制是页面可用性的量度标准|
|FID(First Input Delay)(首次输入延迟)|用户首次和页面交互到页面响应交互的时间|
|卡顿|超过50ms的长任务|

### 2.3 业务（business）

|错误名称|备注|
|----|----|
|PV|page view即页面浏览量或点击量|
|UV|指访问某个站点的不同IP地址的人数|
|页面的停留时间|用户在每一个页面的停炉时间|

## 3. 前端监控流程


### 3.1 常见的埋点方案

#### 3.1.1 代码埋点

- 代码埋点，就是以嵌入代码的形式埋点，比如需要监控用户的点击事件，会选择在用户点击时，插入一段代码，保存这个监听行为或者直接将监听行为以某一种数据格式直接传递给服务器端
- 优点是可以在任意时刻，精确的发送或保存所需要的数据信息
- 缺点是工作量较大

#### 3.1.2 可视化埋点

- 通过可视化交互的手段，代替代码埋点
- 将业务代码和埋点代码分离，提供一个可视化交互的页面，通过这个可视化系统，可以在业务代码中自定义的增加埋点事件等等，最后输出的代码耦合了业务代码和埋点代码
- 可视化埋点其实是用系统来代替手工插入埋点代码

#### 3.1.3 无痕埋点

- 前端的任意一个事件都被绑定一个标识，所有的事件都被记录下来
- 通过定期上传记录文件，配合文件解析，解析出来我们想要的数据，并生成可视化报告供专业人员分析
- 无痕埋点的优点是采集全量数据，不会出现漏埋和误埋等现象
- 缺点是给数据传输和服务器增加压力，也无法灵活定制数据结构

## 4. 编写监控采集脚本

### 4.1 开通日志服务

- 日志服务(Log Service, 简称 SLS)是针对日志类数据一站式服务，用户无需开发就能快捷完成数据采集、消费、投递以及查询分析等功能，帮助提升运维、运营效率，建立DT时代海量日志处理能力
- 日志服务帮助文档
- Web Tracking

### 4.2 监控错误

#### 4.2.1 错误分类

- JS 错误
    - JS 错误
    - Promise 异常

- 资源异常
    - 监听 error

#### 4.2.2 数据结构设计

##### 1. jsError

```js
{
    "title": "前端监控系统", // 页面标题
    "url": "http://localhost:8080/", // 页面URL
    "timestamp": "1151341241",
    "userAgent": "Chrome", // 用户浏览器类型
    "kind": "stability", // 大类
    "type": "error", // 小类
    "errorType": "jsError", // 错误类型
    "message": "Uncaught TypeError: Cannot", // 类型详情
    "filename": "http://localhost:8080/", // 访问的文件名
    "position": "0:0", // 行列信息
    "stack": "btnClick ()...", // 堆栈信息
    "selector": "HTML BODY #container .content INPUT" // 选择器
}
```

##### 2. promiseError

```js
{
    "title": "前端监控系统", // 页面标题
    "url": "http://localhost:8080/", // 页面URL
    "timestamp": "1151341241",
    "userAgent": "Chrome", // 用户浏览器类型
    "kind": "stability", // 大类
    "type": "error", // 小类
    "errorType": "promiseError", // 错误类型
    "message": "someVar is not defined", // 类型详情
    "filename": "http://localhost:8080/", // 访问的文件名
    "position": "0:0", // 行列信息
    "stack": "btnClick ()...", // 堆栈信息
    "selector": "HTML BODY #container .content INPUT" // 选择器
}
```

##### 3. resourceError

```js
{
    "title": "前端监控系统", // 页面标题
    "url": "http://localhost:8080/", // 页面URL
    "timestamp": "1151341241",
    "userAgent": "Chrome", // 用户浏览器类型
    "kind": "stability", // 大类
    "type": "error", // 小类
    "errorType": "resourceError", // 错误类型
    "message": "someVar is not defined", // 类型详情
    "filename": "http://localhost:8080/", // 访问的文件名
    "position": "0:0", // 行列信息
    "stack": "btnClick ()...", // 堆栈信息
    "selector": "HTML BODY #container .content INPUT" // 选择器
}
```

### 4.3 接口异常采集脚本

#### 4.3.1 数据设计

```js
{
    "title": "前端监控系统", // 页面标题
    "url": "http://localhost:8080/", // 页面URL
    "timestamp": "1151341241",
    "userAgent": "Chrome", // 用户浏览器类型
    "kind": "stability", // 大类
    "type": "xhr", // 小类
    "eventType": "load", // 事件类型
    "pathname": "/success", // 路径
    "status": "200-OK", // 状态码
    "duration": "7", // 持续时间
    "response": "{\"id\":1}", // 响应内容
    "params": "" // 参数
}
```

### 4.3.2 实现

#### 1. scr/index.html

```html

```

## 4.4 白屏

- 白屏就是页面上什么都没有

### 4.4.1 数据设计

```js
{
    "title": "前端监控系统", // 页面标题
    "url": "http://localhost:8080/", // 页面URL
    "timestamp": "1151341241",
    "userAgent": "Chrome", // 用户浏览器类型
    "kind": "stability", // 大类
    "type": "blank", // 小类
    "emptyPoints": "0", // 空白点
    "screen": "2049x1152", // 分辨率
    "viewPoint": "2048x994", // 视口
    "selector": "HTML BODY #container" // 选择器
}
```

### 4.4.2 实现

- screen 返回当前window的screen对象，返回当前渲染窗口中和屏幕有关的属性
- innnerWidth 只读的window属性 innerWidth 返回以像素为单位的窗口的内部宽度
- innerHeight 窗口的内部高度（布局视口）的高度
- layout_viewport
- elementsFromPoint 方法可以获取到当前视口内指定坐标处，由里到外排列的所有元素


### 4.5.2 阶段计算

|字段|描述|计算方式|意义|
|----|----|----|----|
|unload|前一个页面卸载耗时|unloadEventEnd - unloadEventStart|-|
|redirect|重定向耗时|redirectEnd - redirectStart|重定向的时间|
|appCache|缓存耗时|domainLookupStart - fetchStart|读取缓存的时间|
|dns|DNS解析耗时|domainLookupEnd - domainLookupStart|可观察域名解析服务是否正常|
|tcp|TCP连接耗时|connectEnd - connectStart|建立连接的耗时|
|ssl|SSL安全连接耗时|connectEnd - secureConnectionStart|反映数据安全连接建立耗时|
|ttfb|Time to First Byte(TTFB)网络请求耗时|responseStart - requestStart|TTFB是发出页面请求到接收到应答数据第一个字节所花费的毫秒数|
|reponse|响应数据传输耗时|responseEnd - responseStart|观察网络是否正常|
|dom|DOM解析耗时|domInteractive - responseEnd|观察DOM结构是否合理，是否有JS阻塞页面解析|
|dcl|DOMContentLoaded事件耗时|domContentLoadedEventEnd - domContentLoadedStart|当HTML文档被完全加载和解析完成之后，DOMContentLoaded事件等待样式表、图像和子框架的完成加载|
|resource|资源加载耗时|domComplete - domContentLoadedEventEnd|可观察文档流是否过大|
|domReady|DOM阶段渲染耗时|domContentLoadedEventEnd - fetchStart|DOM树和页面资源加载完成时间，会触发domContentLoaded事件|
|首次渲染耗时|首次渲染耗时|responseEnd - fetchStart|加载文档到看到第一个非空图像的时间，也叫白屏时间|
|首次可交互时间|首次可交互时间|domInteractive - fetchStart|DOM树解析完成时间，此时document.readyState为interactive|
|首包时间耗时|首包时间|responseStart - domainLookupStart|DNS解析到响应返回给浏览器第一个字节的时间|
|页面完全加载时间|页面完全加载时间|loadEventStart - fetchStart|-|
|onload|onLoad事件耗时|loadEventEnd - loadEventStart|-|

### 4.5.3 数据结构

```js

```

### 4.5.4 实现


## 4.6 性能指标

- PerformanceObserver.observe方法用于观察传入的参数中指定的性能条目类的集合。当记录一个指定类型的性能条目时，性能检测对象的回调函数将会被调用
- entryType
- paint-timing
- event-timing
- LCP
- FMP
- time-to-interactive

|字段|描述|备注|
|----|----|----|
|FP|First Paint(首次绘制)|包括了任何用户自定义的背景绘制，它是首先将像素绘制到屏幕的时刻|
|FCP|First Content Paint(首次内容绘制)|是浏览器第一个DOM渲染到屏幕的时间，可能是文本、图像、SVG等，这其实就是白屏时间|
|FMP|First Mainingful Paint(首次有意义绘制)|页面有意义的内容渲染时间|
|LCP|(Largest Contentful Paint)(最大内容渲染)|代表在viewport中最大的页面元素加载时间|
|DCL|DomContentLoaded(DOM加载完成)|当HTML文档被完全加载和解析完成之后，DOMContentLoaded事件被触发，无需等待样式表、图像和子框架的完成加载|
|L|onload|当以来的资源全部加载完成之后才会触发|
|TTI|Time to interactive(可交互时间)|用于标记应用已进行视觉渲染并能可靠响应用户输入的时间点|
|FID|First Input Delay(首次输入延迟)|用户首次和页面交互（单击链接，点击按钮等）到页面响应交互的时间|




