# RESTful API规范 #

RESTful API已经非常成熟，也得到了大家的认可。规范按照[Richardson Maturity Mode](https://martinfowler.com/articles/richardsonMaturityModel.html)对REST评价的模型，规范基于level2来设计。

## 资源 ##

### 路径 ###

路径，API的具体地址。在REST中，每个地址都代表一个具体的资源（Resource）。所以就有了以下的约定：

- 路径仅表示资源的路径（位置），以及一些特殊的actions操作。
- 以复数（名词）进行命名资源，不管返回单个或者多个资源。
- 使用小写字母、数字以及下划线（“_”）。（下划线是为了区分多个单词，如token_access）。
- 资源的路径从父到子依次如：/{resource}/{resource_id}/{sub_resource}/{sub_resource_id}/{sub_resource_property}。
- 使用?来进行资源的过滤、搜索以及分页等。
- 使用版本号，且版本号在资源路径之前。
- 优先使用内容协商来区分表述格式，而不是使用后缀来区分表述格式。
- 应该放在一个专用的域名下，如：http：//api.webfuse.cn。
- 建议使用SSL。

综上，一个API路径可能会是

    https://api.webfuse.cn/v0.1/{resource}/{resource_id}/{sub_resource}/{sub_resource_id}/{sub_resource_property}
    https://api.webfuse.cn/v0.1/{resource}?limit=10&offset=10
    https://api.webfuse.cn/v0.1/{resource}?page=1&page_size=10
    https://api.webfuse.cn/v0.1/{resource}?sortby=name&order=asc

### 操作 ###

用HTTP动词（方法）表示对资源的具体操作。常用的HTTP动词有：

    GET：从服务器取出资源（一项或多项）。
    POST：在服务器新建一个资源。
    PUT：在服务器更新资源（客户端提供改变后的完整资源，全部更新）。
    PATCH：在服务器更新资源（客户端提供改变的属性，部分更新）。
    DELETE：从服务器删除资源。
    HEAD：获取资源的元数据。
    OPTIONS：获取信息，关于资源的哪些属性是客户端可以改变的。
所以，

    GET    https://api.webfuse.cn/v0.1/users    获得用户列表
    GET    https://api.webfuse.cn/v0.1/users/{id}    获得指定的用户
    POST   https://api.webfuse.cn/v0.1/users    创建一个用户
    PUT    https://api.webfuse.cn/v0.1/users/{id}    更新指定的用户（提供该用户的全部信息）
    PATCH  https://api.webfuse.cn/v0.1/users/{id}    更新指定的用户（提供该用户的部分信息）
    DELETE https://api.webfuse.cn/v0.1/users/{id}    删除指定的用户

## 数据 ##

数据是对资源的具体描述，分为请求数据和返回数据。约定如下：

- 查询，过滤条件使用query string。
- Content body 仅仅用来传输数据。
- 通过Content-Type指定请求与返回的数据格式。其中请求数据还要指定Accept。
- 数据应该拿来就能用，不应该还要进行转换操作。
- 使用ISO-8601格式表达时间字段，例如: 2014-04-05T14:30Z。
- 采用UTF-8编码。
- 返回的数据应该尽量简单，响应状态应该包含在响应头中。
- 使用小写字母、数字以及下划线（“_”）描述字段，不使用大写描述字段。
- 建议资源使用UUID最为唯一标识。同时建议命名为id。
- 属性和字符串值必须使用双引号””。
- 建议对每个字段设置默认值（数组型可设置为[],字符串型可设置为””，数值可设置为0，对象可设置为{}）,这一条是为了方便前端/客户端进行判断字段存不存在操作。
- POST操作应该返回新建的资源；PUT/PATCH操作返回更新后的完整的资源；DELETE返回一个空文档；GET返回资源数组或当个资源。
- 为了方便以后的扩展兼容，如果返回的是数组，强烈建议用一个包含如items属性的对象进行包裹。如：{"items":[{},{}]}。

所以，一个请求以及返回的数据结构可能如：

    POST https://api.webfuse.cn/v0.1/users
    Request
        headers:
            Accept: application/json
            Content-Type: application/json;charset=UTF-8
        body:
            {
                "user_name": "HingKwan",
                "id":"550e8400-e29b-41d4-a716-446655440000"
            }

    Response
        status: 201 Created
        headers:
            Content-Type: application/json;charset=UTF-8
        body:
            {
                "user_name": "HingKwan",
                "id":"550e8400-e29b-41d4-a716-446655440000"
            }

## 安全 ##

### 调用限制 ###

为了避免请求泛滥，给API设置速度限制很重要。入速度设置之后，可以在HTTP返回头上对返回的信息进行说明，下面是几个必须的返回头（依照twitter的命名规则）：

    X-Rate-Limit-Limit :当前时间段允许的并发请求数
    X-Rate-Limit-Remaining:当前时间段保留的请求数。
    X-Rate-Limit-Reset:当前时间段剩余秒数

### 授权校验 ###

RESTful API是无状态的也就是说用户请求的鉴权和cookie以及session无关，每一次请求都应该包含鉴权证明。

可以使用http请求头Authorization设置授权码; 必须使用User-Agent设置客户端信息, 无User-Agent请求头的请求应该被拒绝访问。具体的授权可以采用OAuth2，或者自己定义并实现相关的授权验证机制（基于token）。

常见的请求Header为：

    User-Agent: Data-Server-Client
    Authorzation: Bearer 383w9JKJLJFw4ewpie2wefmjdlJLDJF
或

    User-Agent: Data-Server-Client
    Authorzation: MAC id="2YotenFZFEjrizCsicMWpBA",nonce="14187532423423:adfadsfa",mac="SfadfaFdafadfdasFFyu"
以上两个代码只是列出来可以采用的格式，具体的实现可以根据各项目具体规划。

## 错误 ##

当API返回非2XX的HTTP响应时，应该采用统一的响应信息，格式如：

    HTTP/1.1 400 Bad Request
    Content-Type: application/json;charset=UTF-8
    {
        "code":"INVALID_ARGUMENT",
        "message":"{error message}",
        "request_id":"",
        "host_id":"{server identity}",
        "server_time":"2016-05-17T22:08:00Z"
        "document":"https://developer.webfuse.cn/v0.1/errors/400"
    }

- HTTP Header Code：符合HTTP响应的状态码。详细见以下的“状态码”节。
- code：用来表示某类错误，比如缺少参数等。是对HTTP Header Code的补充，开发团队可以根据自己的需要自己定义。
- message：错误信息的摘要，应该是对用户处理错误有用的信息。
- request_id：请求的id，方便开发定位发生错误的请求（可选）。
- host_id：服务器实例的ID，方便开发定位是哪台服务器实例发生了错误（可选）。
- server_time：发生错误时候的服务器时间（可选）。
- document：错误解决的文档（方便前端展示，可选）。

code的定义约定：

- 采用大写字母命名，字母与字母之间用下划线（”_”）隔开。
- 采用{biz_name}/{err_code}的命名结构。其中biz_name为业务名称的缩写。
- code应该用来定义错误类别，而非定义具体的某个错误。

状态码

为每个请求返回适当的状态码，状态码可以参考HTTP的状态码。

常用的状态码有：

    200 ok  - 成功返回状态，对应，GET,PUT,PATCH,DELETE。
    201 created  - 成功创建。
    304 not modified   - HTTP缓存有效。
    400 bad request   - 请求格式错误。
    401 unauthorized   - 未授权。
    403 forbidden   - 鉴权成功，但是该用户没有权限。
    404 not found - 请求的资源不存在
    405 method not allowed - 该http方法不被允许。
    410 gone - 这个url对应的资源现在不可用。
    415 unsupported media type - 请求类型错误。
    422 unprocessable entity - 校验错误时用。
    429 too many request - 请求过多。
    500 internal server error - 通用错误响应。
    503 service unavailable - 服务当前无法处理请求。

---
参考：

1. http://restful-api-design.readthedocs.io/en/latest/
1. http://arccode.net/2015/02/26/RESTful%E6%9C%80%E4%BD%B3%E5%AE%9E%E8%B7%B5/