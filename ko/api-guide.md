## Contents Delivery > CDN > API 가이드

TOAST CDN에서 제공하는 Public API를 설명합니다.

## API 공통 정보

### 사전 준비

API를 사용하기 위해서는 앱 키(Appkey)와 보안 키(SecretKey) 필요합니다. 앱 키와 보안 키는 [그림 1]과 같이 웹 콘솔 상단의 [URL&Appkey]을 클릭하여 확인할 수 있습니다.

![[그림 1] 앱 키 및 보안 키 확인](http://static.toastoven.net/prod_cdn/img_08.png)
<center>[그림 1] 앱 키 및 보안 키 확인</center>

### 요청 공통 정보

#### 요청 헤더

| 이름 | 설명 |
| --- | --- |
| Authorization | 콘솔에서 확인한 보안 키 (SecretKey) |

#### Path 파라미터

모든 API는 appKey를 path 파라미터를 지정하도록 되어 있습니다.
* 예) api-gw.cloud.toast.com/tc-cdn/v1.0/appKeys/**{appKey}**/distributions

| 이름 | 설명 |
| --- | --- |
| appKey | 콘솔에서 확인한 앱 키 (Appkey) | 

### 응답 공통 정보

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

#### 필드

| 필드 |	타입 | 설명|
| --- | --- | --- |
| header | Object |	헤더 영역|
| header.isSuccessful |	Boolean| 성공 여부 |
| header.resultCode | Integer | 결과 코드 |
| header.resultMessage | String | 결과 메시지 |

## CDN 생성

### 요청

#### URI

| 메서드 | URI |
| --- | --- |
| POST | /v1.0/appKeys/{appKey}/distributions |

#### 요청 본문

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
			]
		}
	]
}
```

#### 필드

| 이름 | 타입 | 필수 여부 | 기본값 | 유효 범위 | 설명 |
| --- | --- | --- | --- | --- | --- |
| distributions | List | O | | | 생성할 CDN의 오브젝트 리스트 |
| distributions[0].region | String | O | | | 서비스 지역 ("LOCAL": 대한민국, "GLOBAL" : 글로벌) |
| distributions[0].useOrigin | String | O | | | Cache 만료 설정 ("Y": 원본 설정 사용, "N":사용자 설정 사용) |
| distributions[0].referrerType | String | O | | | Referrers 접근 관리 ("BLACKLIST": 블랙 리스트, "WHITELIST": 화이트 리스트) |
| distributions[0].description | String | X | | | 설명 |
| distributions[0].domainAlias | String | X | | | Domain alias (개인 혹은 회사가 소유한 도메인 사용) |
| distributions[0].maxAge | Integer | X | 0 | | Cache 만료 시간(초) |
| distributions[0].referrers | String | X | | | Referrers (여러 개 입력시 \n 토큰으로 분리하여 입력해주세요.) |
| distributions[0].origins | List | O | | | 원본 서버 오브젝트 리스트 |
| distributions[0].origins[0].origin | String | O | | | 원본서버 (domain or ip) |
| distributions[0].origins[0].port | String | O | | | 원본서버 포트 |
| distributions[0].origins[0].originPath | String | X | | | 원본서버 하위 경로 (/를 포함한 경로로 입력해주세요.) |

### 응답

#### 응답 본문

```json
{
    "header": {
        "resultCode": Integer,
        "resultMessage": String,
        "isSuccessful": Boolean
    },
    "distributions": [
        {
            "domain": String,
            "domainAlias": String,
            "region": String,
            "description": String,
            "status": String,
            "createTime": Long,
            "useOrigin": String,
            "maxAge": String,
            "referrerType": String,
            "referrers": String,
            "origins": [
                {
                    "origin": String,
                    "originPath": String,
                    "port": Integer
                }
            ]
        }
    ]
}
```

#### 필드

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
| distributions[0].origins[0].originPath | String|	원본서버 하위 경로 (/를 포함한 하위 path를 입력해주세요.) |
| distributions[0].origins[0].port | Integer| 원본서버 포트|

## CDN 조회

### 요청

#### URI

| 메서드 | URI |
| --- | --- |
| GET | /v1.0/appKeys/{appKey}/distributions |

#### 파라미터

| 이름 | 타입 | 필수 여부 | 유효 범위 | 설명 |
| --- | --- | --- | --- | --- |
| domain | String | O | - | 조회할 도메인(서비스 이름) |

### 응답

#### 응답 본문

```json
{
    "header": {
        "resultCode": Integer,
        "resultMessage": String,
        "isSuccessful": Boolean
    },
    "distributions": [
        {
            "domain": String,
            "domainAlias": String,
            "region": String,
            "description": String,
            "status": String,
            "createTime": Long,
            "useOrigin": String,
            "maxAge": String,
            "referrerType": String,
            "referrers": String,
            "origins": [
                {
                    "origin": String,
                    "originPath": String,
                    "port": Integer,
                }
            ]
        }
    ]
}
```

#### 필드

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
| distributions[0]. description|  String| 설명 |
| distributions[0].status| String| CDN 상태 ([표] CDN 상태 참고) |
| distributions[0].createTime|  String| 생성일시 |
| distributions[0].useOrigin| String| 원본 서버 설정 사용 여부("Y": 원본서버 설정 사용, "ㅜN": 사용자 설정) |
| distributions[0].maxAge| String| Cache 만료시간(초) |
| distributions[0].referrerType|  String| Referrers 접근 관리 ("BLACKLIST": 블랙 리스트, "WHITELIST": 화이트 리스트) |
| distributions[0].referrers| String| referrers 리스트 |
| distributions[0].origins|  List| 원본 서버 오브젝트 리스트 |
| distributions[0].origins[0].origin|	String|	원본 서버(domain or ip) |
| distributions[0].origins[0].originPath|	String|	원본서버 하위 경로 |
| distributions[0].origins[0].port|	Integer| 원본서버 포트|

## CDN 수정

### 요청

#### URI

| 메서드 | URI |
| --- | --- |
| PUT | /v1.0/appKeys/{appKey}/distributions |

#### 요청 본문

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
		]
}
```

#### 필드

| 이름 | 타입 | 필수 여부 | 기본값 | 유효 범위 | 설명 |
| --- | --- | --- | --- | --- | --- |
| domain | String | O | - | | 수정할 도메인(서비스 이름) |
|- useOrigin|	String|	O | - | | Cache 만료 설정 ("Y": 원본 설정 사용, "N":사용자 설정 사용) |
|- referrerType|	String|	O| - | |	Referrers 접근 관리 ("BLACKLIST": 블랙 리스트, "WHITELIST": 화이트 리스트) |
|- description|	String|	X| - | | 설명|
|- domainAlias|	String|	X |- | | Domain alias (개인 혹은 회사가 소유한 도메인 사용)
|- maxAge|	Integer|	X| 0 | | Cache 만료 시간(초) |
|- referrers|	String|	X | - | |	Referrers (여러 개 입력시 \\n 토큰으로 분리하여 입력해주세요. )|
|- origins|	List|	O |-|	|원본서버|
|-- origin|	String|	O | -|	|원본서버 (domain or ip)|
|-- port|	Integer|	O | -|	|원본서버 포트|
|-- originPath|	String|	X | -| |	원본서버 하위 경로 |

### 응답

#### 응답 본문

```json
{
    "header": {
        "resultCode": Integer,
        "resultMessage": String,
        "isSuccessful": Boolean
    }
}
```

#### 필드

| 필드 |	타입 | 설명|
| --- | --- | --- |
| header | Object |	헤더 영역|
| header.isSuccessful |	Boolean| 성공 여부 |
| header.resultCode | Integer | 결과 코드 |
| header.resultMessage | String | 결과 메시지 |

## 캐시 재배포(Purge)

### 요청

#### URI

| 메서드 | URI |
| --- | --- |
|POST|	/v1.0/appKeys/{appKey}/purges|

#### 요청 본문

```json
{
	"domain": "sample.cdn.toastcloud.com",
	"purgeType": "ITEM",
	"purgeList":"/img_01.png"
}
```

#### 필드

| 이름 | 타입 | 필수 여부 | 기본값 | 유효 범위 | 설명 |
| --- | --- | --- | --- | --- | --- |
|domain|	String|	O| - | | Purge할 도메인(서비스) 이름 |
|purgeType| List | O | - | | Purge Type("ITEM", "WILDCARD", "ALL") |
|purgeList|	String|	X| -| | purge 항목 리스트 (여러 개를 입력할 경우 \\n 토큰으로 분리하여 입력해주세요, purgeType이 ALL인 경우 입력하지 않아도됩니다.) |

### 응답

#### 응답 본문

```json
{
    "header": {
        "resultCode": Integer,
        "resultMessage": String,
        "isSuccessful": Boolean
    },
    "purgeSeq" : Integer
}
```

#### 필드

| 필드 |	타입 | 설명|
| --- | --- | --- |
| header | Object |	헤더 영역|
| header.isSuccessful |	Boolean| 성공 여부 |
| header.resultCode | Integer | 결과 코드 |
| header.resultMessage | String | 결과 메시지 |
| purgeSeq | String | Purge 요청 번호 |

## 캐시 재배포(Purge) 조회

### 요청

#### URI

| 메서드 | URI |
| --- | --- |
|GET|	/v1.0/appKeys/{appKey}/purges|

#### 파라미터

| 이름 | 타입 | 필수 여부 | 기본값 | 유효 범위 | 설명 |
| --- | --- | --- | --- | --- | --- |
|domain|	String|	O| - | | Purge할 도메인(서비스) 이름 |

### 응답

#### 응답 본문

```json
{
    "header": {
        "resultCode": Integer,
        "resultMessage": String,
        "isSuccessful": Boolean
    },
    "totalItems": Integer,
    "purges": [
        {
            "seq": Integer,
            "progress": Integer,
            "purgeTime": Long,
            "lastCheckTime": Long,
            "type": String,
            "path": String
        }
    ]
}
```

#### 필드

| 필드 |	타입 | 설명|
| --- | --- | --- |
| header | Object |	헤더 영역|
| header.isSuccessful |	Boolean| 성공 여부 |
| header.resultCode | Integer | 결과 코드 |
| header.resultMessage | String | 결과 메시지 |
| totalItems | Integer | purge 총 개수 |
| purges |	List|	purge 리스트|
| purges[0].seq|	Integer|	purge 요청 번호 |
| purges[0].progress|	Integer|	purge 진행률|
| purges[0].purgeTime|	Long|	purge 요청 시간 |
| purges[0].lastCheckTime|	Long|	purge 수행 마지막 체크 시간 |
| purges[0].type|	String|	Purge Type("ITEM", "WILDCARD", "ALL") |
| purges[0].path|	String| purge 요청 항목 |


## CDN 코드

### CDN 상태 코드

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

[표1] CDN 상태 코드
