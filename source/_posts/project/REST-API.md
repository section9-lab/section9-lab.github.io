---
title: REST-API
date: 2022-01-12
category:
  - Project
tag:
  - java
  - api
star: false
sticky: false
---

# REST-API



## 1 API设计指南
- API 返回的数据格式,不应该是纯文本而是一个 JSON 对象
- 发生错误时，不要返回 200 状态码
- 业务异常http状态码需要定义为 402

```txt
GET：读取（Read）
POST：新建（Create）
PUT：更新（Update）
PATCH：更新（Update），通常是部分更新
DELETE：删除（Delete）
```

状态码：
```txt
1xx：相关信息
2xx：操作成功
3xx：重定向
4xx：客户端错误
5xx：服务器错误
```
## 2 状态码4XX
> 4xx状态码表示客户端错误，主要有下面几种。
>
> 400 Bad Request：服务器不理解客户端的请求，未做任何处理。
>
> 401 Unauthorized：用户未提供身份验证凭据，或者没有通过身份验证。
>
> 403 Forbidden：用户通过了身份验证，但是不具有访问资源所需的权限。
>
> 404 Not Found：所请求的资源不存在，或不可用。
>
> 405 Method Not Allowed：用户已经通过身份验证，但是所用的 HTTP 方法不在他的权限之内。
>
> 410 Gone：所请求的资源已从这个地址转移，不再可用。
>
> 415 Unsupported Media Type：客户端要求的返回格式不支持。比如，API 只能返回 JSON 格式，但是客户端要求返回 XML 格式。
>
> 422 Unprocessable Entity ：客户端上传的附件无法处理，导致请求失败。
>
> 429 Too Many Requests：客户端的请求次数超过限额。
>
## 3 状态码5XX

> 500 Internal Server Error：客户端请求有效，服务器处理时发生了意外。
>
> 503 Service Unavailable：服务器无法处理请求，一般用于网站维护状态。
>
> 504  服务器作为网关或代理，但是没有及时从上游服务器收到请求。




参考：
- https://www.ruanyifeng.com/blog/2018/10/restful-api-best-practices.html
- https://cloud.google.com/apis/design?hl=zh-cn
- https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design
