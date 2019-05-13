

###　一. HTTP基础 (Client-Server协议)

#### 1. 会话分为三个阶段：

1. 客户端建立一条 TCP 连接（如果传输层不是 TCP，也可以是其他适合的连接）。
2. 客户端发送请求并等待应答。
3. 服务器处理请求并送回应答，回应包括一个状态码和对应的数据。

> 从 HTTP/1.1 开始，连接在完成第三阶段后不再关闭，客户端可以再次发起新的请求。这意味着第二步和第三步可以连续进行数次。
> 使用 TCP 时，HTTP 服务器的默认端口号是 80，页面的 URL 会包含域名和端口号，但当端口号为 80 时可以省略.



#### 2. 请求结构

> 客户端请求由一系列文本指令组成，并使用 CRLF 分隔，它们被划分为三个块：

1. 第一行包括请求方法及请求参数：
    * 文档路径，不包括协议和域名的绝对路径 URL
    * 使用的 HTTP 协议版本
2. 接下来的行每一行都表示一个 HTTP 首部，为服务器提供关于所需数据的信息（例如语言，或 MIME 类型），
   或是一些改变请求行为的数据（例如当数据已经被缓存，就不再应答）。这些 HTTP 首部组成以一个空行结束 的一个块。
3. 最后一块是可选数据块，包含更多数据，主要被 POST 方法所使用。

``` 
    POST /contact_form.php HTTP/1.1
    Host: developer.mozilla.org
    Content-Length: 64
    Content-Type: application/x-www-form-urlencoded

    name=Joe%20User&request=Send%20me%20one%20of%20your%20catalogue

```


#### 3. 服务器响应结构
> 服务器响应由一系列文本指令组成, 并使用 CRLF 分隔，它们被划分为三个不同的块

1. 第一行是 状态行，包括使用的 HTTP 协议版本，状态码和一个状态描述（可读描述文本）
2. 接下来每一行都表示一个 HTTP 首部，为客户端提供关于所发送数据的一些信息（如类型，数据大小，使用的压缩算法，缓存指示）。与客户端请求的头部块类似，这些 HTTP 首部组成一个块，并以一个空行结束。
3. 最后一块是数据块，包含了响应的数据 （如果有的话）。

```
    HTTP/1.1 200 OK
    Date: Sat, 09 Oct 2010 14:28:02 GMT
    Server: Apache
    Last-Modified: Tue, 01 Dec 2009 20:18:22 GMT
    ETag: "51142bc1-7449-479b075b2891b"
    Accept-Ranges: bytes
    Content-Length: 29769
    Content-Type: text/html

    <!DOCTYPE html... (这里是 29769 字节的网页HTML源代码)

```

(mdn)[https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Session]


### 二. 