# REST CheatSheet

REST,即Representational State Transfer的缩写，表现层状态转化。

Resource（资源） ：对象的单个实例。 例如，一只动物。它可以是一段文本、一张图片、一首歌曲、一种服务，总之就是一个具体的实在。你可以用一个URI（统一资源定位符）指向它，每种资源对应一个特定的URI。要获取这个资源，访问它的URI就可以，因此URI就成了每一个资源的地址或独一无二的识别符。

"资源"是一种信息实体，它可以有多种外在表现形式。我们把"资源"具体呈现出来的形式，叫做它的"表现层"（Representation）。状态转化（State Transfer）
访问一个网站，就代表了客户端和服务器的一个互动过程。在这个过程中，势必涉及到数据和状态的变化。互联网通信协议HTTP协议，是一个无状态协议。这意味着，所有的状态都保存在服务器端。因此，如果客户端想要操作服务器，必须通过某种手段，让服务器端发生"状态转化"（State Transfer）。而这种转化是建立在表现层之上的，所以就是"表现层状态转化"。

客户端用到的手段，只能是HTTP协议。具体来说，就是HTTP协议里面，四个表示操作方式的动词：GET、POST、PUT、DELETE。它们分别对应四种基本操作：GET用来获取资源，POST用来新建资源（也可以用于更新资源），PUT用来更新资源，DELETE用来删除资源。
比如，文本可以用txt格式表现，也可以用HTML格式、XML格式、JSON格式表现，甚至可以采用二进制格式；图片可以用JPG格式表现，也可以用PNG格式表现。
URI只代表资源的实体，不代表它的形式。严格地说，有些网址最后的".html"后缀名是不必要的，因为这个后缀名表示格式，属于"表现层"范畴，而URI应该只代表"资源"的位置。它的具体表现形式，应该在HTTP请求的头信息中用Accept和Content-Type字段指定，这两个字段才是对"表现层"的描述。

# 接口规范

## Action | 动作

GET （SELECT）：从服务器检索特定资源，或资源列表。
POST （CREATE）：在服务器上创建一个新的资源。
PUT （UPDATE）：更新服务器上的资源，提供整个资源。
PATCH （UPDATE）：更新服务器上的资源，仅提供更改的属性。
DELETE （DELETE）：从服务器删除资源。
首先是四个半种动作：
post、delete、put/patch、get
因为put/patch只能算作一类，所以将patch归为半个。
另外还有有两个较少知名的HTTP动词：
HEAD - 检索有关资源的元数据，例如数据的哈希或上次更新时间。
OPTIONS - 检索关于客户端被允许对资源做什么的信息。

## 接口命名

在RESTful架构中，每个网址代表一种资源（resource），所以网址中不能有动词，只能有名词，而且所用的名词往往与数据库的表格名对应。一般来说，数据库中的表都是同种记录的"集合"（collection），所以API中的名词也应该使用复数。

举例来说，有一个API提供动物园（zoo）的信息，还包括各种动物和雇员的信息，则它的路径应该设计成下面这样。
接口尽量使用名词，禁止使用动词，下面是一些例子。

```
GET         /zoos：列出所有动物园
POST        /zoo：新建一个动物园
GET         /zoos/ID：获取某个指定动物园的信息
PUT         /zoos/ID：更新某个指定动物园的信息（提供该动物园的全部信息）
PATCH       /zoos/ID：更新某个指定动物园的信息（提供该动物园的部分信息）
DELETE      /zoos/ID：删除某个动物园
GET         /zoos/ID/animals：列出某个指定动物园的所有动物
DELETE      /zoos/ID/animals/ID：删除某个指定动物园的指定动物
```

对于版本控制，则应该将API的版本号放入URL。如：

```
https://api.example.com/v1/
```

另一种做法是，将版本号放在HTTP头信息中，但不如放入URL方便和直观。Github采用这种做法。

参考业界实现
/menu/create 微信自定义菜单创建接口
/menu/get      微信自定义菜单查询接口
/menu/delete 微信自定义菜单删除接口
comments/show 获取某条微博的评论列表
comments/create 评论一条微博
comments/destroy 删除一条我的评论
comments/destroy_batch 批量删除我的评论
1.HTTP动词有限，难以满足复杂业务需求，在URL中使用自定义动词可以更好的扩展
2.可以更好的从URL体现业务含义
3.REST规范需要URL+HTTP动词，对某个资源的查询和更新的URL是相同的，而一般URL统计、流控只会针对URL
4.统一基本的资源操作含义
create 创建
get      查询
delete  删除
update 更新
批量xxx batch_xxx

## Filter & Search | 过滤与检索

如果记录数量很多，服务器不可能都将它们返回给用户。API应该提供参数，过滤返回结果。
下面是一些常见的参数。

```
?limit=10：指定返回记录的数量
?offset=10：指定返回记录的开始位置。
?page_number=2&page_size=100：指定第几页，以及每页的记录数。
?sortby=name&order=asc：指定返回结果按照哪个属性排序，以及排序顺序。
?animal_type_id=1：指定筛选条件
参数的设计允许存在冗余，即允许API路径和URL参数偶尔有重复。比如，
GET /zoo/ID/animals 与 GET /animals?zoo_id=ID 的含义是相同的。
```

## Status Code | 状态码

状态码范围
```
1xx 信息，请求收到，继续处理。范围保留用于底层HTTP的东西，你很可能永远也用不到。
2xx 成功，行为被成功地接受、理解和采纳
3xx 重定向，为了完成请求，必须进一步执行的动作
4xx 客户端错误，请求包含语法错误或者请求无法实现。范围保留用于响应客户端做出的错误，例如。他们提供不良数据或要求不存在的东西。这些请求应该是幂等的，而不是更改服务器的状态。
5xx 范围的状态码是保留给服务器端错误用的。这些错误常常是从底层的函数抛出来的，甚至
```
开发人员也通常没法处理，发送这类状态码的目的以确保客户端获得某种响应。
当收到5xx响应时，客户端不可能知道服务器的状态，所以这类状态码是要尽可能的避免。
服务器向用户返回的状态码和提示信息，常见的有以下一些（方括号中是该状态码对应的HTTP动词）。
```
200 OK - [GET]：服务器成功返回用户请求的数据，该操作是幂等的（Idempotent）。
201 CREATED - [POST/PUT/PATCH]：用户新建或修改数据成功。
202 Accepted - [*]：表示一个请求已经进入后台排队（异步任务）
204 NO CONTENT - [DELETE]：用户删除数据成功。
400 INVALID REQUEST - [POST/PUT/PATCH]：用户发出的请求有错误，服务器没有进行新建或修改数据的操作，该操作是幂等的。
401 Unauthorized - [*]：表示用户没有权限（令牌、用户名、密码错误）。
403 Forbidden - [*] 表示用户得到授权（与401错误相对），但是访问是被禁止的。
404 NOT FOUND - [*]：用户发出的请求针对的是不存在的记录，服务器没有进行操作，该操作是幂等的。
406 Not Acceptable - [GET]：用户请求的格式不可得（比如用户请求JSON格式，但是只有XML格式）。
410 Gone -[GET]：用户请求的资源被永久删除，且不会再得到的。
422 Unprocesable entity - [POST/PUT/PATCH] 当创建一个对象时，发生一个验证错误。
500 INTERNAL SERVER ERROR - [*]：服务器发生错误，用户将无法判断发出的请求是否成功。
502 网关错误
503 Service Unavailable
504 网关超时
```
