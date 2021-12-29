# Web 安全

## 1.XSS攻击

XSS(Cross-Site Scripting，跨站脚本攻击)是一种代码注入攻击。攻击者在目标网站上注入恶意代码，当被攻击者登陆网站时就会执行这些恶意代码，这些脚本可以读取 cookie，session tokens，或者其它敏感的网站信息，对用户进行钓鱼欺诈，甚至发起蠕虫攻击等。

**XSS 的本质是**：恶意代码未经过滤，与网站正常的代码混在一起；浏览器无法分辨哪些脚本是可信的，导致恶意脚本被执行。由于直接在用户的终端执行，恶意代码能够直接获取用户的信息，利用这些信息冒充用户向网站发起攻击者定义的请求。

XSS分类

根据攻击的来源，XSS攻击可以分为存储型(持久性)、反射型(非持久型)和DOM型三种。下面我们来详细了解一下这三种XSS攻击：

### 1. 反射型XSS

当用户点击一个恶意链接，或者提交一个表单，或者进入一个恶意网站时，注入脚本进入被攻击者的网站。Web服务器将注入脚本，比如一个错误信息，搜索结果等，未进行过滤直接返回到用户的浏览器上。

反射型 XSS 的攻击步骤：

1. 攻击者构造出特殊的 URL，其中包含恶意代码。
2. 用户打开带有恶意代码的 URL 时，网站服务端将恶意代码从 URL 中取出，拼接在 HTML 中返回给浏览器。
3. 用户浏览器接收到响应后解析执行，混在其中的恶意代码也被执行。
4. 恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

反射型 XSS 漏洞常见于通过 URL 传递参数的功能，如网站搜索、跳转等。由于需要用户主动打开恶意的 URL 才能生效，攻击者往往会结合多种手段诱导用户点击。

POST 的内容也可以触发反射型 XSS，只不过其触发条件比较苛刻（需要构造表单提交页面，并引导用户点击），所以非常少见。

查看反射型攻击示例

请戳： https://github.com/YvetteLau/Blog/tree/master/Security

根据 README.md 的提示进行操作(真实情况下是需要诱导用户点击的，上述代码仅是用作演示)。

注意Chrome 和 Safari 能够检测到 url 上的xss攻击，将网页拦截掉，但是其它浏览器不行，如Firefox

如果不希望被前端拿到cookie，后端可以设置 httpOnly (不过这不是 XSS攻击 的解决方案，只能降低受损范围)

### 如何防范反射型XSS攻击

#### 对字符串进行编码。

对url的查询参数进行转义后再输出到页面。
```
app.get('/welcome', function(req, res) {
    //对查询参数进行编码，避免反射型 XSS攻击
    res.send(`${encodeURIComponent(req.query.type)}`); 
});
```

## 2.DOM 型 XSS

DOM 型 XSS 攻击，实际上就是前端 JavaScript 代码不够严谨，把不可信的内容插入到了页面。在使用 .innerHTML、.outerHTML、.appendChild、document.write()等API时要特别小心，不要把不可信的数据作为 HTML 插到页面上，尽量使用 .innerText、.textContent、.setAttribute() 等。

**DOM 型 XSS 的攻击步骤：**

1. 攻击者构造出特殊数据，其中包含恶意代码。
2. 用户浏览器执行了恶意代码。
3. 恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

### 如何防范 DOM 型 XSS 攻击

防范 DOM 型 XSS 攻击的核心就是对输入内容进行转义(DOM 中的内联事件监听器和链接跳转都能把字符串作为代码运行，需要对其内容进行检查)。

1. 对于url链接(例如图片的src属性)，那么直接使用 `encodeURIComponent` 来转义。
2. 非url，我们可以这样进行编码：
```
function encodeHtml(str) {
    return str.replace(/"/g, '&quot;')
            .replace(/'/g, '&apos;')
            .replace(/</g, '&lt;')
            .replace(/>/g, '&gt;');
}
```

DOM 型 XSS 攻击中，取出和执行恶意代码由浏览器端完成，属于前端 JavaScript 自身的安全漏洞。

查看DOM型XSS攻击示例(根据readme提示查看)

请戳： https://github.com/YvetteLau/Blog/tree/master/Security

## 3.存储型XSS

恶意脚本永久存储在目标服务器上。当浏览器请求数据时，脚本从服务器传回并执行，影响范围比反射型和DOM型XSS更大。存储型XSS攻击的原因仍然是没有做好数据过滤：前端提交数据至服务端时，没有做好过滤；服务端在接受到数据时，在存储之前，没有做过滤；前端从服务端请求到数据，没有过滤输出。

### 存储型 XSS 的攻击步骤：
1. 攻击者将恶意代码提交到目标网站的数据库中。
2. 用户打开目标网站时，网站服务端将恶意代码从数据库取出，拼接在 HTML 中返回给浏览器。
3. 用户浏览器接收到响应后解析执行，混在其中的恶意代码也被执行。
4. 恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

这种攻击常见于带有用户保存数据的网站功能，如论坛发帖、商品评论、用户私信等。

**如何防范存储型XSS攻击：**

1. 前端数据传递给服务器之前，先转义/过滤(防范不了抓包修改数据的情况)
2. 服务器接收到数据，在存储到数据库之前，进行转义/过滤
3. 前端接收到服务器传递过来的数据，在展示到页面前，先进行转义/过滤

查看存储型XSS攻击示例(根据Readme提示查看)

请戳： https://github.com/YvetteLau/Blog/tree/master/Security

**除了谨慎的转义，我们还需要其他一些手段来防范XSS攻击:**

### 1. `Content Security Policy`
在服务端使用 HTTP的 Content-Security-Policy 头部来指定策略，或者在前端设置 meta 标签。
例如下面的配置只允许加载同域下的资源：

`Content-Security-Policy: default-src 'self'`

`<meta http-equiv="Content-Security-Policy" content="form-action 'self';">`

前端和服务端设置 CSP 的效果相同，但是meta无法使用report
更多的设置可以查看 Content-Security-Policy 

严格的 CSP 在 XSS 的防范中可以起到以下的作用：
1. 禁止加载外域代码，防止复杂的攻击逻辑。
2. 禁止外域提交，网站被攻击后，用户的数据不会泄露到外域。
3. 禁止内联脚本执行（规则较严格，目前发现 GitHub 使用）。
4. 禁止未授权的脚本执行（新特性，Google Map 移动版在使用）。
5. 合理使用上报可以及时发现 XSS，利于尽快修复问题。

### 2.输入内容长度控制

对于不受信任的输入，都应该限定一个合理的长度。虽然无法完全防止 XSS 发生，但可以增加 XSS 攻击的难度。

### 3.输入内容限制

对于部分输入，可以限定不能包含特殊字符或者仅能输入数字等。

### 4.其他安全措施

- HTTP-only Cookie: 禁止 JavaScript 读取某些敏感 Cookie，攻击者完成 XSS 注入后也无法窃取此 Cookie。
- 验证码：防止脚本冒充用户提交危险操作。

## 4.XSS 检测

读到这儿，相信大家已经知道了什么是XSS攻击，XSS攻击的类型，以及如何去防范XSS攻击。但是有一个非常重要的问题是：我们如何去检测XSS攻击，怎么知道自己的页面是否存在XSS漏洞？

很多大公司，都有专门的安全部门负责这个工作，但是如果没有安全部门，作为开发者本身，该如何去检测呢？

1. 使用通用 XSS 攻击字串手动检测 XSS 漏洞
如: 
```
jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert() )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert()//>\x3e
```
能够检测到存在于 HTML 属性、HTML 文字内容、HTML 注释、跳转链接、内联 JavaScript 字符串、内联 CSS 样式表等多种上下文中的 XSS 漏洞，也能检测 `eval()`、`setTimeout()`、`setInterval()`、`Function()`、`innerHTML`、`document.write()` 等 DOM 型 XSS 漏洞，并且能绕过一些 XSS 过滤器。

`<img src=1 onerror=alert(1)>`

2.使用第三方工具进行扫描（详见最后一个章节）


## c2. CSRF

CSRF（Cross-site request forgery）跨站请求伪造：攻击者诱导受害者进入第三方网站，在第三方网站中，向被攻击网站发送跨站请求。利用受害者在被攻击网站已经获取的注册凭证，绕过后台的用户验证，达到冒充用户对被攻击的网站执行某项操作的目的。

**典型的CSRF攻击流程：**

1. 受害者登录A站点，并保留了登录凭证（Cookie）。
2. 攻击者诱导受害者访问了站点B。
3. 站点B向站点A发送了一个请求，浏览器会默认携带站点A的Cookie信息。
4. 站点A接收到请求后，对请求进行验证，并确认是受害者的凭证，误以为是无辜的受害者发送的请求。
5. 站点A以受害者的名义执行了站点B的请求。
6. 攻击完成，攻击者在受害者不知情的情况下，冒充受害者完成了攻击。
一图胜千言：

**CSRF的特点**
 
1. 攻击通常在第三方网站发起，如图上的站点B，站点A无法防止攻击发生。
2. 攻击利用受害者在被攻击网站的登录凭证，冒充受害者提交操作；并不会去获取cookie信息(cookie有同源策略)
3. 跨站请求可以用各种方式：图片URL、超链接、CORS、Form提交等等(来源不明的链接，不要点击)

运行代码，更直观了解一下
用户 loki 银行存款 10W。
用户 yvette 银行存款 1000。
我们来看看 yvette 如何通过 CSRF 攻击，将 loki 的钱偷偷转到自己的账户中，并根据提示，查看如何去防御CSRF攻击。

请戳： https://github.com/YvetteLau/Blog/tree/master/Security[根据readme中的CSRF部分进行操作]

### CSRF 攻击防御

1. 添加验证码(体验不好)
验证码能够防御CSRF攻击，但是我们不可能每一次交互都需要验证码，否则用户的体验会非常差，但是我们可以在转账，交易等操作时，增加验证码，确保我们的账户安全。
2. 判断请求的来源：检测Referer(并不安全，Referer可以被更改)
`Referer` 可以作为一种辅助手段，来判断请求的来源是否是安全的，但是鉴于 `Referer` 本身是可以被修改的，因为不能仅依赖于  `Referer`

3. 使用Token(主流)
CSRF攻击之所以能够成功，是因为服务器误把攻击者发送的请求当成了用户自己的请求。那么我们可以要求所有的用户请求都携带一个CSRF攻击者无法获取到的Token。服务器通过校验请求是否携带正确的Token，来把正常的请求和攻击的请求区分开。跟验证码类似，只是用户无感知。

- 服务端给用户生成一个token，加密后传递给用户
- 用户在提交请求时，需要携带这个token
- 服务端验证token是否正确

4. Samesite Cookie属性
为了从源头上解决这个问题，Google起草了一份草案来改进HTTP协议，为Set-Cookie响应头新增Samesite属性，它用来标明这个 Cookie是个“同站 Cookie”，同站Cookie只能作为第一方Cookie，不能作为第三方Cookie，Samesite 有两个属性值，分别是 Strict 和 Lax。
部署简单，并能有效防御CSRF攻击，但是存在兼容性问题。
Samesite=Strict
Samesite=Strict 被称为是严格模式,表明这个 Cookie 在任何情况都不可能作为第三方的 Cookie，有能力阻止所有CSRF攻击。此时，我们在B站点下发起对A站点的任何请求，A站点的 Cookie 都不会包含在cookie请求头中。

**Samesite=Lax**

`Samesite=Lax` 被称为是宽松模式，与 Strict 相比，放宽了限制，允许发送安全 HTTP 方法带上 Cookie，如 `Get` / `OPTIONS` 、`HEAD` 请求.

但是不安全 HTTP 方法，如： `POST`, `PUT`, `DELETE` 请求时，不能作为第三方链接的 Cookie

为了更好的防御CSRF攻击，我们可以组合使用以上防御手段。

3. 点击劫持

点击劫持是指在一个Web页面中隐藏了一个透明的iframe，用外层假页面诱导用户点击，实际上是在隐藏的frame上触发了点击事件进行一些用户不知情的操作。
典型点击劫持攻击流程
1. 攻击者构建了一个非常有吸引力的网页【不知道哪些内容对你们来说有吸引力，我就不写页面了，偷个懒】
2. 将被攻击的页面放置在当前页面的 iframe 中
3. 使用样式将 iframe 叠加到非常有吸引力内容的上方
4. 将iframe设置为100%透明
5. 你被诱导点击了网页内容，你以为你点击的是***，而实际上，你成功被攻击了。

**点击劫持防御**
1. `frame busting`
```
Frame busting
if ( top.location != window.location ){
    top.location = window.location
}
```

需要注意的是: HTML5中iframe的 sandbox 属性、IE中iframe的security 属性等，都可以限制iframe页面中的JavaScript脚本执行，从而可以使得 frame busting 失效。

2. `X-Frame-Options`
X-FRAME-OPTIONS是微软提出的一个http头，专门用来防御利用iframe嵌套的点击劫持攻击。并且在IE8、Firefox3.6、Chrome4以上的版本均能很好的支持。
可以设置为以下值:
- DENY: 拒绝任何域加载
- SAMEORIGIN: 允许同源域下加载
- ALLOW-FROM: 可以定义允许frame加载的页面地址
安全扫描工具
上面我们介绍了几种常见的前端安全漏洞，也了解一些防范措施，那么我们如何发现自己网站的安全问题呢？没有安全部门的公司可以考虑下面几款开源扫码工具：
1. Arachni
Arachni是基于Ruby的开源，功能全面，高性能的漏洞扫描框架，Arachni提供简单快捷的扫描方式，只需要输入目标网站的网址即可开始扫描。它可以通过分析在扫描过程中获得的信息，来评估漏洞识别的准确性和避免误判。
Arachni默认集成大量的检测工具，可以实施 代码注入、CSRF、文件包含检测、SQL注入、命令行注入、路径遍历等各种攻击。
同时，它还提供了各种插件，可以实现表单爆破、HTTP爆破、防火墙探测等功能。
针对大型网站，该工具支持会话保持、浏览器集群、快照等功能，帮助用户更好实施渗透测试。
2. Mozilla HTTP Observatory
Mozilla HTTP Observatory，是Mozilla最近发布的一款名为Observatory的网站安全分析工具，意在鼓励开发者和系统管理员增强自己网站的安全配置。用法非常简单：输入网站URL，即可访问并分析网站HTTP标头，随后可针对网站安全性提供数字形式的分数和字母代表的安全级别。
检查的主要范围包括：
- Cookie
- 跨源资源共享（CORS）
- 内容安全策略（CSP）
- HTTP公钥固定（Public Key Pinning）
- HTTP严格安全传输（HSTS）状态
- 是否存在HTTP到HTTPs的自动重定向
- 子资源完整性（Subresource Integrity）
- X-Frame-Options
- X-XSS-Protection
3. w3af
W3af是一个基于Python的Web应用安全扫描器。可帮助开发人员，有助于开发人员和测试人员识别Web应用程序中的漏洞。
扫描器能够识别200多个漏洞，包括跨站点脚本、SQL注入和操作系统命令。


# web 应用常见安全漏洞一览

## 1. SQL 注入

SQL 注入就是通过给 web 应用接口传入一些特殊字符，达到欺骗服务器执行恶意的 SQL 命令。

SQL 注入漏洞属于后端的范畴，但前端也可做体验上的优化。

### 原因

当使用外部不可信任的数据作为参数进行数据库的增、删、改、查时，如果未对外部数据进行过滤，就会产生 SQL 注入漏洞。

比如：

```
name = "外部输入名称";

sql = "select * from users where name=" + name;
```

上面的 SQL 语句目的是通过用户输入的用户名查找用户信息，因为由于 SQL 语句是直接拼接的，也没有进行过滤，所以，当用户输入 `'' or '1'='1'` 时，这个语句的功能就是搜索 `users` 全表的记录。

```
select * from users where name='' or '1'='1';
```

### 解决方案

具体的解决方案很多，但大部分都是基于一点：不信任任何外部输入。

所以，对任何外部输入都进行过滤，然后再进行数据库的增、删、改、查。

此外，适当的权限控制、不曝露必要的安全信息和日志也有助于预防 SQL 注入漏洞。

参考 [Web 安全漏洞之 SQL 注入 - 防御方法](https://juejin.im/post/5bd5b820e51d456f72531fa8#heading-2) 了解具体的解决方案。

### 推荐参考

- [Web 安全漏洞之 SQL 注入](https://juejin.im/post/5bd5b820e51d456f72531fa8)
- [SQL 注入详解](https://segmentfault.com/a/1190000007520556)

## 2. XSS 攻击

XSS 攻击全称跨站脚本攻击（Cross-Site Scripting），简单的说就是攻击者通过在目标网站上注入恶意脚本并运行，获取用户的敏感信息如 Cookie、SessionID 等，影响网站与用户数据安全。

XSS 攻击更偏向前端的范畴，但后端在保存数据的时候也需要对数据进行安全过滤。

### 原因

当攻击者通过某种方式向浏览器页面注入了恶意代码，并且浏览器执行了这些代码。

比如：

在一个文章应用中（如微信文章），攻击者在文章编辑后台通过注入 `script` 标签及 `js` 代码，后端未加过滤就保存到数据库，前端渲染文章详情的时候也未加过滤，这就会让这段 `js` 代码执行，引起 XSS 攻击。

### 解决方案

一个基本的思路是渲染前端页面（不管是客户端渲染还是服务器端渲染）或者动态插入 HTML 片段时，任何数据都不可信任，都要先做 HTML 过滤，然后再渲染。

参考 [前端安全系列（一）：如何防止 XSS 攻击？ - 攻击的预防](https://segmentfault.com/a/1190000016551188#articleHeader7) 了解具体的解决方案。

### 推荐参考

- [前端安全系列（一）：如何防止 XSS 攻击？](https://segmentfault.com/a/1190000016551188)
- [前端防御 XSS](https://juejin.im/entry/56da82a87664bf0052ebad41)
- [浅说 XSS 和 CSRF](https://juejin.im/entry/5b4b56fd5188251b1a7b2ac1)

## 3. CSRF 攻击

CSRF 攻击全称跨站请求伪造（Cross-site Request Forgery），简单的说就是攻击者盗用了你的身份，以你的名义发送恶意请求。

### 原因

一个典型的 CSRF 攻击有着如下的流程：

- 受害者登录 `a.com`，并保留了登录凭证（Cookie）
- 攻击者引诱受害者访问了 `b.com`
- `b.com` 向 `a.com` 发送了一个请求：`a.com/act=xx`（浏览器会默认携带 `a.com` 的 Cookie）
- `a.com` 接收到请求后，对请求进行验证，并确认是受害者的凭证，误以为是受害者自己发送的请求
- `a.com` 以受害者的名义执行了 `act=xx`
- 攻击完成，攻击者在受害者不知情的情况下，冒充受害者，让 `a.com` 执行了自己定义的操作

注：上面的过程摘自 [前端安全系列之二：如何防止 CSRF 攻击？](https://segmentfault.com/a/1190000016659945)

### 解决方案

防止 CSRF 攻击需要在服务器端入手，基本的思路是能正确识别是否是用户发起的请求。

参考 [前端安全系列之二：如何防止 CSRF 攻击？ - 防护策略](https://segmentfault.com/a/1190000016659945#articleHeader4) 了解具体的解决方案。

### 推荐参考

- [前端安全系列之二：如何防止 CSRF 攻击？](https://segmentfault.com/a/1190000016659945)
- [Web 安全漏洞之 CSRF](https://juejin.im/post/5ba1a800e51d450e8657f5dd)
- [浅说 XSS 和 CSRF](https://juejin.im/entry/5b4b56fd5188251b1a7b2ac1)

## 4. DDoS 攻击

DoS 攻击全称拒绝服务（Denial of Service），简单的说就是让一个公开网站无法访问，而 DDoS 攻击（分布式拒绝服务 Distributed Denial of Service）是 DoS 的升级版。

这个就完全属于后端的范畴了。

### 原因

攻击者不断地提出服务请求，让合法用户的请求无法及时处理，这就是 DoS 攻击。

攻击者使用多台计算机或者计算机集群进行 DoS 攻击，就是 DDoS 攻击。

### 解决方案

防止 DDoS 攻击的基本思路是限流，限制单个用户的流量（包括 IP 等）。

参考 [DDoS 的攻击及防御 - 防御](https://segmentfault.com/a/1190000016584829#articleHeader19) 了解具体的解决方案。

### 推荐参考

- [DDoS 的攻击及防御](https://segmentfault.com/a/1190000016584829)
- [浅谈 DDoS 攻击与防御](https://juejin.im/entry/5b7a21256fb9a01a031aef67)
- [使用 Nginx、Nginx Plus 抵御 DDOS 攻击](https://juejin.im/entry/56d824591ea493005db9d284)

## 5. XXE 漏洞

XXE 漏洞全称 XML 外部实体漏洞（XML External Entity），当应用程序解析 XML 输入时，如果没有禁止外部实体的加载，导致可加载恶意外部文件和代码，就会造成任意文件读取、命令执行、内网端口扫描、攻击内网网站等攻击。

这个只在能够接收 XML 格式参数的接口才会出现。

### 解决方案

1. 禁用外部实体
2. 过滤用户提交的 XML 数据

参考 [xxe 漏洞的学习与利用总结](https://www.cnblogs.com/r00tuser/p/7255939.html) 了解具体的解决方案。

### 推荐参考

- [好刚: 6 分钟视频看懂 XXE 漏洞攻击](https://juejin.im/entry/5b719fdc6fb9a009a0607aaa)
- [xxe 漏洞的学习与利用总结](https://www.cnblogs.com/r00tuser/p/7255939.html)
- [XXE 漏洞攻防学习（上）](https://www.cnblogs.com/ESHLkangi/p/9245404.html)

## 6. JSON 劫持

JSON 劫持（JSON Hijacking）是用于获取敏感数据的一种攻击方式，属于 CSRF 攻击的范畴。

### 原因

一些 Web 应用会把一些敏感数据以 json 的形式返回到前端，如果仅仅通过 Cookie 来判断请求是否合法，那么就可以利用类似 CSRF 的手段，向目标服务器发送请求，以获得敏感数据。

比如下面的链接在已登录的情况下会返回 json 格式的用户信息：

```
http://www.test.com/userinfo
```

攻击者可以在自己的虚假页面中，加入如下标签：

```
<script src="http://www.test.com/userinfo"></script>
```

如果当前浏览器已经登录了 `www.test.com`，并且 Cookie 未过期，然后访问了攻击者的虚假页面，那么该页面就可以拿到 json 形式的用户敏感信息，因为 `script` 标签会自动解析 json 数据，生成对应的 js 对象。然后再通过：

```
Object.prototype.__defineSetter__
```

这个函数来触发自己的恶意代码。

但是这个函数在当前的新版本 Chrome 和 Firefox 中都已经失效了。

注：上面的过程摘自 [JSON 和 JSONP 劫持以及解决方法](https://blog.csdn.net/yjclsx/article/details/80353754)

### 解决方案

1. `X-Requested-With` 标识
2. 浏览器 JSON 数据识别
3. 禁止 Javascript 执行 JSON 数据

### 推荐参考

- [JSON 和 JSONP 劫持以及解决方法](https://blog.csdn.net/yjclsx/article/details/80353754)
- [JSONP 安全攻防技术（JSON 劫持、 XSS 漏洞）](https://www.cnblogs.com/52php/p/5677775.html)

## 7. 暴力破解

这个一般针对密码而言，弱密码（Weak Password）很容易被别人（对你很了解的人等）猜到或被破解工具暴力破解。

### 解决方案

1. 密码复杂度要足够大，也要足够隐蔽
2. 限制尝试次数

## 8. HTTP 报头追踪漏洞

HTTP/1.1（RFC2616）规范定义了 HTTP TRACE 方法，主要是用于客户端通过向 Web 服务器提交 TRACE 请求来进行测试或获得诊断信息。

当 Web 服务器启用 TRACE 时，提交的请求头会在服务器响应的内容（Body）中完整的返回，其中 HTTP 头很可能包括 Session Token、Cookies 或其它认证信息。攻击者可以利用此漏洞来欺骗合法用户并得到他们的私人信息。

### 解决方案

禁用 HTTP TRACE 方法。

## 9. 信息泄露

由于 Web 服务器或应用程序没有正确处理一些特殊请求，泄露 Web 服务器的一些敏感信息，如用户名、密码、源代码、服务器信息、配置信息等。

所以一般需注意：

- 应用程序报错时，不对外产生调试信息
- 过滤用户提交的数据与特殊字符
- 保证源代码、服务器配置的安全

## 10. 目录遍历漏洞

攻击者向 Web 服务器发送请求，通过在 URL 中或在有特殊意义的目录中附加 `../`、或者附加 `../` 的一些变形（如 `..\` 或 `..//` 甚至其编码），导致攻击者能够访问未授权的目录，以及在 Web 服务器的根目录以外执行命令。

## 11. 命令执行漏洞

命令执行漏洞是通过 URL 发起请求，在 Web 服务器端执行未授权的命令，获取系统信息、篡改系统配置、控制整个系统、使系统瘫痪等。

## 12. 文件上传漏洞

如果对文件上传路径变量过滤不严，并且对用户上传的文件后缀以及文件类型限制不严，攻击者可通过 Web 访问的目录上传任意文件，包括网站后门文件（`webshell`），进而远程控制网站服务器。

所以一般需注意：

- 在开发网站及应用程序过程中，需严格限制和校验上传的文件，禁止上传恶意代码的文件
- 限制相关目录的执行权限，防范 `webshell` 攻击

## 13. 其他漏洞

1. SSLStrip 攻击
2. OpenSSL Heartbleed 安全漏洞
3. CCS 注入漏洞
4. 证书有效性验证漏洞

## 14. 业务漏洞

一般业务漏洞是跟具体的应用程序相关，比如参数篡改（连续编号 ID / 订单、1 元支付）、重放攻击（伪装支付）、权限控制（越权操作）等。

另外可以参考：[6 种常见 web 漏洞坑](https://blog.csdn.net/xueshao110/article/details/78912988)

## 15. 框架或应用漏洞

- WordPress 4.7 / 4.7.1：REST API 内容注入漏洞
- Drupal Module RESTWS 7.x：Remote PHP Code Execution
- SugarCRM 6.5.23：REST PHP Object Injection Exploit
- Apache Struts：REST Plugin With Dynamic Method Invocation Remote Code Execution
- Oracle GlassFish Server：REST CSRF
- QQ Browser 9.6：API 权限控制问题导致泄露隐私模式
- Hacking Docker：Registry API 未授权访问

