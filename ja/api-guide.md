## Contents Delivery > CDN > API Guide

TOAST CDN에서 제공하는 Public API를 설명합니다.

## API 공통 정보

### 도메인

| 이름 | 도메인 |
| --- | --- |
| CDN Public API 도메인 | https://api-gw.cloud.toast.com/tc-cdn |

### 사전 준비

API를 사용하기 위해서는 앱 키(Appkey)와 보안 키(SecretKey)가 필요합니다. 
앱 키와 보안 키는 콘솔 상단 "URL & Appkey" 메뉴에서 확인이 가능합니다.

### 요청 공통 정보

#### 요청 헤더

| 이름 | 설명 |
| --- | --- |
| Authorization | 콘솔에서 발급받은 보안 키 (SecretKey) |

#### Path 파라미터

모든 API는 appKey를 path 파라미터로 지정하도록 되어 있습니다.
* 예) /v1.0/appKeys/**{appKey}**/distributions

| 이름 | 설명 |
| --- | --- |
| appKey | 콘솔에서 발급받은 앱 키 (Appkey) | 

### 응답 공통 정보

#### 헤더

모든 API 요청에 대해서 **200 OK** 로 응답합니다. 자세한 응답 결과는 다음의 예와 같이 응답 본문의 헤더를 참고합니다.

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Success"
    }
}
```


[필드]

| 필드 |	타입 | 설명|
| --- | --- | --- |
| header | Object |	헤더 영역|
| header.isSuccessful |	Boolean| 성공 여부 |
| header.resultCode | Integer | 결과 코드 |
| header.resultMessage | String | 결과 메시지 |

#### CDN 상태 코드

다음은 CDN 서비스 상태를 나타내는 상태 코드로, 서비스 조회시 서비스 상태를 확인할 수 있습니다.

|값|설명|
|---|---|
|OPENING|서비스 시작 중|
|OPEN | 서비스 중|
|MODIFYING| 수정중|
|RESUME | 시작|
|SUSPENDING | 정지 진행중|
|SUSPEND | 정지|
|CLOSING| 사용 종료중|
|CLOSE| 사용 종료|

## 서비스 API

### 서비스 생성

#### 요청


[URI]

| 메서드 | URI |
| --- | --- |
| POST | /v1.0/appKeys/{appKey}/distributions |


[요청 본문]

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


[필드]

| 이름 | 타입 | 필수 여부 | 기본값 | 유효 범위 | 설명 |
| --- | --- | --- | --- | --- | --- |
| distributions | List | 필수 | | | 생성할 CDN의 오브젝트 리스트 |
| distributions[0].region | String | 필수 | | LOCAL / GLOBAL | 서비스 지역 ("LOCAL": 대한민국, "GLOBAL" : 글로벌) |
| distributions[0].useOrigin | String | 필수 | | Y / N | Cache 만료 설정 ("Y": 원본 설정 사용, "N":사용자 설정 사용) |
| distributions[0].referrerType | String | 필수 | | BLACKLIST / WHITELIST | Referrers 접근 관리 ("BLACKLIST": 블랙 리스트, "WHITELIST": 화이트 리스트) |
| distributions[0].description | String | 선택 | | 최대 255자 | 설명 |
| distributions[0].domainAlias | String | 선택 | | 최대 255자 | Domain alias (개인 혹은 회사가 소유한 도메인 사용) |
| distributions[0].maxAge | Integer | 선택 | 0 | 0 ~ 2,147,483,647 | Cache 만료 시간(초), 기본 값 0은 604,800초 입니다. |
| distributions[0].referrers | String | 선택 | | '\n' 토큰 포함, 최대 1024자 | Referrers (여러 개 입력시 \n 토큰으로 분리하여 입력해 주세요.) |
| distributions[0].origins | List | 필수 | | | 원본 서버 오브젝트 리스트 |
| distributions[0].origins[0].origin | String | 필수 | | 최대 255자 | 원본 서버 (domain or ip) |
| distributions[0].origins[0].port | String | 필수 | | 0 ~ 65,536 | 원본 서버 포트 |
| distributions[0].origins[0].originPath | String | 선택 | | 최대 8192자 | 원본 서버 하위 경로 (/를 포함한 경로로 입력해 주세요.) |
| distributions[0].callback | Object | 선택 | | | CDN 생성 처리 결과를 통보받을 콜백 URL (콜백 설정은 선택입력 입니다.) |
| distributions[0].callback.httpMethod | String | 필수 | | GET/POST/PUT | 콜백의 HTTP Method |
| distributions[0].callback.url | String | 필수 | | 최대 1024자 | 콜백 URL |




#### 응답


[응답 본문]

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


[필드]

| 필드 |	타입 | 설명|
| --- | --- | --- |
| header | Object |	헤더 영역|
| header.isSuccessful |	Boolean| 성공 여부 |
| header.resultCode | Integer | 결과 코드 |
| header.resultMessage | String | 결과 메시지 |
| distributions | List | 생성된 CDN 오브젝트 리스트|
| distributions[0].domain|	String|	생성된 도메인(서비스) 이름 |
| distributions[0].domainAlias|	String|	소유 도메인 |
| distributions[0].region|  String| 서비스 지역 ("LOCAL": 대한민국, "GLOBAL" : 글로벌)|
| distributions[0].description|  String| 설명 |
| distributions[0].status| String| CDN 상태 ([표] CDN 상태 참고) |
| distributions[0].createTime|  String| 생성일시 |
| distributions[0].useOrigin| String| 원본 서버 설정 사용 여부("Y": 원본서버 설정 사용, "N": 사용자 설정) |
| distributions[0].maxAge| String| Cache 만료시간(초) |
| distributions[0].referrerType|  String| Referrers 접근 관리 ("BLACKLIST": 블랙 리스트, "WHITELIST": 화이트 리스트) |
| distributions[0].referrers | String| referrers 리스트 |
| distributions[0].origins |  List| 원본 서버 오브젝트 리스트 |
| distributions[0].origins[0].origin | String|	원본 서버(domain or ip) |
| distributions[0].origins[0].originPath | String|	원본 서버 하위 경로 |
| distributions[0].origins[0].port | Integer| 원본 서버 포트 |
| distributions[0].callback | Object | 서비스 생성 처리 결과를 통보 받을 콜백 |
| distributions[0].callback.httpMethod | String | 콜백의 HTTP Method |
| distributions[0].callback.url | String | 콜백 URL |


### 서비스 조회

#### 요청


[URI]

| 메서드 | URI |
| --- | --- |
| GET | /v1.0/appKeys/{appKey}/distributions |


[파라미터]

| 이름 | 타입 | 필수 여부 | 유효 범위 | 설명 |
| --- | --- | --- | --- | --- |
| domain | String | 선택 | 최대 255자 | 조회할 도메인(서비스 이름) |
| status | String | 선택 | CDN 상태 코드 | CDN 상태 ([표] CDN 상태 참고) |

[예]
```
curl -X GET "https://api-gw.cloud.toast.com/tc-cdn/v1.0/appKeys/{appKey}/distributions?domain={domain}" \
 -H "Authorization: {secretKey}" \
 -H "Content-Type: application/json"
```

#### 응답


[응답 본문]

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


[필드]

| 필드 |	타입 | 설명|
| --- | --- | --- |
| header | Object |	헤더 영역|
| header.isSuccessful |	Boolean| 성공 여부 |
| header.resultCode | Integer | 결과 코드 |
| header.resultMessage | String | 결과 메시지 |
| distributions |	List |	생성된 CDN 오브젝트 리스트|
| distributions[0].domain|	String|	domain 이름(서비스 이름) |
| distributions[0].domainAlias|	String|	소유 도메인 |
| distributions[0].region|  String| 서비스 지역 ("LOCAL": 대한민국, "GLOBAL" : 글로벌)|
| distributions[0].description|  String| 설명 |
| distributions[0].status| String| CDN 상태 ([표] CDN 상태 참고) |
| distributions[0].createTime|  String| 생성일시 |
| distributions[0].useOrigin| String| 원본 서버 설정 사용 여부("Y": 원본서버 설정 사용, "ㅜN": 사용자 설정) |
| distributions[0].maxAge| String| Cache 만료시간(초) |
| distributions[0].referrerType|  String| Referrers 접근 관리 ("BLACKLIST": 블랙 리스트, "WHITELIST": 화이트 리스트) |
| distributions[0].referrers| String| referrers 리스트 |
| distributions[0].origins|  List| 원본 서버 오브젝트 리스트 |
| distributions[0].origins[0].origin|	String|	원본 서버(domain or ip) |
| distributions[0].origins[0].originPath|	String|	원본 서버 하위 경로 |
| distributions[0].origins[0].port|	Integer| 원본 서버 포트|
| distributions[0].callback | Object | 서비스 생성 처리결과를 통보 받을 콜백 |
| distributions[0].callback.httpMethod | String | 콜백의 HTTP Method |
| distributions[0].callback.url | String | 콜백 URL |


### 서비스 수정

#### 요청


[URI]

| 메서드 | URI |
| --- | --- |
| PUT | /v1.0/appKeys/{appKey}/distributions |


[요청 본문]

```json
{
		"domain" : "sample.cdn.toastcloud.com",
		"useOrigin" : "N",
		"referrerType" : "BLACKLIST",
		"description" : "change contents",
		"maxAge": 86400,
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
        }
}
```


[필드]

| 이름 | 타입 | 필수 여부 | 기본값 | 유효 범위 | 설명 |
| --- | --- | --- | --- | --- | --- |
| domain | String | 필수 | | 최대 255자 | 수정할 도메인(서비스 이름) |
| useOrigin|	String | 필수 | | Y / N | Cache 만료 설정 ("Y": 원본 설정 사용, "N":사용자 설정 사용) |
| referrerType|	String | 필수 | | BLACKLIST / WHITELIST |	Referrers 접근 관리 ("BLACKLIST": 블랙 리스트, "WHITELIST": 화이트 리스트) |
| description|	String | 선택 | | 최대 255자 | 설명|
| domainAlias|	String | 선택 | | 최대 255자 | Domain alias (개인 혹은 회사가 소유한 도메인 사용) |
| maxAge|	Integer |선택| 0 | 0 ~ 2,147,483,647 | Cache 만료 시간(초), 기본 값 0은 604,800초 입니다. |
| referrers|	String|	선택 | | '\n' 토큰 포함, 최대 1024자 |	Referrers (여러 개 입력시 \\n 토큰으로 분리하여 입력해주세요. )|
| origins|	List| 필수 | | |원본 서버|
| origins[0].origin| String| 필수 | | 최대 255자	|원본 서버 (domain or ip)|
| origins[0].port|	Integer| 필수 | |	0 ~ 65,536 |원본서 버 포트|
| origins[0].originPath|	String|	선택 | | 최대 8192자 | 원본 서버 하위 경로 |
| distributions[0].callback | Object | 선택 |  | CDN 생성 처리 결과를 통보받을 콜백 URL (콜백 설정은 선택입력 입니다.) |
| distributions[0].callback.httpMethod | String | 필수 | | GET/POST/PUT | 콜백의 HTTP Method |
| distributions[0].callback.url | String | 필수 | | 최대 1024자 | 콜백 URL |


#### 응답


[응답 본문]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    }
}
```


[필드]

| 필드 |	타입 | 설명|
| --- | --- | --- |
| header | Object |	헤더 영역|
| header.isSuccessful |	Boolean| 성공 여부 |
| header.resultCode | Integer | 결과 코드 |
| header.resultMessage | String | 결과 메시지 |

### 서비스 삭제

#### 요청


[URI]

| 메서드 | URI |
| --- | --- |
| DELETE | /v1.0/appKeys/{appKey}/distributions |


[요청 본문]

```json
{
    "domains" : [
        "lhcsxuo0.cdn.toastcloud.com"
    ]
}
```


[필드]

| 이름 | 타입 | 필수 여부 | 기본값 | 유효 범위 | 설명 |
| --- | --- | --- | --- | --- | --- |
| domains | String | 필수 | | | 삭제할 도메인, 여러 도메인 입력 가능 |

**\* 여러 도메인을 입력시, 해당하는 서비스들은 모두 종료처리 됩니다.**

#### 응답


[응답 본문]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    }
}
```


[필드]

| 필드 |	타입 | 설명|
| --- | --- | --- |
| header | Object |	헤더 영역|
| header.isSuccessful |	Boolean| 성공 여부 |
| header.resultCode | Integer | 결과 코드 |
| header.resultMessage | String | 결과 지시지 |

## 캐시 재배포 API

### 캐시 재배포(Purge)

#### 요청


[URI]

| 메서드 | URI |
| --- | --- |
|POST|	/v1.0/appKeys/{appKey}/purges|


[요청 본문]

```json
{
	"domain": "sample.cdn.toastcloud.com",
	"purgeType": "ITEM",
	"purgeList":"/img_01.png"
}
```


[필드]

| 이름 | 타입 | 필수 여부 | 기본값 | 유효 범위 | 설명 |
| --- | --- | --- | --- | --- | --- |
|domain|	String|	필수 |  | 최대 255자 | 재배포할 도메인(서비스) 이름 |
|purgeType| List | 필수 |  | ITEM / WILDCARD / ALL | 재배포 타입 ("ITEM", "WILDCARD", "ALL") |
|purgeList|	String|	선택 | | | 재배포 타겟 항목 리스트 (여러 개를 입력할 경우 \\n 토큰으로 분리하여 입력해 주세요, purgeType이 ALL인 경우 입력하지 않아도됩니다.) |

#### 응답


[응답 본문]

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


[필드]

| 필드 |	타입 | 설명|
| --- | --- | --- |
| header | Object |	헤더 영역|
| header.isSuccessful |	Boolean| 성공 여부 |
| header.resultCode | Integer | 결과 코드 |
| header.resultMessage | String | 결과 메시지 |
| purgeSeq | Integer | 재배포 요청 번호 |

### 캐시 재배포(Purge) 조회

#### 요청


[URI]

| 메서드 | URI |
| --- | --- |
|GET|	/v1.0/appKeys/{appKey}/purges|


[파라미터]

| 이름 | 타입 | 필수 여부 | 기본값 | 유효 범위 | 설명 |
| --- | --- | --- | --- | --- | --- |
| domain | String|	필수 |  | 최대 255자 | 도메인(서비스) 이름 |
| page | Integer | 선택 | | | 페이지 번호 |
| itemsPerPage | Integer | 선택 | | | 페이지당 재배포된 항목 개수 |
| startTime | String | 선택 | | "yyyy-MM-dd'T'HH:mm:ss.SSSZ" | 기간 검색 |
| endTime | String | 선택 | | "yyyy-MM-dd'T'HH:mm:ss.SSSZ" | 기간 검색  |


```
curl -X GET "https://api-gw.cloud.toast.com/tc-cdn/v1.0/appKeys/{appKey}/purges?domain={domain}" \
 -H "Authorization: {secretKey}" \
 -H "Content-Type: application/json"
```

#### 응답


[응답 본문]

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


[필드]

| 필드 |	타입 | 설명|
| --- | --- | --- |
| header | Object |	헤더 영역|
| header.isSuccessful |	Boolean| 성공 여부 |
| header.resultCode | Integer | 결과 코드 |
| header.resultMessage | String | 결과 메시지 |
| totalItems | Integer | 재배포한 총 개수 |
| purges |	List| 재배포 항목 리스트|
| purges[0].seq|	Integer| 재배포 요청 번호 |
| purges[0].progress|	Integer| 재배포 진행률|
| purges[0].purgeTime|	Long| 재배포 요청 시간 |
| purges[0].lastCheckTime|	Long| 재배포 수행 마지막 체크 시간 |
| purges[0].type|	String|	재배포 타입 ("ITEM", "WILDCARD", "ALL") |
| purges[0].path|	String| 재배포 요청 항목 |
