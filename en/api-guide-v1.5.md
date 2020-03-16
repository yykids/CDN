## Content Delivery > CDN > API v1.5 Guide

TOAST CDN에서 제공하는 Public API v1.5를 설명합니다.

## Common API Information  

### Domain

| Name         | Domain                             |
| --------------- | ------------------------------------- |
| Public API CDN Domain | https://api-gw.cloud.toast.com/tc-cdn |

### Prerequisites

Requires Appkey and SecretKey API, which are available in **URL & Appkey** on top right of the console.  

### Common Request Information

#### Request Header

| Name          | Description                   |
| ------------- | ----------------------------- |
| Authorization | SecretKey issued on a console |

#### Path Parameter  

In all APIs, the appKey must be specified in the path parameter.
* e.g.) /v1.5/appKeys/**{appKey}**/distributions

| Name   | Description                |
| ------ | -------------------------- |
| appKey | Appkey issued on a console |

### Common Response Information

#### Header

Respond with **200 OK** to all API requests. For more details, see the header at the response body as below.

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Success"
    }
}
```


[Fields]

| Field                | Type    | Description       |
| -------------------- | ------- | ----------------- |
| header               | Object  | Header area       |
| header.isSuccessful  | Boolean | Successful or not |
| header.resultCode    | Integer | Result code       |
| header.resultMessage | String  | Result message    |

#### CDN Status Codes

Below shows the status codes of CDN service, which are available at the query of service.  

| Value      | Description                           |
| ---------- | ------------------------------------- |
| OPENING    | Service is starting                   |
| OPEN       | In service                            |
| MODIFYING  | Under modification                    |
| RESUME     | Resumed                               |
| SUSPENDING | Under suspension                      |
| SUSPEND    | Suspended                             |
| CLOSING    | Closing                               |
| CLOSE      | Closed                                |
| ERROR      | Error occurred while creating service |


## Service API

### Create  

#### Request


[URI]

| Method | URI                                  |
| ------ | ------------------------------------ |
| POST   | /v1.5/appKeys/{appKey}/distributions |


[Request Body]

```json
{
   "distributions":[
       {
			"region": "GLOBAL",
            "useOriginHttpProtocolDowngrade": false,
            "forwardHostHeader": "ORIGIN_HOSTNAME",
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

[Field]

| Name                                   | Type    | Required | Default | Valid Range                                  | Description                                                  |
| -------------------------------------- | ------- | -------- | ------- | -------------------------------------------- | ------------------------------------------------------------ |
| distributions                          | List    | Required |         |                                              | List of CDN objects to create                                |
| distributions[0].region                | String  | 필수      |        | GLOBAL                | 서비스 지역("GLOBAL": 글로벌)           |
| distributions[0].useOriginHttpProtocolDowngrade | Boolean  | 필수     |        | true/false         | 원본 서버가 HTTP 응답만 가능한 경우, CDN 서버에서 원본 서버로 요청 시 HTTPS 요청을 HTTP 요청으로 다운그레이드하기 위한 설정 사용 여부 |
| distributions[0].forwardHostHeader     | String  | 필수      |        | ORIGIN_HOSTNAME/REQUEST_HOST_HEADER   | CDN 서버가 원본 서버로 콘텐츠 요청 시 전달 할 호스트 헤더 설정("ORIGIN_HOSTNAME" : 원본 서버의 호스트네임으로 설정, "REQUEST_HOST_HEADER" : 클라이언트 요청의 호스트헤더로 설정)|
| distributions[0].useOrigin             | String  | Required |         | Y/N                                          | Cache expiration setting ("Y": Original setting, "N":User-configured) |
| distributions[0].referrerType          | String  | Required |         | BLACKLIST/WHITELIST                          | Referrer access management ("BLACKLIST": Blacklist, "WHITELIST": Whitelist) |
| distributions[0].description           | String  | Optional |         | Up to  255 characters                        | Description                                                  |
| distributions[0].domainAlias           | String  | Optional |         | Up to 255 characters                         | Domain alias (Use personal or company-owned domains; delimit by \n tokens.) |
| distributions[0].maxAge                | Integer | Optional | 0       | 0~2,147,483,647                              | Cache expiration time (second); default 0 refers to 604,800 seconds. |
| distributions[0].referrers             | String  | Optional |         | Up to 1024 characters, including '\n' tokens | Referrers (Delimit by \n tokens.)                            |
| distributions[0].origins               | List    | Required |         |                                              | List of origin server objects                                |
| distributions[0].origins[0].origin     | String  | Required |         | Up to 255 characters                         | Origin server (domain or IP)                                 |
| distributions[0].origins[0].port       | Integer  | 선택      |        |[콘솔 사용 가이드] > [[원본서버](./console-guide/#_2)의 [표2] 사용 가능한 원본 서버 포트 번호] 참고| 원본 서버 HTTP 프로토콜 포트<br>(origins[0].port 설정 시 origins[0].httpPort와 origins[0].httpsPort는 입력하지 않습니다.)|
| distributions[0].origins[0].httpPort   | Integer  | 선택      |        |[콘솔 사용 가이드] > [[원본서버](./console-guide/#_2)의 [표2] 사용 가능한 원본 서버 포트 번호] 참고| 원본 서버 HTTP 프로토콜 포트<br>(origins[0].port 미설정 시 origins[0].httpPort와 origins[0].httpsPort 중 하나는 필수 입력해야 합니다.)  |
| distributions[0].origins[0].httpsPort  | Integer  | 선택      |        |[콘솔 사용 가이드] > [[원본서버](./console-guide/#_2)의 [표2] 사용 가능한 원본 서버 포트 번호] 참고 | 원본 서버 HTTPS 프로토콜 포트<br>(origins[0].port 미설정 시 origins[0].httpPort와 origins[0].httpsPort 중 하나는 필수 입력해야 합니다.) |
| distributions[0].origins[0].originPath | String  | Optional |         | Up to 8192 characters                        | Lower paths of origin server (path must include /.)          |
| distributions[0].callback              | Object  | Optional |         |                                              | Callback URL to receive processing result of CDN creation (callback setting is optional.) |
| distributions[0].callback.httpMethod   | String  | Required |         | GET/POST/PUT                                 | HTTP method of callback                                      |
| distributions[0].callback.url          | String  | Required |         | Up to 1024 characters                        | Callback URL                                                 |

- forwardHostHeader 필드와 useOriginHttpProtocolDowngrade 필드는 *.toastcdn.net 도메인을 사용할 경우 설정 가능하며, *.cdn.toastcloud.com 도메인은 설정 불가능합니다.


#### Response


[Response Body]

```json
{
    "header": {
        "resultCode": Integer,
        "resultMessage": String,
        "isSuccessful": Boolean
    },
    "distributions": [
        {
            "domain" : "lhcsxuo0.toastcdn.net"
            "region" :  "GLOBAL",
            "description" :  "api test pad",
            "status" : "OPENING",
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


[Field]

| Field                                  | Type    | Description                                                  |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | Header area                                                  |
| header.isSuccessful                    | Boolean | Successful or not                                            |
| header.resultCode                      | Integer | Result code                                                  |
| header.resultMessage                   | String  | Result message                                               |
| distributions                          | List    | List of created CDN objects                                  |
| distributions[0].domain                | String  | Created domain (service) name                                |
| distributions[0].domainAlias           | String  | 도메인 별칭 목록(개인 혹은 회사가 소유한 도메인 사용)              |
| distributions[0].region                | String  | 서비스 지역("GLOBAL": 글로벌)            |
| distributions[0].description           | String  | Description                                                  |
| distributions[0].status                | String  | CDN status code (see CDN status codes in [Table])            |
| distributions[0].createTime            | String  | Date and time of creation                                    |
| distributions[0].useOrigin             | String  | Whether to set origin server <br />("Y": Origin server setting, "N": User-configured) |
| distributions[0].maxAge                | String  | Cache expiration time (second)                               |
| distributions[0].referrerType          | String  | Referrer access control ("BLACKLIST": Blacklist, "WHITELIST": Whitelist) |
| distributions[0].referrers             | String  | List of referrers                                            |
| distributions[0].origins               | List    | List of origin server objects                                |
| distributions[0].origins[0].origin     | String  | Origin server (domain or IP)                                 |
| distributions[0].origins[0].originPath | String  | Lower paths of origin server                                 |
| distributions[0].origins[0].port       | Integer | Origin server port                                           |
| distributions[0].callback              | Object  | Callback to receive service processing results               |
| distributions[0].callback.httpMethod   | String  | HTTP method of callback                                      |
| distributions[0].callback.url          | String  | Callback URL                                                 |


### Get

#### Request


[URI]

| Method | URI                                  |
| ------ | ------------------------------------ |
| GET    | /v1.5/appKeys/{appKey}/distributions |


[Parameter]

| Name | Type | Required | Valid Range | Description  |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| domain | String | Optional | Up to 255 characters | Domain (service name) to get |
| status | String | Optional | CDN status codes | CDN status code (see CDN status codes in [Table]) |

[Example]
```
curl -X GET "https://api-gw.cloud.toast.com/tc-cdn/v1.5/appKeys/{appKey}/distributions?domain={domain}" \
 -H "Authorization: {secretKey}" \
 -H "Content-Type: application/json"
```

#### Response


[Response Body]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
      "isSuccessful" :  true
    },
    "domain" :  "lhcsxuo0.toastcdn.com",
    "domainAlias" :  "test.domain.com",
    "region" :  "GLOBAL",
    "description" :  "api test pad",
    "status" :  "OPENING",
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


[Field]

| Field                                  | Type    | Description                                                  |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | Header area                                                  |
| header.isSuccessful                    | Boolean | Successful or not                                            |
| header.resultCode                      | Integer | Result code                                                  |
| header.resultMessage                   | String  | Result message                                               |
| distributions                          | List    | List of created CDN objects                                  |
| distributions[0].domain                | String  | Domain (service) name                                        |
| distributions[0].domainAlias           | String  | 도메인 별칭 목록(개인 혹은 회사가 소유한 도메인 사용)                    |
| distributions[0].region                | String  | 서비스 지역("GLOBAL": 글로벌)                                    |
| distributions[0].description           | String  | Description                                                  |
| distributions[0].status                | String  | CDN status code (see CDN status codes in [Table])            |
| distributions[0].createTime            | String  | Date and time of creation                                    |
| distributions[0].useOrigin             | String  | Whether to use origin server setting <br />("Y": Origin server setting, "N": User-configured) |
| distributions[0].maxAge                | String  | Cache expiration time (second)                               |
| distributions[0].referrerType          | String  | Referrer access management ("BLACKLIST": Blacklist, "WHITELIST": Whitelist) |
| distributions[0].referrers             | String  | List of referrers                                            |
| distributions[0].origins               | List    | List of origin server objects                                |
| distributions[0].origins[0].origin     | String  | Origin server (domain or IP)                                 |
| distributions[0].origins[0].originPath | String  | Lower paths of origin server                                 |
| distributions[0].origins[0].port       | Integer | Origin server port                                           |
| distributions[0].callback              | Object  | Callback to receive service deployment results               |
| distributions[0].callback.httpMethod   | String  | HTTP method of callback                                      |
| distributions[0].callback.url          | String  | Callback URL                                                 |


### Modify

#### Request


[URI]

| Method | URI                                  |
| ------ | ------------------------------------ |
| PUT    | /v1.5/appKeys/{appKey}/distributions |


[Request Body]

```json
{
        "domain" : "sample.cdn.toastcloud.com",
        "useOriginHttpProtocolDowngrade": false,
        "forwardHostHeader": "ORIGIN_HOSTNAME",
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

[Field]

| Name                  | Type    | Required | Default | Valid Range                                                  | Description                                                  |
| --------------------- | ------- | -------- | ------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| domain                | String  | Required |         | Up to 255 characters                                         | Domain (service name) to modify                              |
| useOriginHttpProtocolDowngrade | Boolean  | 필수     |        | true/false         | 원본 서버가 HTTP 응답만 가능한 경우, CDN 서버에서 원본 서버로 요청 시 HTTPS 요청을 HTTP 요청으로 다운그레이드하기 위한 설정 사용 여부 |
| forwardHostHeader     | String  | 필수      |        | ORIGIN_HOSTNAME/REQUEST_HOST_HEADER   | CDN 서버가 원본 서버로 콘텐츠 요청 시 전달 할 호스트 헤더 설정("ORIGIN_HOSTNAME" : 원본 서버의 호스트네임으로 설정, "REQUEST_HOST_HEADER" : 클라이언트 요청의 호스트헤더로 설정 |
| useOrigin             | String  | Required |         | Y/N                                                          | Cache expiration setting (Y: Original setting, "N": User-configured) |
| referrerType          | String  | Required |         | BLACKLIST/WHITELIST                                          | Referrer access management ("BLACKLIST": Blacklist, "WHITELIST": Whitelist) |
| description           | String  | Optional |         | Up to 255 characters                                         | Description                                                  |
| domainAlias           | String  | Optional |         | Up to 255 characters                                         | Domain alias (Personal or company-owned domains; delimit by \n tokens.) |
| maxAge                | Integer | Optional | 0       | 0 ~ 2,147,483,647                                            | Cache expiration time (second); default 0 refers to 604,800 seconds. |
| referrers             | String  | Optional |         | Up to 1024 characters, including '\n\' tokens                | Referrers (delimit by \n tokens. )                           |
| origins               | List    | Required |         |                                                              | Origin server                                                |
| origins[0].origin     | String  | Required |         | Up to 255 characters                                         | Origin server (domain or IP)                                 |
| origins[0].port       | Integer  | 선택      |        |[콘솔 사용 가이드] > [[원본서버](./console-guide/#_2)의 [표2] 사용 가능한 원본 서버 포트 번호] 참고| 원본 서버 HTTP 프로토콜 포트<br>(origins[0].port 설정 시 origins[0].httpPort와 origins[0].httpsPort는 입력하지 않습니다.)|
| origins[0].httpPort   | Integer  | 선택      |        |[콘솔 사용 가이드] > [[원본서버](./console-guide/#_2)의 [표2] 사용 가능한 원본 서버 포트 번호] 참고| 원본 서버 HTTP 프로토콜 포트<br>(origins[0].port 미설정 시 origins[0].httpPort와 origins[0].httpsPort 중 하나는 필수 입력해야 합니다.)  |
| origins[0].httpsPort  | Integer  | 선택      |        |[콘솔 사용 가이드] > [[원본서버](./console-guide/#_2)의 [표2] 사용 가능한 원본 서버 포트 번호] 참고 | 원본 서버 HTTPS 프로토콜 포트<br>(origins[0].port 미설정 시 origins[0].httpPort와 origins[0].httpsPort 중 하나는 필수 입력해야 합니다.) |
| origins[0].originPath | String  | Optional |         | Up to 8192 characters                                        | Lower paths of origin server                                 |
| callback              | Object  | Optional |         | Callback URL to receive CDN service deployment results (callback setting is optional.) |                                    |
| callback.httpMethod   | String  | Required |         | GET/POST/PUT                                                 | HTTP method of callback                                      |
| callback.url          | String  | Required |         | Up to 1024 characters                                        | Callback URL                                                 |

- forwardHostHeader 필드와 useOriginHttpProtocolDowngrade 필드는 *.toastcdn.net 도메인을 사용할 경우 설정 가능하며, *.cdn.toastcloud.com 도메인은 설정 불가능합니다.

#### Response


[Response Body]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    }
}
```


[Field]

| Field                | Type    | Description       |
| -------------------- | ------- | ----------------- |
| header               | Object  | Header area       |
| header.isSuccessful  | Boolean | Successful or not |
| header.resultCode    | Integer | Result code       |
| header.resultMessage | String  | Result message    |


### Patch

Apply partial modification API to change a part of the service setting.

#### Request


[URI]

| Method | URI                                  |
| ------ | ------------------------------------ |
| PATCH  | /v1.5/appKeys/{appKey}/distributions |


[Request Body]

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


[Field]

| Name                  | Type    | Required | Default | Valid Range                                                  | Description                                                  |
| --------------------- | ------- | -------- | ------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| domain                | String  | Required |         | Up to 255 characters                                         | Domain (service name) to modify                              |
| useOriginHttpProtocolDowngrade | Boolean  | 선택     |        | true/false         | 원본 서버가 HTTP 응답만 가능한 경우, CDN 서버에서 원본 서버로 요청 시 HTTPS 요청을 HTTP 요청으로 다운그레이드하기 위한 설정 사용 여부 |
| forwardHostHeader     | String  | 선택      |        | ORIGIN_HOSTNAME/REQUEST_HOST_HEADER   | CDN 서버가 원본 서버로 콘텐츠 요청 시 전달 할 호스트 헤더 설정("ORIGIN_HOSTNAME": 원본 서버의 호스트 이름으로 설정, "REQUEST_HOST_HEADER": 클라이언트 요청의 호스트 헤더로 설정 |
| useOrigin             | String  | Optional |         | Y/N                                                          | Cache expiration setting (Y: Original setting, N: User-configured) |
| referrerType          | String  | Optional |         | BLACKLIST / WHITELIST                                        | Referrer access management ("BLACKLIST": Blacklist, "WHITELIST": Whitelist) |
| description           | String  | Optional |         | Up to 255 characters                                         | Description                                                  |
| domainAlias           | String  | Optional |         | Up to 255 characters                                         | Domain alias (personal or company-owned domains; delimit by \n tokens.) |
| maxAge                | Integer | Optional | 0       | 0~2,147,483,647                                              | Cache expiration time (second); default 0 refers to 604,800 seconds. |
| referrers             | String  | Optional |         | Up to 1024 characters, including '\n' tokens                 | Referrers (delimit by \n tokens. )                           |
| origins               | List    | Optional |         |                                                              | Origin server                                                |
| origins[0].origin     | String  | Optional |         | Up to 255 characters                                         | Origin server (domain or IP)                                 |
| origins[0].port       | Integer | 선택      |        |[콘솔 사용 가이드] > [[원본서버](./console-guide/#_2)의 [표2] 사용 가능한 원본 서버 포트 번호] 참고| 원본 서버 HTTP 프로토콜 포트<br>(origins[0].port 설정 시 origins[0].httpPort와 origins[0].httpsPort는 입력하지 않습니다.)|
| origins[0].httpPort   | Integer  | 선택      |        |[콘솔 사용 가이드] > [[원본서버](./console-guide/#_2)의 [표2] 사용 가능한 원본 서버 포트 번호] 참고| 원본 서버 HTTP 프로토콜 포트<br>(origins[0].port 미설정 시 origins[0].httpPort와 origins[0].httpsPort 중 하나는 필수 입력해야 합니다.)  |
| origins[0].httpsPort  | Integer  | 선택      |        |[콘솔 사용 가이드] > [[원본서버](./console-guide/#_2)의 [표2] 사용 가능한 원본 서버 포트 번호] 참고 | 원본 서버 HTTPS 프로토콜 포트<br>(origins[0].port 미설정 시 origins[0].httpPort와 origins[0].httpsPort 중 하나는 필수 입력해야 합니다.) |
| origins[0].originPath | String  | Optional |         | Up to 8192 characters                                        | Lower paths of origin server                                 |
| callback              | Object  | Optional |         | Callback URL to receive CDN service deployment results (callback setting is optional.) |                                    |
| callback.httpMethod   | String  | Optional |         | GET/POST/PUT                                                 | HTTP method of callback                                      |
| callback.url          | String  | Optional |         | Up to 1024 characters                                        | Callback URL                                                 |

- origins 필드를 설정할 때 origin, originPath 필드는 필수 입력값이며, port 필드나 httpPort, httpsPort 필드 중 하나는 필수로 입력해야 합니다.
- callback 필드를 설정할 때 httpMethod, url 필드는 필수 입력값입니다.
- forwardHostHeader 필드와 useOriginHttpProtocolDowngrade 필드는 *.toastcdn.net 도메인을 사용할 경우 설정 가능하며, *.cdn.toastcloud.com 도메인은 설정 불가능합니다.

#### Response


[Response Body]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    }
}
```


[Field]

| Field                | Type    | Description       |
| -------------------- | ------- | ----------------- |
| header               | Object  | Header area       |
| header.isSuccessful  | Boolean | Successful or not |
| header.resultCode    | Integer | Result code       |
| header.resultMessage | String  | Result message    |


### Delete

#### Request


[URI]

| Method | URI                                  |
| ------ | ------------------------------------ |
| DELETE | /v1.5/appKeys/{appKey}/distributions |


[Request Body]

```json
{
    "domains" : [
        "lhcsxuo0.cdn.toastcloud.com"
    ]
}
```


[Field]

| Name    | Type   | Required | Default | Valid Range | Description                                 |
| ------- | ------ | -------- | ------- | ----------- | ------------------------------------------- |
| domains | String | Y        |         |             | Domains to delete; multiple domains allowed |

**\* With the input of many domains, all corresponding services are closed.**

#### Response


[Response Body]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    }
}
```


[Field]

| Field                | Type    | Description       |
| -------------------- | ------- | ----------------- |
| header               | Object  | Header area       |
| header.isSuccessful  | Boolean | Successful or not |
| header.resultCode    | Integer | Result code       |
| header.resultMessage | String  | Result message    |

## Cache Purge API

### Purge

#### Request

[URI]

| Method | URI                           |
| ------ | ----------------------------- |
| POST   | /v1.5/appKeys/{appKey}/purges |


[Request Body]

```json
{
	"domain": "sample.cdn.toastcloud.com",
	"purgeType": "ITEM",
	"purgeList":"/img_01.png"
}
```


[Field]

| Name      | Type   | Required | Default | Valid Range           | Description                                                  |
| --------- | ------ | -------- | ------- | --------------------- | ------------------------------------------------------------ |
| domain    | String | Required |         | Up to 255 characters  | Domain (service) name to purge                               |
| purgeType | List   | Required |         | ITEM / WILDCARD / ALL | Purge type ("ITEM", "WILDCARD", or "ALL")                    |
| purgeList | String | Optional |         |                       | List of items to purge (delimit by \n tokens; not required, if the purge type is ALL.) |

#### Response

[Response Body]

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


[Field]

| Field                | Type    | Description             |
| -------------------- | ------- | ----------------------- |
| header               | Object  | Header area             |
| header.isSuccessful  | Boolean | Successful or not       |
| header.resultCode    | Integer | Result code             |
| header.resultMessage | String  | Result message          |
| purgeSeq             | Integer | Purge requesting number |

- CDN 서비스를 신규로 생성한 후 약 1시간 이내에는 캐시 재배포 요청이 실패할 수 있습니다. 이후에도 실패가 지속되는 경우 고객센터로 문의해주시기 바랍니다.
- 퍼지 API은 사용량 제한 정책이 있습니다. 자세한 내용은 [콘솔 사용 가이드 > CDN 캐시 재배포](./console-guide/#cdn_3)의 '캐시 재배포 사용량 제한' 내용을 확인해주세요.
- ITEM, WILDCARD 타입은 요청 당 퍼지 경로 수가 제한 되어 있습니다. 초과하여 요청한 경우 요청 당 퍼지 경로 개수 만큼씩 나누어 퍼지 요청이 진행됩니다. 이 경우 처음 퍼지 요청의 재배포 요청 번호만 응답으로 전달됩니다.   

### Get Cache Purges

#### Request


[URI]

| Method | URI                           |
| ------ | ----------------------------- |
| GET    | /v1.5/appKeys/{appKey}/purges |


[Parameter]

| Name         | Type    | Required | Default | Valid Range                                                  | Description                         |
| ------------ | ------- | -------- | ------- | ------------------------------------------------------------ | ----------------------------------- |
| domain       | String  | Required |         | Up to 255 characters                                         | Domain (service) name               |
| page         | Integer | Optional |         |                                                              | Page number                         |
| itemsPerPage | Integer | Optional |         |                                                              | Number of purged items on each page |
| startTime    | String  | Optional |         | "yyyy-MM-ddTHH:mm:ss.SSSZ" e.g.)"2018-02-22T09:00:00.000Z"(UTC) | Search of period                    |
| endTime      | String  | Optional |         | "yyyy-MM-ddTHH:mm:ss.SSSZ" e.g.)"2018-02-22T09:00:00.000Z"(UTC) | Search of period                    |


```
curl -X GET "https://api-gw.cloud.toast.com/tc-cdn/v1.5/appKeys/{appKey}/purges?domain={domain}" \
 -H "Authorization: {secretKey}" \
 -H "Content-Type: application/json"
```

#### Response


[Response Body]

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


[Field]

| Field                 | Type  | Description                     |
| ----------------------- | ------- | --------------------------------- |
| header                  | Object  | Header area                  |
| header.isSuccessful     | Boolean | Successful or not        |
| header.resultCode       | Integer | Result code                  |
| header.resultMessage    | String  | Result message             |
| totalItems              | Integer | Total number of purges  |
| purges                  | List    | List of purged items   |
| purges[0].seq           | Integer | Purge requesting number |
| purges[0].progress      | Integer | Purge progress rate |
| purges[0].purgeTime     | Long    | Purge requesting time |
| purges[0].lastCheckTime | Long    | Last confirmed purge time |
| purges[0].type          | String  | Purge type ("ITEM", "WILDCARD",or "ALL") |
| purges[0].path          | String  | Requested purge items |

## 콜백 응답
CDN 서비스에 콜백 기능이 설정되어있을 경우, 생성/수정/일시정지/재개/삭제 의 변경 작업 완료 시 콜백 URL에 아래와 같은 응답값을 전달합니다.

[응답 본문]
``` json
{
  "header" : {
    "resultCode" :  0,
    "resultMessage" :  "SUCCESS",
    "isSuccessful" :  true
  },
  "distribution":{
      "appKey": "String",
      "domain" : "lhcsxuo0.toastcdn.net",
      "domainAlias" : "test.domain.com",
      "region" : "GLOBAL",
      "description" : "api test pad",
      "status" : "OPENING",
      "createTime" : 1498613094692,
      "useOrigin" : "N",
      "maxAge" : "100",
      "referrerType" : "BLACKLIST",
      "referrers" : "test.com",
      "deleteTime": "DateTime",
      "origins":[
         {
            "origin": "static.toastoven.net",
            "originPath": "/path",
            "port": "80"
         }
      ],
      "callback":{
         "httpMethod": "GET",
         "url": "http://test.callback.com/cdn?=appKey={appKey}&status={status}&domain={domain}"
      }
  }
}
```

[필드]

| 필드                                   | 타입    | 설명                                                         |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | 헤더 영역                                                    |
| header.isSuccessful                    | Boolean | 성공 여부                                                    |
| header.resultCode                      | Integer | 결과 코드                                                    |
| header.resultMessage                   | String  | 결과 메시지                                                  |
| distribution                          | Object    | 변경작업이 완료된 CDN 오브젝트                                     |
| distribution.appKey                   | String    | 앱키                                  |
| distribution.domain                | String  | 도메인 이름(서비스 이름)                                     |
| distribution.domainAlias           | String  | 소유 도메인                                                  |
| distribution.region                | String  | 서비스 지역("GLOBAL": 글로벌)             |
| distribution.description           | String  | 설명                                                         |
| distribution.status                | String  | CDN 상태 코드([표] CDN 상태 코드 참고)                                 |
| distribution.createTime            | String  | 생성 일시                                                    |
| distribution.deleteTime            | String  | 삭제 일시                                                    |
| distribution.useOrigin             | String  | 원본 서버 설정 사용 여부("Y": 원본 서버 설정 사용, "N": 사용자 설정) |
| distribution.maxAge                | String  | 캐시 만료 시간(초)                                           |
| distribution.referrerType          | String  | 리퍼러 접근 관리("BLACKLIST": 블랙리스트, "WHITELIST": 화이트리스트) |
| distribution.referrers             | String  | 리퍼러 목록                                                  |
| distribution.origins               | List    | 원본 서버 오브젝트 목록                                      |
| distribution.origins[0].origin     | String  | 원본 서버(domain 또는 IP)                                      |
| distribution.origins[0].originPath | String  | 원본 서버 하위 경로                                          |
| distribution.origins[0].port       | Integer | 원본 서버 포트                                               |
| distribution.callback              | Object  | 서비스 배포 처리 결과를 통보받을 콜백                        |
| distribution.callback.httpMethod   | String  | 콜백의 HTTP Method                                           |
| distribution.callback.url          | String  | 콜백 URL                                                     |