---
title: session和jwt鉴权
date: 2020-03-24 11:16:54
tags: javascript
typora-root-url: ..
---

### 前言

由于http是一种无状态协议，即http协议对于交互场景没有记忆能力，因此需要一种方式能让服务器知道是谁在访问，于是传统的cookie-session鉴权和JWT鉴权等方式应运而生。

<!-- more -->

### 传统的cookie-session鉴权

session 是一种服务器机制，是存储在服务器上的信息。存储方式多种多样，可以是服务器内存中，或者是mongo数据库，redis内存数据库中。而session是基于cookie实现的，服务器会生成sessionID，通过set-cookie的方式写入到客户端的cookie中。客户端每次请求都会自动携带服务器写入sessionID的cookie发送给服务端，通过解析cookie中的sessionID与服务端保存的session来判断用户是否登录。

#### 缺点

session通常放在内存中，用户数量如果过大会对服务器产生压力

扩展性。 哪怕session以文件形式保存，放在redis中。对于分布式系统来说会产生高流量的数据读取（文件同步读取问题）

### JWT鉴权

#### 什么是JWT（Json web token）?

Json web token (JWT), 是为了在网络应用环境间传递声明而执行的一种基于JSON的开放标准（(RFC 7519).该token被设计为紧凑且安全的，特别适用于分布式站点的单点登录（SSO）场景。JWT的声明一般被用来在身份提供者和服务提供者间传递被认证的用户身份信息，以便于从资源服务器获取资源，也可以增加一些额外的其它业务逻辑所必须的声明信息，该token也可直接被用于认证，也可被加密。

#### JWT鉴权的流程

![jwt流程](/images/session和jwt鉴权/jwt流程.png)

1. 客户端通过post请求请求服务端登录认证接口
2. 服务端用秘密创建JWT
3. 服务端将JWT返回浏览器
4. 客户端在授权报头上发送JWT
5. 服务端检查JWT签名从JWT获取用户信息
6. 服务端向客户端发送响应

#### 优点

服务器不需要存储session，节省内存开支

结构简单，占用资源少

JSON格式，可以兼容多种语言

#### JWT结构分析

![jwt结构](/images/session和jwt鉴权/jwt结构.webp)

JWT由三个部分构成，用英文句点分割(.) 。分为

1. **Header**(头信息)。通常包含两部分，type：代表token的类型，这里使用的是JWT类型。 alg:代表使用的算法，例如HMAC SHA256或RSA.

```json
// 这会被经过base64Url编码形成第一部分
{
	"alg": "HS256",
	"typ": "JWT"
}
```

2. **Payload**(载荷信息，实际数据)。一个token的第二部分是荷载信息，它包含一些声明Claim（实体的描述，例：用户信息和其他的一些元数据）

   声明分为三类： **标准字段**（一套预定义的声明，并不是必须的,这是一套易于使用、操作性强的声明），**公共字段**，**私有字段**

   标准字段：

   ​      **iss**: JWT签发者

   ​      **sub**: JWT所面向的用户

   ​      **aud**: 接收JWT的一方

   ​      **exp**: JWT的过期时间，这个过期时间必须要大于签发时间

   ​      **nbf**: 定义在什么时间之前，该JWT都是不可用的.

   ​      **iat**: JWT的签发时间

   ​      **jti**: JWT的唯一身份标识，主要用来作为一次性token,从而回避重放攻击。

```json
//同样经过Base64Url编码后形成第二部分
{
	"sub": "1234567890",
	"name": "John Doe",
	"iat": 1516239022
}
```

3. **Signature**(由头信息+荷载信息+密钥 组合之后进行加密得到) 。使用header中指定的算法将编码后的header、编码后的payload、一个secret进行加密

   例如使用的是HMAC SHA256算法，大致流程类似于: 

   ```js
   HMACSHA256( base64UrlEncode(header) + "." + base64UrlEncode(payload), secret)
   ```

   这个signature字段被用来确认JWT信息的发送者是谁，并保证信息没有被修改

#### JWT校验真伪

1. 服务器收到JWT
2. 将header和payload 用秘钥和对应的算法生成签名
3. 判断生成的签名和jwt的第三部分是否一致
4. 一致则代表可信，不一致则返回错误