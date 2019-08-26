## Content Delivery > CDN > Console Guide

This document describes how to configure and use CDN service on a console.

## Order of Creating CDN Service   

From **Content Delivery > CDN**, click **Create** on **CDN Service** to display a window for **Create CDN Service**.  
Domain names for CDN service are automatically created. If you own a domain, it can be serviced by using domain alias.  

### CDN Service

![cdn_01_201812](https://static.toastoven.net/prod_cdn/cdn_01_201812.png)

- **Service Region**
  Select a service region.
  Choose KOREA or GLOBAL, depending on the service type. GLOBAL allows global service via cache servers in each country, while KOREA provides service within Korea only, at more affordable prices.    

- **Description**
  Enter additional description for CDN service.

- **Origin Server**
  Serviceable on TOAST instances or your existing servers. Available in IP or domain format, and ports can be specified.  
- **Origin Path**
  Enter lower paths in the format of URL to the origin path, with '/' included. "Origin Path" is optional.  

- **Domain Alias**
  Domain alias can be configured.
  When CDN service is created, service domain in the form of *.cdn.toastcloud.com is automatically issued, which can be applied to service.
  However, to provide service via personal or company-owned domain, you may set **domain alias**.  
  With **domain alias**, CDN service can be accessed even by personal or company-owned domains, as well as TOAST domains.
  Enter address of your own domain in **domain alias**, and change its name server setting.
  Add CDN service address issued by TOAST as CNAME record, and the service becomes available on your own domain.

> [Caution]
> When CDN service is created, service domain is issued as `*.cdn.toastcloud.com`.
> If you cannot access resources via`*.cdn.toastcloud.com` , check the following:
>
>
> 1. See if service status is 'green (in service)'.
> 2. Check if the origin server allows access of particular hosts only.
> 3. When user requests resources on a CDN service domain, the `Host` request header is configured as `*.cdn.toastcloud.com` and is requested to the origin server.
> Therefore, if origin server does not allow access to the `*.cdn.toastcloud.com` host, CDN cache server cannot import resources from the origin server.  
> In this case, the `*.cdn.toastcloud.com` host must be registered in the virtual host setting at the origin server.

* Example of Domain Alias Use

After CDN service is created, random-exam.cdn.toastcloud.com is issued. Following is an example of service configuration by using alias.nhnentcustomer.com, which is possessed by customer.

    1. Check if random-exam.cdn.toastcloud.com,issued automatically after TOAST CDN creation,is available.
    2. Enter alias.nhnentcustomer.com, which is the customer domain for service, at Domain Alias of Basic Information Settings.
    3. Take random-exam.cdn.toastcloud.com to add CNAME record at the nhnentcustomer.com, in the name server management section. (Each domain provider may provide different configuration methods. For more details, ask your domain provider.)
    4. Now, get ready to start with alias.nhnentcustomer.com.

### Cache

Cache expiration can be configured in Cache.
![cdn_02_201812](https://static.toastoven.net/prod_cdn/cdn_02_201812.png)

- **Cache Expiration Setting**
Specify cache expiration time as needed. **Original Setting** is the default.

  * Enable Original Setting: Apply cache expiration of the origin server.
  * Enable User-Defined Setting: Set cache expiration time as you need.

- **Cache Expiration Time (second)**
To specify cache expiration time, click **User-Configured** and change cache expiration time in **Cache Expiration Time (second)**.

> [Caution]
> If the origin server setting includes cache expiration time, the **Cache Expiration Time (second)** configured on console is ignored.
> To specify expiration time by using the CDN service, remove the cache expiration setting at the origin server.
>
> [Note]
> Default for cache expiration time is 0. With 0 as default, the cache expiration time is 604,800 (seconds).  Cache expiration time allows from 0 as default, to 2,147,483,647 (seconds).

- **Referrer Header Access Management **
  Referrer access management can be set.
  When a web page requests for resources with user content linkage, the linked URL path is delivered at the referrer header, so as to know the path where it has been requested.
  Referrer access management can be configured to allow only particular referrers to access user content, in reference of referrer header.  
  Referrers can be entered in regular expression, and add more lines to control many referrers.
  * Blacklist Type:
      * Suitable to restrict access from specific referrers.
      * If a referrer at the request header is a matching character string with configured regular expression, access to content is restricted: if it is not matching, access is allowed.
  * Whitelist Type:
      * Suitable to allow access from specific referrers.
      * If a referrer at the request header is a matching character string with configured regular expression, access to content is allowed: if it is not matching, access is restricted.

> [Caution]
> When the request header does not include a referrer, the access control by referrer setting shall not operate.
>
> [Example]
> * Type: Whitelist
> * Regular Expression: `^https://[a-zA-Z0-9._-]*\.toast\.com/.*`
> Access to content is allowed only when resources are requested from a lower path of a random toast.com sub domain.
>
> [Note]
> Some characters are used as special characters in regular expression.
> To take period(`.`) as an example, period (`.`) serves as a special character representing that it matches all characters, in regular expression.  
> (e.g: `\.`) To interpret a special character as a general character, add the backslash (`\`) escape character before it.
> Special characters in the regular expression are: `^ . [ ] $ ( ) | * + ? { } \`
> To control many referrers, enter them consecutively in the proceeding lines.
> To set many referrers via API, delimit by \n tokens.


### Callbacks

Changing CDN service (create, modify, suspend/resume, or delete) may require up to tens of minutes, after requested.
After service change is completed, its completion status and service information can be delivered via pre-defined callback URL.

![cdn_03_201812](https://static.toastoven.net/prod_cdn/cdn_03_201812.png)


1. Enter **HTTP Method** and **Callback URL**.
2. To receive results on CDN service change on query parameter of request URI, include path variables to **Callback URL**.

| Path Variable | Description | Example of Delivered Values |
| ------------- | --- | ------- |
| {appKey} | Appkey for CDN service | Appkye issued on console |
| {domain} | Name of CDN service | xxxxxx.cdn.toastcloud.com |
| {status} | Current status of CDN service | OPEN, SUSPEND, CLOSE, or ERROR |
| {isSuccessful} | If service change is successful (API v1.0 is not supported.) | "True" or "False" |
3. Click **OK** and the request for creating CDN service is completed.

> Example
> GET http://test.callback.com?appKey={appKey}&domain={domain}&status={status}&deploySuccess={isSuccessful}

3. 콜백 전달시 CDN 서비스의 정보를 요청 본문(Request Body)로 전달합니다. 

API V1.0을 통해 변경할 경우 요청 본문의 내용은 아래와 같습니다. 
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



CDN 콘솔을 통해 변경을 하거나 API V1.5 을 통해 변경할 경우 응답 형식은 아래와 같습니다. 
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


> [주의]
> API V1.0과 V1.5 버전에 따라 콜백 동작이 다르므로 유의해주세요.
> API V1.0은 CDN 서비스 생성과 수정시에만 콜백이 호출되고, API V1.5는 생성,수정,일시정지와 재개,삭제시 콜백을 호출합니다. 
> API 버전에 따라 콜백의 요청 본문(Request Body)의 json 데이터 형식이 다르므로 유의해주세요.


## Cache 재배포

원본 콘텐츠의 내용이 변경된 경우 기존에 지정된 캐시 만료 시간 이후에는 새로운 콘텐츠로 캐시가 업데이트 됩니다. 하지만 빠르게 캐시 내용을 재배포 하고 싶은 경우 "Cache 재배포" 기능을 이용해 기존 캐시를 새로운 콘텐츠로 업데이트 합니다.

1.변경을 원하는 서비스를 CDN 서비스 목록에서 선택합니다.  
2.[그림 4]와 같이 [Cache 재배포] 탭을 클릭합니다.  

![[그림 4] Cache 재배포](http://static.toastoven.net/prod_cdn/img_04.png)
<center>[그림 4] Cache 재배포</center>

3.캐시 재배포 타입을 선택합니다. 3가지 타입의 캐시 재배포 방식을 제공합니다.  

* Item: 정확한 파일명과 경로 설정을 통해 원하는 파일만 재배포 가능합니다.  
	* 예) /path/to/file1.jpg
* Wildcard: 파일명과 경로명에 와일드카드 문자를 이용가능합니다.  
	* \* : 임의의 문자열  
	* ? : 1개의 문자  
	* \\ : Escape 문자  
		* 예) /images/games/\\*.jpg  
		* /\\*/sports/\\*.jpg  
		* /images/sports/ac?e/\\*.jpg
* All: 모든 캐시를 한꺼번에 재배포 합니다.  

4.선택한 캐시 재배포 타입에 맞게 재배포할 파일을 지정합니다.  

5.[Cache 재배포] 버튼을 클릭해 재배포 요청을 합니다.  
재배포까지는 수 분의 시간이 소요됩니다. (용량에 따라 소요 시간은 달라질 수 있습니다.)

> [주의] 캐시 재배포 사용량 제한    
> 서비스별로 캐시 재배포 사용 횟수가 제한되므로 제한 사용량을 초과한 경우 사용량이 초기화된 이후에 다시 사용할 수 있습니다.   
>   - ITEM 타입: 시간당 60회 제한, 한 번에 요청할 수 있는 최대 Path 수: 1000개 제한  
>   - Wildcard 타입: 시간당 60회 제한, 한 번에 요청할 수 있는 최대 Path : 10개 제한  
>   - ALL 타입: 시간당 5회 제한  


## 감시설정

예상치 못한 트래픽이 발생 할 경우를 대비하여, 감시 설정을 등록할 수 있습니다. 지정된 값 이상의 트래픽이 발생할 경우 이메일을 발송하며, 강제 정지 옵션을 설정하면 이메일 발송 후 CDN 서비스를 정지합니다.  

1.변경을 원하는 서비스를 CDN 서비스 목록에서 선택합니다.  
2.[그림 5]의 [감시 설정] 탭에 있는 [수정] 버튼을 클릭합니다.  

![[그림 5] 감시 설정](http://static.toastoven.net/prod_cdn/img_007.png)
<center>[그림 5] 감시 설정</center>

3.누적 트래픽 타입으로 제한 할 트래픽 양을 지정합니다. 단위는 Byte입니다.  
4.+/- 버튼을 이용해 여러 개의 감시설정을 추가 및 삭제합니다.  
5.지정한 값 이상의 트래픽이 감지 되었을 경우에 서비스 강제 정지를 원하면 강제 정지 설정을 [예]로 활성화합니다.  
6.[확인] 버튼을 눌러 변경된 내용을 적용합니다.  

## 통계 확인하기

네트워크 전송량, HTTP 상태코드 별 통계 및 Top 콘텐츠에 대한 통계를 확인할 수 있습니다. 

1.[Contents Delivery] > [CDN] 의 [통계] 탭을 클립합니다.

![[그림 6] CDN 통계 조회](http://static.toastoven.net/prod_cdn/img_006.png)
<center>[그림 6] CDN 통계 조회</center>

2.통계를 확인하기 원하는 CDN 서비스를 선택합니다.  
3.검색 기간을 입력합니다.  
4.검색 기간내 데이터 주기는 선택한 기간에 따라 자동으로 선택됩니다.  
5.[검색] 버튼을 클릭합니다.  

## Domain Alias 사용 예

CDN 서비스 생성 후 random-exam.cdn.toastcloud.com 이라는 도메인이 발급되고, 기존 고객이 소유하던 alias.nhnentcustomer.com을 이용하여 서비스 하기 위한 설정 방법입니다.

You can find statistics on the network transfer volume, each HTTP status code, and the list of most downloaded content.
1. Go to **Content Delivery > CDN** and click **Statistics**.
    ![cdn_08_201812](https://static.toastoven.net/prod_cdn/cdn_08_201812.png)
2. Select CDN service to check statistics.
3. Enter search periods.
4. Data cycle within a search period is automatically selected depending on the period.
5. Click **Search**.
