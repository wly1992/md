# HTTP缓存详解
## HTTP的缓存的过程是怎样的？

通常情况下的步骤是:

1. 客户端向服务器发出请求，请求资源 
2. 服务器返回资源，并通过响应头决定缓存策略 
3. 客户端根据响应头的策略决定是否缓存资源（这⾥假设是），并将响应头与资源缓存下来 
4. 在客户端再次请求且命中资源的时候，此时客户端去检查上次缓存的缓存策略，根据策略的不同、是否过期等判断 是直接读取本地缓存还是与服务器协商缓存

## 什么时候会触发强缓存或者协商缓存？

强缓存离不开两个响应头 Expires 与 Cache-Control

- Expires：Expires是http1.0提出的⼀个表示资源过期时间的header，它描述的是⼀个绝对时间，由服务器返回， Expires 受限于本地时间，如果修改了本地时间，可能会造成缓存失效

`Expires: Wed, 11 May 2018 07:20:00 GMT`

- Cache-Control: Cache-Control 出现于 HTTP / 1.1，优先级⾼于 Expires ,表示的是相对时间（资源存在时间不大于这个时间）

`Cache-Control: max-age=315360000`

⽬前主流的做法使⽤ Cache-Control 控制缓存，除了 max-age 控制过期时间外，还有⼀些不得不提:

- Cache-Control: public可以被所有⽤户缓存，包括终端和CDN等中间代理服务器 
- Cache-Control: private只能被终端浏览器缓存，不允许中继缓存服务器进⾏缓存 
- Cache-Control: no-cache,先缓存本地，但是在命中缓存之后必须与服务器验证缓存的新鲜度才能使⽤ 
- Cache-Control: no-store，不会产⽣任何缓存

在缓存有效期内命中缓存，浏览器会直接读取本地的缓存资源，当缓存过期之后会与服务器进⾏协商。

## 协商缓存

当第⼀次请求时服务器返回的响应头中没有Cache-Control和Expires或者Cache-Control和Expires过期抑或它的属性设置为no-cache时，那么浏览器第⼆次请求时就会与服务器进⾏协商。

如果缓存和服务端资源的最新版本是⼀致的，那么就⽆需再次下载该资源，服务端直接返回304 Not Modified 状态码， 如果服务器发现浏览器中的缓存已经是旧版本了，那么服务器就会把最新资源的完整内容返回给浏览器，状态码就是 200 Ok。

服务器判断缓存是否是新鲜的⽅法就是依靠HTTP的另外两组信息

## Last-Modified/If-Modified-Since

客户端⾸次请求资源时，服务器会把资源的最新修改时间 `Last-Modified:Thu, 19 Feb 2019 08:20:55 GMT` 通过响应部⾸发 送给客户端，当再次发送请求是，客户端将服务器返回的修改时间放在请求头 `If-Modified-Since:Thu, 19 Feb 2019 08:20:55 GMT` 发送给服务器，服务器再跟服务器上的对应资源进⾏⽐对，如果服务器的资源更新，那么返回最新的资源，此时状态码200，当服务器资源跟客户端的请求的部⾸时间⼀致，证明客户端的资源是最新的，返回304状态码， 表示客户端直接⽤缓存即可。

## ETag/If-None-Match

ETag的流程跟Last-Modified是类似的，区别就在于ETag是根据资源内容进⾏hash，⽣成⼀个信息摘要，只要资源内容 有变化，这个摘要就会发⽣巨变，通过这个摘要信息⽐对，即可确定客户端的缓存资源是否为最新，这⽐Last-Modified 的精确度要更⾼。
Last-Modified，服务端只要打开文件，即使不改变内容 Last-Modified 也会改变。