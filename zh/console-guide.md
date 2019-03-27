## Content Delivery > CDN > 控制台使用指南

下面介绍在控制台中配置和使用CDN服务的方法。

## CDN服务创建顺序

**在Contents Delivery > CDN**的**CDN服务**标签中单击**创建**按钮，打开**创建CDN服务**窗口。
自动创建要在CDN服务中使用的域名。若已拥有域名，可使用域别名（Domain Alias）功能服务。

### CDN服务

![cdn_01_201812](https://static.toastoven.net/prod_cdn/cdn_01_201812.png)

- **服务地区**
  选择要服务的地区。
  根据服务提供形式，可选择KOREA或GLOBAL。若欲利用各国的缓存服务器以全球为对象提供服务，请选择GLOBAL，若仅在韩国国内提供服务，则选择KOREA。如果选择KOREA，可以以更低的价格使用服务。

- **说明**
  为CDN服务输入补充说明。

- **原始服务器**
  可利用在TOAST中创建的实例或原来拥有的服务器提供服务。原始服务器可以以IP或域的形式输入，且可指定端口。
- **原始路径**
  请在原始路径中输入包含“/”的URL路径形式的下级路径。**原始路径**为可选项目。

- **域别名**
  设置域别名。
  CDN服务创建完成后，自动发布*.cdn.toastcloud.com形式的服务域，发布的域可直接用于服务。
  但需要利用个人或公司所拥有的域提供CDN服务时，可使用**域别名**设置。
  如果使用**域别名**，除了TOAST提供的域以外，还可以利用个人或公司所拥有的域来使用CDN服务。
  请在**域别名**中输入拥有的域地址，更改相应域的名称服务器设置。
  如果将从TOAST接收的CDN服务地址添加到CNAME记录，则也可以利用拥有的域来使用服务。

> 【注意】
> CDN服务创建后，发布`*.cdn.toastcloud.com`服务域。
> 通过`*.cdn.toastcloud.com`服务域无法访问资源时，请确认以下内容。
> 
> 
> 1.请确认CDN服务状态是否为“绿色”（服务中）。
> 2.请确认原始服务器（origin server）是否仅允许特定主机的访问。
> 3.若用户从CDN服务域请求资源，`Host`请求标头设置为用户请求的地址`*.cdn.toastcloud.com`，向原始服务器发出请求。
> 因此，原始服务器不允许访问`*.cdn.toastcloud.com`主机时，CDN缓存服务器无法从原始服务器获取资源。
> 此种情况下，应在原始服务器的Virtual host设置中注册`*.cdn.toastcloud.com`主机。

* 域别名使用示例

该设置方法是，创建CDN服务后，发布random-exam.cdn.toastcloud.com域，利用原有客户拥有的alias.nhnentcustomer.com来提供服务。

    1.确认创建TOAST CDN后，确认自动发布的random-exam.cdn.toastcloud.com。
    2.在默认信息设置标签的Domain Alias项目中，输入服务中要使用的客户域alias.nhnentcustomer.com。
    3.在Nhnentcustomer.com名称服务器管理项目中，利用random-exam.cdn.toastcloud.com添加CNAME记录。（根据不同的域提供商，安装方法可能有所不同。详细事项请咨询域提供商。）
    4.利用alias.nhnentcustomer.com开始服务。

### 缓存

可在缓存下设置缓存到期。
![cdn_02_201812](https://static.toastoven.net/prod_cdn/cdn_02_201812.png)

- **缓存到期设置**
可指定所需的缓存到期时间。**使用原始设置**选项为默认值。
  
  * 使用原始设置：设置为使用原始服务器的缓存到期设置。
  * 使用用户设置：可设置所需的缓存到期时间。

- **缓存到期时间（秒）**
若欲指定缓存到期时间，单击**使用用户设置**按钮，在 **缓存到期时间（秒）**中更改缓存到期时间。

> 【注意】
> 如果原始服务器设置指定了缓存到期时间，那么控制台中设置的**缓存到期时间（秒）**值将被忽略。
> 若欲使用CDN服务指定到期时间，删除原始服务的缓存到期设置。
>
> 【参考】
> 缓存到期时间默认值为0。若默认值设置为0，缓存到期时间为604,800（单位/秒）。
> 缓存到期时间可以输入从默认值0至2,147,483,647（单位/秒）之间的值。

- **引用(referrer)标头访问管理**
  设置引用访问管理。
  从网页链接用户内容请求资源时，向引用标头传达链接的URL路径，可以确认请求来自哪个路径。
  引用访问管理可参考引用标头，设置为仅特定引用才可访问用户内容。
  引用可以正规表达式的形式输入，控制多个引用时，在输入窗口中添加行后输入。

  * Blacklist（黑名单）类型：
      * 适合用于限制特定引用的访问。
      * 若请求标头的引用值是与设置的正规表达式匹配的字符串，则限制访问内容。若是不匹配的字符串，则允许访问内容。
  * Whitelist（白名单）类型：
      * 适合用于允许特定引用的访问。
      * 若请求标头的引用值是与正规表达式匹配的字符串，则允许访问内容。若是不匹配的字符串，则限制访问内容。

> 【注意】
> 若请求标头中无引用，则引用设置的访问控制不起作用。
>
> 【示例】
> * 类型：Whitelist
> * 正规表达式：`^https://[a-zA-Z0-9._-]*\.toast\.com/.*`
> 仅在从任意toast.com子域的下级路径中请求资源时，允许访问内容。
>
> 【参考】
> 部分字符在正规表达式中作为特殊字符使用。 
> 以点(`.`)为例，正规表达式中，点(`.`)是显示与所有字符一致的特殊字符。 
> 如果不是特殊字符的意义，而是需要按照一般字符解释，则请在特殊字符前添加反斜杠(`\`)。（例：`\.`)
> 正规表达式的特殊字符有`^.[ ] $ ( ) | * + ?{ } \`等。
> 控制多个引用时，在下一行中连续输入。
> 通过API设置多个引用时，请以\n令牌区分输入。


### 回调

CDN服务更改操作（创建、修改、停用/重启、删除操作）在请求更改后最长可能需要数十分钟。 
服务更改操作完成后，可利用提前设置的回调URL接收服务更改操作是否完成及服务信息。

![cdn_03_201812](https://static.toastoven.net/prod_cdn/cdn_03_201812.png)


1.输入**HTTP Method**与**回调URL**。
2.若欲利用Request URI的Query Parameter接收CDN服务更改操作的结果，请在**回调URL**中加入Path变量。
| Path Variable | 说明 | 示例传达值 |
| ------------- | --- | ------- |
| {appKey} | CDN服务接口密钥 | 从控制台接收的接口密钥 |
| {domain} | CDN服务名 | xxxxxx.cdn.toastcloud.com |
| {status} | 当前CDN服务状态 | OPEN, SUSPEND, CLOSE, ERROR |
| {isSuccessful} | 服务更改操作是否成功（API V1.0不支持。）| “true”或"false" |
3.单击**确认**按钮，则完成创建CDN服务请求。

> 示例
> GET http://test.callback.com?appKey={appKey}&domain={domain}&status={status}&deploySuccess={isSuccessful}
- 传达回调时，CDN服务的信息将传至请求正文（Request Body）。
- 更改为API V1.0时，请求正文内容如下。
```
    {
      "seq": Integer,
      "appKey": String,
      "domain": String,
      "domainAlias": String,
      "type": String,
      "region": String,
      "description": String,
      "status": String, 
      "createTime": DateTime,
      "useOrigin": String,
      "maxAge": String,
      "referrerType": String,
      "referrers": String,
      "deleteTime": DateTime,
      "company": String,
      "origins":[
      {
         "seq": Integer,
         "distributionSeq": Integer,
         "origin": String,
         "originPath": String,
         "port":Integer,
      }
      ],
      "callbackHttpMethod": String,
      "callbackUrl": String
}
```
* 在CDN控制台中更改或更改为API V1.5时，响应格式如下。
```
    {
      "header":{
      "resultCode": Integer,
      "resultMessage": String,
      "isSuccessful": Boolean
      },
      "distribution":{
      "seq": String,
      "appKey": String,
      "domain": String,
      "domainAlias": String,
      "type": String,
      "region": String,
      "description": String,
      "status": String,
      "createTime": DateTime,
      "useOrigin": String,
      "maxAge": String, 
      "referrerType": String,
      "referrers": String,
      "deleteTime": DateTime,
      "company": String,
      "origins":[
         {
            "seq": Integer,
            "distributionSeq": Integer,
            "origin": String,
            "originPath": String,
            "port": Integer
         }
      ],
      "callback":{
         "httpMethod": String,
         "url": String
      }
      },
      "successful": Boolean
}
```
> 【注意】
> 回调行为因API V1.0与 V1.5版本不同而有所不同，请注意。
> API V1.0仅在创建及修改CDN服务时调用回调，而API V1.5在创建、修改、暂停和重启、删除时调用回调。
> 调用请求正文（Request Body）的JSON数据格式因API版本不同而有所不同，请注意。


**请求创建后至服务分发完成，约需要几分钟（最长一小时）。分发完成后，可使用服务。**

## 设置

### 更改CDN服务设置

可更改补充说明及原始服务器信息。
但服务名和地区无法更改，因此若欲更改，应删除原有服务后，创建新的服务。

1.在CDN服务列表中选择想要更改的服务。
2.单击界面下端**基本信息**标签的**修改**按钮。

如下可更改的项目被激活。

![cdn_04_201812](https://static.toastoven.net/prod_cdn/cdn_04_201812.png)

* 可更改的项目是说明、原始服务器信息、域别名、回调。
* 单击**确认**按钮，完成更改。

**原始服务器更改后，再次分发原本缓存的所有内容，根据内容量，再次分发时间有所不同。**

### 更改CDN缓存设置

1.在CDN服务列表中选择想要更改的服务。
2.单击**缓存设置**标签的**修改**按钮。

如下可更改的项目被激活。

![cdn_05_201812](https://static.toastoven.net/prod_cdn/cdn_05_201812.png)

* 可更改的项目是缓存到期设置、缓存到期时间、引用标头访问管理、调回。
* 单击**确认**按钮，完成更改。

### 再次分发CDN缓存设置

若原始内容的内容有所更改，已指定的缓存到期时间之后，缓存更新为新的内容。但若欲快速再次分发缓存内容，使用**再次分发缓存**功能，将原缓存更新为新的内容。

1.在CDN服务列表中选择想要更改的服务。
2.单击**再次分发缓存** 标签。
![cdn_06_201812](https://static.toastoven.net/prod_cdn/cdn_06_201812.png)
3.选择再次分发缓存类型。可以在3种类型中选择。
  * 特定文件：设置正确的文件名和路径，仅可分发所需文件。
      * 例）/path/to/file1.jpg
  * 通配符：文件名及路径名中可使用通配符。
      * \*：任意字符列
      * ?：1个字符
      * \：Escape字符
          * 例）/images/games/\*.jpg
          * /\_/sports/\_.jpg
          * /images/sports/ac?e/\*.jpg
  * 所有文件：一次性再次分发所有缓存。
4.按照所选再次分发缓存类型，指定要再次分发的文件。
5.单击**再次分发缓存**按钮，请求再次分发。

再次分发需要约几分钟。根据容量，需要的时间可能有所不同。

> 【注意]】再次分发缓存使用量限制
> 各服务再次分发缓存的使用次数有所限制，因此若超过使用量，可在初始化使用量后再次使用。
> 
> 
> * 特定文件类型：限制每小时60次，一次可请求的最多Path数为1000个
> * 通配符类型：限制每小时60次，一次可请求的最多Path数为10个
> * 所有文件类型：每小时限制5次

### CDN监控设置

为了预防产生意外的流量，可注册监控设置，。若产生高于指定值的流量，则会发送邮件，若设置强制停止选项，则在发送邮件后停止CDN服务。

1.在CDN服务列表中选择想要更改的服务。
2.单击**监控设置**标签的**修改**按钮。
![cdn_07_201812](https://static.toastoven.net/prod_cdn/cdn_07_201812.png)
  * 指定需要累计流量的形式限制的流量。单位为字节。
  * 单击+/-按钮，添加或删除多个监控设置。
  * 若希望在检测到高于指定值的流量时强制停止服务，利用**是**激活强制停止设置。
3.单击**确定**按钮，应用更改的内容。

## 统计

可确认网络传输量、各HTTP状态代码统计及下载次数最多的内容的排名统计。
1.单击**Contents Delivery > CDN**的**统计**标签。
![cdn_08_201812](https://static.toastoven.net/prod_cdn/cdn_08_201812.png)
2.若欲确认统计，选择CDN服务。
3.输入搜索时间段。
4.搜索时间段内的数据周期根据所选时间段自动选择。
5.单击**搜索**按钮。