TODO：
- 加上有道云笔记上的总结

### 宏任务(task)、微任务(micro-task/jobs)

#### 宏任务(task)
- script(整体代码)
- setTimeout、setInterval、setImmediate
- I/O、UI交互事件
- postMessage、MessageChannel

#### 微任务(micro-task/jobs)
- Promise.then
- [MutationObserver](https://developer.mozilla.org/zh-CN/docs/Web/API/MutationObserver)
- process.nextTick(Node环境)

注：
new Promise传入的方法是立即执行的

#### 相关文章
- [译文：JS事件循环机制（event loop）之宏任务、微任务](https://segmentfault.com/a/1190000014940904)

---

```js
function sum(...args) {
  const value = args.reduce((a, b) => a+b, 0);
  const func = (...args) => sum(value, ...args);
  func.valueOf = () =>  value;
  return func;
}

sum(1)(2,3)(4).valueOf();
```

--- 
### shadow dom
独立封装的一段html代码块，他所包含的html标签、css样式和js行为都可以隔离、隐藏起来。如Video

---

### 重绘（redraw或repaint）,重排（reflow）
1. 根据HTML构建Dom树
2. DOM树结合CSS，构建渲染树。render-tree
3. 布局渲染树，通过遍历计算出每个元素的精选定位。（重排）
4. 渲染。（重绘）

优化：
1. 批量修改DOM，使用fragment
2. 修改样式名代替修改具体样式。
3. 会触发多次重排的可以使用absolute或者fixed，脱离文档流不会导致重绘重排。

相关资料
- [重绘（redraw或repaint）,重排（reflow）](https://www.cnblogs.com/cencenyue/p/7646718.html)

---

### 页面性能优化
- 静态资源优化
    - 压缩资源、使用cdn、合并请求、CssSprite、缓存资源及一些业务数据
    - 代码分割成多bundle的形式，预下发资源（离线包）
    - 异步加载

- 业务代码优化
    - 首屏服务端渲染
    - 控制DOM数量
    - 长列表分屏，复用DOM
    - 复用代码
    - react 优化渲染

- 客户端相关
    - 配合渲染主结构。
    - 下发公共参数

---

### 缓存

#### 200 from cache 强缓存
决定于max-age，不会发起请求

#### 304 协商缓存
e-tag、last-modified
- 服务器优先检测e-tag
- e-tag安全性更强，性能较last-modified差。
- 服务器需要校验的使用etag

---

### HTTP 2.0

#### HTTP1.0 存在问题
- 一个TCP链接只有一个请求，阻塞。
- 单向请求，只能由客户端发起。
- 请求报文与响应报文首部信息冗余量大。
- 数据未压缩，导致数据的传输量大。

HTTP2.0 
1. 二进制分帧
2. 多路复用 共享链接
3. 服务端推送
4. 首部压缩

---

### 安全问题
#### XSS跨站脚本攻击
- 输入过滤
- 输出转码

#### CSRF请求伪造
使用referer或者token

#### sql注入
#### 文件上传漏洞