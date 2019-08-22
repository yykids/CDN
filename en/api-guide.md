## Content Delivery > CDN > API Guide

This document describes Public API provided by TOAST CDN.

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

[Field]

| Name                                   | Type    | Required | Default | Valid Range                                  | Description                                                  |
| -------------------------------------- | ------- | -------- | ------- | -------------------------------------------- | ------------------------------------------------------------ |
| distributions                          | List    | Required |         |                                              | List of CDN objects to create                                |
| distributions[0].region                | String  | Required |         | LOCAL/GLOBAL                                 | Service region ("LOCAL": Korea, "GLOBAL": Global)            |
| distributions[0].useOrigin             | String  | Required |         | Y/N                                          | Cache expiration setting ("Y": Original setting, "N":User-configured) |
| distributions[0].referrerType          | String  | Required |         | BLACKLIST/WHITELIST                          | Referrer access management ("BLACKLIST": Blacklist, "WHITELIST": Whitelist) |
| distributions[0].description           | String  | Optional |         | Up to  255 characters                        | Description                                                  |
| distributions[0].domainAlias           | String  | Optional |         | Up to 255 characters                         | Domain alias (Use personal or company-owned domains; delimit by \n tokens.) |
| distributions[0].maxAge                | Integer | Optional | 0       | 0~2,147,483,647                              | Cache expiration time (second); default 0 refers to 604,800 seconds. |
| distributions[0].referrers             | String  | Optional |         | Up to 1024 characters, including '\n' tokens | Referrers (Delimit by \n tokens.)                            |
| distributions[0].origins               | List    | Required |         |                                              | List of origin server objects                                |
| distributions[0].origins[0].origin     | String  | Required |         | Up to 255 characters                         | Origin server (domain or IP)                                 |
| distributions[0].origins[0].port       | String  | Required |         | 0~65,536                                     | Origin server port                                           |
| distributions[0].origins[0].originPath | String  | Optional |         | Up to 8192 characters                        | Lower paths of origin server (path must include /.)          |
| distributions[0].callback              | Object  | Optional |         |                                              | Callback URL to receive processing result of CDN creation (callback setting is optional.) |
| distributions[0].callback.httpMethod   | String  | Required |         | GET/POST/PUT                                 | HTTP method of callback                                      |
| distributions[0].callback.url          | String  | Required |         | Up to 1024 characters                        | Callback URL                                                 |




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


[Field]

| Field                                  | Type    | Description                                                  |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | Header area                                                  |
| header.isSuccessful                    | Boolean | Successful or not                                            |
| header.resultCode                      | Integer | Result code                                                  |
| header.resultMessage                   | String  | Result message                                               |
| distributions                          | List    | List of created CDN objects                                  |
| distributions[0].domain                | String  | Created domain (service) name                                |
| distributions[0].domainAlias           | String  | Owned domain                                                 |
| distributions[0].region                | String  | Service region  ("LOCAL": Korea, "GLOBAL": Global)           |
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


[Field]

| Field                                  | Type    | Description                                                  |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | Header area                                                  |
| header.isSuccessful                    | Boolean | Successful or not                                            |
| header.resultCode                      | Integer | Result code                                                  |
| header.resultMessage                   | String  | Result message                                               |
| distributions                          | List    | List of created CDN objects                                  |
| distributions[0].domain                | String  | Domain (service) name                                        |
| distributions[0].domainAlias           | String  | Owned domain                                                 |
| distributions[0].region                | String  | Service region ("LOCAL": Korea, "GLOBAL": Global)            |
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
| useOrigin             | String  | Required |         | Y/N                                                          | Cache expiration setting (Y: Original setting, "N": User-configured) |
| referrerType          | String  | Required |         | BLACKLIST/WHITELIST                                          | Referrer access management ("BLACKLIST": Blacklist, "WHITELIST": Whitelist) |
| description           | String  | Optional |         | Up to 255 characters                                         | Description                                                  |
| domainAlias           | String  | Optional |         | Up to 255 characters                                         | Domain alias (Personal or company-owned domains; delimit by \n tokens.) |
| maxAge                | Integer | Optional | 0       | 0 ~ 2,147,483,647                                            | Cache expiration time (second); default 0 refers to 604,800 seconds. |
| referrers             | String  | Optional |         | Up to 1024 characters, including '\n\' tokens                | Referrers (delimit by \n tokens. )                           |
| origins               | List    | Required |         |                                                              | Origin server                                                |
| origins[0].origin     | String  | Required |         | Up to 255 characters                                         | Origin server (domain or IP)                                 |
| origins[0].port       | Integer | Required |         | 0~65,536                                                     | Origin server port                                           |
| origins[0].originPath | String  | Optional |         | Up to 8192 characters                                        | Lower paths of origin server                                 |
| callback              | Object  | Optional |         | Callback URL to receive CDN service deployment results (callback setting is optional.) |                                                              |
| callback.httpMethod   | String  | Required |         | GET/POST/PUT                                                 | HTTP method of callback                                      |
| callback.url          | String  | Required |         | Up to 1024 characters                                        | Callback URL                                                 |


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
| useOrigin             | String  | Optional |         | Y/N                                                          | Cache expiration setting (Y: Original setting, N: User-configured) |
| referrerType          | String  | Optional |         | BLACKLIST / WHITELIST                                        | Referrer access management ("BLACKLIST": Blacklist, "WHITELIST": Whitelist) |
| description           | String  | Optional |         | Up to 255 characters                                         | Description                                                  |
| domainAlias           | String  | Optional |         | Up to 255 characters                                         | Domain alias (personal or company-owned domains; delimit by \n tokens.) |
| maxAge                | Integer | Optional | 0       | 0~2,147,483,647                                              | Cache expiration time (second); default 0 refers to 604,800 seconds. |
| referrers             | String  | Optional |         | Up to 1024 characters, including '\n' tokens                 | Referrers (delimit by \n tokens. )                           |
| origins               | List    | Optional |         |                                                              | Origin server                                                |
| origins[0].origin     | String  | Optional |         | Up to 255 characters                                         | Origin server (domain or IP)                                 |
| origins[0].port       | Integer | Optional |         | 0 ~ 65,536                                                   | Origin server port                                           |
| origins[0].originPath | String  | Optional |         | Up to 8192 characters                                        | Lower paths of origin server                                 |
| callback              | Object  | Optional |         | Callback URL to receive CDN service deployment results (callback setting is optional.) |                                                              |
| callback.httpMethod   | String  | Optional |         | GET/POST/PUT                                                 | HTTP method of callback                                      |
| callback.url          | String  | Optional |         | Up to 1024 characters                                        | Callback URL                                                 |

- To set origins field, origin, port, and originPath are required field values.
- To set callback field, httpMethod and url are required field values.

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
