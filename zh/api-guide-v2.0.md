## Content Delivery > CDN > API指南

介绍TOAST CDN提供的Public API。

## API通用信息

### 域

| 名称              | 域                                   |
| --------------- | ------------------------------------- |
| CDN Public API域 | https://api-gw.cloud.toast.com/tc-cdn |

### 事前准备

若欲使用API，需要接口密钥（Appkey）和安全密钥（SecretKey）。 
接口密钥和安全密钥可在控制台右上方的**URL & Appkey**菜单中确认。

### 请求通用信息

#### 请求标头

| 名称            | 说明                        |
| ------------- | ------------------------- |
| Authorization | 从控制台接收的安全密钥（SecretKey） |

#### Path参数

所有API应将appKey指定为path参数。
* 例）/v1.5/appKeys/**{appKey}**/distributions

| 名称     | 说明                    |
| ------ | --------------------- |
| appKey | 从控制台接收的接口密钥(Appkey) |

### 响应通用信息

#### 标头

对于所有API请求，以**200 OK**响应。详细响应结果如下例所示，参考响应正文的标头。

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Success"
    }
}
```


【字段】

| 字段                   | 类型      | 说明     |
| -------------------- | ------- | ------ |
| header               | Object  | 标头区域  |
| header.isSuccessful  | Boolean | 是否成功  |
| header.resultCode    | Integer | 结果代码  |
| header.resultMessage | String  | 结果信息 |

#### CDN状态代码

如下为显示CDN服务状态的状态代码，查询服务时可确认服务状态。

| 值         | 说明                     |
| ---------- | ------------------------ |
| OPENING    | 服务启动中           |
| OPEN       | 服务中                |
| MODIFYING  | 修改中                  |
| RESUME     | 开始                     |
| SUSPENDING | 正在停止            |
| SUSPEND    | 停止                     |
| CLOSING    | 正在结束使用             |
| CLOSE      | 使用结束                |
| ERROR      | 创建服务时发生错误 |


## 服务API

### 创建服务

#### 请求


[URI]

| 方法  | URI                                  |
| ---- | ------------------------------------ |
| POST | /v1.5/appKeys/{appKey}/distributions |


【请求正文】

```json
{
   "distributions":[
       {
			"region": "LOCAL",
			"useOrigin" : "N",
			"referrerType" : "BLACKLIST",
			"description" : "sample-cdn",
			"maxAge": 86400,
			"referrers" : "cloud.toast.com",
			"origins" : [
				{
					"origin" : "static.origin.com",
					"port" : 80,
					"originPath" : "/resources"
				}
			],
            "callback": {
                "httpMethod": "GET",
                "url": "http://test.callback.com/cdn?=appKey={appKey}&status={status}&domain={domain}"
            }
		}
	]
}
```

【字段】

| 名称                                   | 类型    | 是否必需 | 默认值 | 有效范围                   | 说明                                                         |
| -------------------------------------- | ------- | --------- | ------ | --------------------------- | ------------------------------------------------------------ |
| distributions                          | List    | 必需      |        |                             | 要创建的CDN对象列表                                   |
| distributions[0].region                | String  | 必需      |        | LOCAL/GLOBAL                | 服务地区（”LOCAL”：韩国，”GLOBAL”：全球）
| distributions[0].useOrigin             | String  | 必需      |        | Y/N                         | 缓存到期设置（”Y”：使用原始设置，”N”：使用用户设置） |
| distributions[0].referrerType          | String  | 必需      |        | BLACKLIST/WHITELIST         | 引用访问管理（”BLACKLIST”：黑名单，”WHITELIST”：白名单） |
| distributions[0].description           | String  | 可选      |        | 最大255个字符                  | 说明                                                         |
| distributions[0].domainAlias           | String  | 可选      |        | 最大255个字符                  | 域别名（使用个人或公司所有的域，输入多个时请以\n令牌区分输入。）|
| distributions[0].maxAge                | Integer | 可选      | 0      | 0~2,147,483,647             | 缓存到期时间（秒），默认值0为604,800秒。             |
| distributions[0].referrers             | String  | 可选      |        | 包括’\n’令牌，最大1024个字符 | 引用（输入多个时请以\n令牌区分输入。）    |
| distributions[0].origins               | List    | 必需      |        |                             | 原始服务器对象列表                                      |
| distributions[0].origins[0].origin     | String  | 必需      |        | 最大255个字符                  | 原始服务器（domain或IP）                                     |
| distributions[0].origins[0].port       | String  | 必需      |        | 0~65,536                    | 原始服务器端口                                               |
| distributions[0].origins[0].originPath | String  | 可选      |        | 最大8192个字符                 | 原始服务器下级路径（请输入带有/的路径。）        |
| distributions[0].callback              | Object  | 可选      |        |                             | 接收CDN创建处理结果的回调URL（回调设置为可选输入。）|
| distributions[0].callback.httpMethod   | String  | 必需      |        | GET/POST/PUT                | 回调的HTTP Method                                           |
| distributions[0].callback.url          | String  | 必需      |        | 最大1024个字符                 | 回调URL                                                     |




#### 响应


【响应正文】

```json
{
    "header": {
        "resultCode": Integer,
        "resultMessage": String,
        "isSuccessful": Boolean
    },
    "distributions": [
        {
            "region" :  "LOCAL",
            "description" :  "api test pad",
            "useOrigin" :  "N",
            "domainAlias" :  "test.domain.com",
            "referrerType" :  "BLACKLIST",
            "referrers" :  "test.com",
            "maxAge" :  100,
            "origins" : [
                {
                    "origin" :  "static.toastoven.net",
                    "port" :  80
                }
            ],
            "callback": {
                "httpMethod": "GET",
                "url": "http://test.callback.com/cdn?=appKey={appKey}&status={status}&domain={domain}"
            }
        }
    ]
}
```


【字段】

| 字段                                   | 类型    | 说明                                                         |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | 标头区域                                                    |
| header.isSuccessful                    | Boolean | 是否成功                                                    |
| header.resultCode                      | Integer | 结果代码                                                    |
| header.resultMessage                   | String  | 结果信息                                                  |
| distributions                          | List    | 创建的CDN对象列表                                   |
| distributions[0].domain                | String  | 创建的域（服务）名                                   |
| distributions[0].domainAlias           | String  | 拥有的域                                                  |
| distributions[0].region                | String  | 服务地区（”LOCAL”：韩国，”GLOBAL”：全球）            |
| distributions[0].description           | String  | 说明                                                         |
| distributions[0].status                | String  | CDN状态代码（参考【表】 CDN状态代码）                                 |
| distributions[0].createTime            | String  | 创建日期                                                    |
| distributions[0].useOrigin             | String  | 是否使用原始服务器设置（”Y”：使用原始服务器设置，”N”：用户设置） |
| distributions[0].maxAge                | String  | 缓存到期时间（秒）                                           |
| distributions[0].referrerType          | String  | 引用访问管理（”BLACKLIST”：黑名单，”WHITELIST”：白名单） |
| distributions[0].referrers             | String  | 引用列表                                                  |
| distributions[0].origins               | List    | 原始服务器对象列表                                      |
| distributions[0].origins[0].origin     | String  | 原始服务器（domain或IP）                                      |
| distributions[0].origins[0].originPath | String  | 原始服务器下级路径                                          |
| distributions[0].origins[0].port       | Integer | 原始服务器端口                                               |
| distributions[0].callback              | Object  | 接收服务创建处理结果的回调                        |
| distributions[0].callback.httpMethod   | String  | 回调的HTTP Method                                           |
| distributions[0].callback.url          | String  | 回调URL                                                     |


### 查询服务

#### 请求


[URI]

| 方法  | URI                                  |
| ---- | ------------------------------------ |
| GET  | /v1.5/appKeys/{appKey}/distributions |


【参数】

| 名称   | 类型   | 是否必需 | 有效范围     | 说明                         |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| domain | String | 可选      | 最大255个字符    | 要查询的域（服务名）   |
| status | String | 选择      | CDN状态代码 | CDN状态代码（参考【表]】CDN状态代码） |

【例】
```
curl -X GET "https://api-gw.cloud.toast.com/tc-cdn/v1.5/appKeys/{appKey}/distributions?domain={domain}" \
 -H "Authorization: {secretKey}" \
 -H "Content-Type: application/json"
```

#### 响应


【响应正文】

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
      "isSuccessful" :  true
    },
    "domain" :  "lhcsxuo0.cdn.toastcloud.com",
    "domainAlias" :  "test.domain.com",
    "region" :  "LOCAL",
    "description" :  "api test pad",
    "status" :  "OPENING",
    "createTime" :  1498613094692,
    "useOrigin" :  "N",
    "maxAge" :  "100",
    "referrerType" :  "BLACKLIST",
    "referrers" :  "test.com",
    "origins" : [
        {
            "origin" :  "static.toastoven.net",
            "port" :  80
        }
    ],
    "callback": {
        "httpMethod": "GET",
        "url": "http://test.callback.com/cdn?=appKey={appKey}&status={status}&domain={domain}"
    }
}
```


【字段】

| 字段                                   | 类型    | 说明                                                         |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | 标头区域                                                    |
| header.isSuccessful                    | Boolean | 是否成功                                                    |
| header.resultCode                      | Integer | 结果代码                                                    |
| header.resultMessage                   | String  | 结果信息                                                  |
| distributions                          | List    | 创建的CDN对象列表                                     |
| distributions[0].domain                | String  | 域名（服务名）                                     |
| distributions[0].domainAlias           | String  | 拥有的域                                                  |
| distributions[0].region                | String  | 服务地区（”LOCAL”：韩国，”GLOBAL”：全球）             |
| distributions[0].description           | String  | 说明                                                         |
| distributions[0].status                | String  | CDN状态代码（参考【表】CDN状态代码）                                 |
| distributions[0].createTime            | String  | 创建日期                                                    |
| distributions[0].useOrigin             | String  | 是否使用原始服务器设置（”Y”：使用原始服务器设置，”N”：用户设置） |
| distributions[0].maxAge                | String  | 缓存到期时间（秒）                                           |
| distributions[0].referrerType          | String  | 引用访问管理（”BLACKLIST”：黑名单，”WHITELIST”：白名单） |
| distributions[0].referrers             | String  | 引用列表                                                  |
| distributions[0].origins               | List    | 原始服务器对象列表                                      |
| distributions[0].origins[0].origin     | String  | 原始服务器（domain或IP）                                      |
| distributions[0].origins[0].originPath | String  | 原始服务器下级路径                                          |
| distributions[0].origins[0].port       | Integer | 原始服务器端口                                               |
| distributions[0].callback              | Object  | 接收服务分发处理结果的回调                         |
| distributions[0].callback.httpMethod   | String  | 回调的HTTP Method                                           |
| distributions[0].callback.url          | String  | 回调URL                                                     |


### 修改服务

#### 请求


[URI]

| 方法  | URI                                  |
| ---- | ------------------------------------ |
| PUT  | /v1.5/appKeys/{appKey}/distributions |


【请求正文】

```json
{
        "domain" : "sample.cdn.toastcloud.com",
        "useOrigin" : "N",
        "maxAge": 86400,
        "referrerType" : "BLACKLIST",
        "referrers" : "test.com",
        "origins" : [
            {
                "origin" : "static.resource.com",
                "port" : 80,
                "originPath" : "/latest/resources"
            }
        ],
        "callback": {
            "httpMethod": "GET",
            "url": "http://test.callback.com/cdn?=appKey={appKey}&status={status}&domain={domain}"
        },
        "description" : "change contents"   
}
```


【字段】

| 名称                  | 类型    | 是否必需 | 默认值 | 有效范围                                                    | 说明                                                         |
| --------------------- | ------- | --------- | ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| domain                | String  | 必需      |        | 最大255个字符                                                   | 要修改的域（服务名）                                   |
| useOrigin             | String  | 必需      |        | Y/N                                                          | 缓存到期设置（Y：使用原始设置，”N”：使用用户设置） | 
| referrerType          | String  | 必需      |        | BLACKLIST/WHITELIST                                          | 引用访问管理（”BLACKLIST”：黑名单，”WHITELIST”：白名单） |
| description           | String  | 可选      |        | 最大255个字符                                                   | 说明                                                         |
| domainAlias           | String  | 可选      |        | 最大255个字符                                                   | 域别名（使用个人或公司所有的域，输入多个时请以\n令牌区分输入。）|
| maxAge                | Integer | 可选      | 0      | 0~2,147,483,647                                            | 缓存到期时间（秒），默认值0为604,800秒。              |
| referrers             | String  | 可选      |        | 包括’\n’令牌，最大1024个字符                                  | 引用（输入多个时请以\\n令牌区分输入。)  |
| origins               | List    | 必需      |        |                                                              | 原始服务器                                                    |
| origins[0].origin     | String  | 必需      |        | 最大255个字符                                                   | 原始服务器（domain或IP）                                      |
| origins[0].port       | Integer | 必需      |        | 0~65,536                                                     | 原始服务器端口                                               |
| origins[0].originPath | String  | 可选      |        | 最大8192个字符                                                  | 原始服务器下级路径                                          |
| callback              | Object  | 可选      |        | 接收CDN服务分发结果的回调URL（回调设置为可选输入。）|                                                              |
| callback.httpMethod   | String  | 必需      |        | GET/POST/PUT                                                 | 回调的HTTP Method                                           |
| callback.url          | String  | 必需      |        | 最大1024个字符                                                  | 回调URL                                                     |


#### 响应


【响应正文】

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    }
}
```


【字段】

| 字段                   | 类型      | 说明     |
| -------------------- | ------- | ------ |
| header               | Object  | 标头区域  |
| header.isSuccessful  | Boolean | 是否成功  |
| header.resultCode    | Integer | 结果代码  |
| header.resultMessage | String  | 结果信息 |


### 服务部分修改

更改服务的部分设置时，可以使用部分修改API。

#### 请求


[URI]

| 方法   | URI                                  |
| ----- | ------------------------------------ |
| PATCH | /v1.5/appKeys/{appKey}/distributions |


【请求正文】

```json
{
        "domain" : "sample.cdn.toastcloud.com",
        "useOrigin" : "N",
        "maxAge": 86400,
        "referrerType" : "BLACKLIST",
        "referrers" : "test.com",
        "origins" : [
            {
                "origin" : "static.resource.com",
                "port" : 80,
                "originPath" : "/latest/resources"
            }
        ],
        "callback": {
            "httpMethod": "GET",
            "url": "http://test.callback.com/cdn?=appKey={appKey}&status={status}&domain={domain}"
        },
        "description" : "change contents"       
}
```


【字段】

| 名称                  | 类型    | 是否必需 | 默认值 | 有效范围                                                    | 说明                                                         |
| --------------------- | ------- | --------- | ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| domain                | String  | 必需      |        | 最大255个字符                                                   | 要修改的域（服务名）                                   |
| useOrigin             | String  | 可选      |        | Y/N                                                          | 缓存到期设置（Y：使用原始设置，N：使用用户设置）       |
| referrerType          | String  | 可选      |        | BLACKLIST / WHITELIST                                        | 引用访问管理（”BLACKLIST”：黑名单，”WHITELIST”：白名单） |
| description           | String  | 可选      |        | 最大255个字符                                                   | 说明                                                         |
| domainAlias           | String  | 可选      |        | 最大255个字符                                                   | 域别名（使用个人或公司所有的域，输入多个时请以\n令牌分开输入。）|
| maxAge                | Integer | 可选      | 0      | 0~2,147,483,647                                              | 缓存到期时间（秒），默认值0为604,800秒。           |
| referrers             | String  | 可选      |        | 包括’\n’令牌，最大1024个字符                                  | 域别名（输入多个时请以\\n令牌分开输入。)  |
| origins               | List    | 可选      |        |                                                              | 原始服务器                                                    |
| origins[0].origin     | String  | 可选      |        | 最大255个字符                                                   | 原始服务器（domain或IP）                                     |
| origins[0].port       | Integer | 可选      |        | 0~65,536                                                   | 原始服务器端口                                               |
| origins[0].originPath | String  | 可选      |        | 最大8192个字符                                                  | 原始服务器下级路径                                          |
| callback              | Object  | 可选      |        | 接收CDN服务分发结果的回调URL（回调设置为可选输入。）|                                                              |
| callback.httpMethod   | String  | 可选      |        | GET/POST/PUT                                                 | 回调的HTTP Method                                           |
| callback.url          | String  | 可选      |        | 最大1024个字符                                                  | 回调URL                                                     |

- 设置origins字段时，origin, port, originPath字段为必须输入值。 
- 设置callback字段时，httpMethod, url字段为必须输入值。 

#### 响应


【响应正文】

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    }
}
```


【字段】

| 字段                   | 类型      | 说明     |
| -------------------- | ------- | ------ |
| header               | Object  | 标头区域  |
| header.isSuccessful  | Boolean | 是否成功  |
| header.resultCode    | Integer | 结果代码  |
| header.resultMessage | String  | 结果信息 |


### 删除服务

#### 请求


[URI]

| 方法    | URI                                  |
| ------ | ------------------------------------ |
| DELETE | /v1.5/appKeys/{appKey}/distributions |


【请求正文】

```json
{
    "domains" : [
        "lhcsxuo0.cdn.toastcloud.com"
    ]
}
```


【字段】

| 名称      | 类型     | 是否必须 | 默认值  | 有效范围 | 说明                    |
| ------- | ------ | ----- | ---- | ----- | --------------------- |
| domains | String | 必需    |      |       | 要删除的域，可输入多个域 |

**\* 输入多个域时，对应的服务将全部终止。**

#### 响应


【响应正文】

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    }
}
```


【字段】

| 字段                   | 类型      | 说明     |
| -------------------- | ------- | ------ |
| header               | Object  | 标头区域  |
| header.isSuccessful  | Boolean | 是否成功  |
| header.resultCode    | Integer | 结果代码  |
| header.resultMessage | String  | 结果信息 |

## 重新分发缓存API

### 重新分发缓存（Purge）

#### 请求

[URI]

| 方法  | URI                           |
| ---- | ----------------------------- |
| POST | /v1.5/appKeys/{appKey}/purges |


【请求正文】

```json
{
	"domain": "sample.cdn.toastcloud.com",
	"purgeType": "ITEM",
	"purgeList":"/img_01.png"
}
```


【字段】

| 名称      | 类型   | 是否必须 | 默认值 | 有效范围             | 说明                                                         |
| --------- | ------ | --------- | ------ | --------------------- | ------------------------------------------------------------ |
| domain    | String | 必需      |        | 最大255个字            | 要重新分发的域（服务）名                                 |
| purgeType | List   | 必需      |        | ITEM / WILDCARD / ALL | 重新分发类型("ITEM", "WILDCARD", "ALL")                       |
| purgeList | String | 可选      |        |                       | 重新分发对象项目列表（输入多个时， 请以\\n令牌区分输入，若purgeType为ALL，也可不输入。）|

#### 响应

【响应正文】

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    },
    "purgeSeq" : 1
}
```


【字段】

| 字段                   | 类型      | 说明        |
| -------------------- | ------- | --------- |
| header               | Object  | 标头区域     |
| header.isSuccessful  | Boolean | 是否成功     |
| header.resultCode    | Integer | 结果代码     |
| header.resultMessage | String  | 结果信息    |
| purgeSeq             | Integer | 请求重新分发编号 |

### 查询重新分发缓存（Purge）

#### 请求


[URI]

| 方法  | URI                           |
| ---- | ----------------------------- |
| GET  | /v1.5/appKeys/{appKey}/purges |


【参数】

| 名称           | 类型      | 是否必需 | 默认值  | 有效范围                                    | 说明              |
| ------------ | ------- | ----- | ---- | ---------------------------------------- | --------------- |
| domain       | String  | 必需    |      | 最大255个字                                  | 域（服务）名     |
| page         | Integer | 可选    |      |                                          | 页编号          |
| itemsPerPage | Integer | 可选    |      |                                          | 每页重新分发的项目个数 |
| startTime    | String  | 可选    |      | “yyyy-MM-ddTHH:mm:ss.SSSZ” ex)”2018-02-22T09:00:00.000Z”(UTC) | 搜索时间段           |
| endTime      | String  | 可选    |      | “yyyy-MM-ddTHH:mm:ss.SSSZ” ex)”2018-02-22T09:00:00.000Z”(UTC) | 搜索时间段           |


```
curl -X GET "https://api-gw.cloud.toast.com/tc-cdn/v1.5/appKeys/{appKey}/purges?domain={domain}" \
 -H "Authorization: {secretKey}" \
 -H "Content-Type: application/json"
```

#### 响应


【响应正文】

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    },
    "totalItems" :  2,
    "purges" : [
        {
            "seq" :  593,
            "distributionSeq" :  8445,
            "purgeId" :  552256938,
            "progress" :  0,
            "purgeTime" :  1496979544701,
            "lastCheckTime" :  1496979559013,
            "type" :  "ITEM",
            "path" :  "/prod_apigateway/img_plugin_cors_1.png"
        },
        {
            "seq" :  589,
            "distributionSeq" :  8445,
            "purgeId" :  498528821,
            "progress" :  100,
            "purgeTime" :  1482385936413,
            "lastCheckTime" :  1482385997077,
            "type" :  "ALL",
            "path" :  ""
        }
    ]
}
```


【字段】

| 字段                      | 类型      | 说明                                |
| ----------------------- | ------- | --------------------------------- |
| header                  | Object  | 标头区域                             |
| header.isSuccessful     | Boolean | 是否成功                             |
| header.resultCode       | Integer | 结果代码                             |
| header.resultMessage    | String  | 结果信息                            |
| totalItems              | Integer | 重新分发的总个数                         |
| purges                  | List    | 重新分发项目列表                        |
| purges[0].seq           | Integer | 重新分发请求编号                         |
| purges[0].progress      | Integer | 重新分发进度                        |
| purges[0].purgeTime     | Long    | 重新分发请求时间                         |
| purges[0].lastCheckTime | Long    | 重新分发执行的最后确认时间                  |
| purges[0].type          | String  | 重新分发类型("ITEM", "WILDCARD", "ALL") |
| purges[0].path          | String  | 重新分发请求项目                         |

