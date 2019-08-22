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
>
> - To deliver callbacks, include information of CDN service in the request body.   
- To change to API v1.0, the request body should be as follows:
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
* To change on CDN console or to API v1.5, here is the response format:  
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
> [Caution]
> Callback operates differently for each version of API V1.0 and V1.5.  
> For API V1.0, callback is called only when service is created or modified; for API v1.5, callback is called to create, modify, suspend, resume, or delete.
> Note that each API version provides different JSON data format in the callback request body.   


**It takes minutes (no more than 1 hour) for a service to be completely deployed after it is requested to create. Service is enabled after deployment is completed. **

## Settings

### Change Settings for CDN Service  

Further description and origin server information can be changed.
Nevertheless, service name and region cannot be changed; hence, to change them, delete the existing service and create a new service.

1. Select a service to change on the list of CDN Service.
2. Click **Modify** on **Basic Information** at the bottom of the page.

Then, available items are enabled as below:

![cdn_04_201812](https://static.toastoven.net/prod_cdn/cdn_04_201812.png)

* Available items to change are Description, Origin Server Information, Domain Alias, and Callback.
* Click **OK** to complete changes.

**When the origin server is changed, all cached content are purged; purging time depends on the capacity of content.**

### Change Settings for CDN Cache

1. Select a service to change on the list of CDN Service.
2. Click **Modify** on **Cache Setting**.

Then, available items are enabled as below:

![cdn_05_201812](https://static.toastoven.net/prod_cdn/cdn_05_201812.png)

* Available items to change are Cache Expiration Setting, Cache Expiration Time, Referrer Header Access Management, and Callback.
* Click **OK** to complete changes.

### Purge CDN Cache

When the original content changes, cache is updated to new content after cache expiration time. However, to purge cache fast, apply **Purge Cache** to update existing caches to new content.

1. Select a service to change on the list of CDN Service.
2. Click **Purge Cache**.
    ![cdn_06_201812](https://static.toastoven.net/prod_cdn/cdn_06_201812.png)
3. Select a purge type, among three.  
  * Particular Files: Specify file names and paths so as to purge files that are wanted only.
      * e.g.) /path/to/file1.jpg
  * Wildcard:  Wildcard characters are available in the file or path name.
      * \*: Random character strings
      * ?: 1 character
      * \: Escape character
          * e.g.) /images/games/\*.jpg
          * /\_/sports/\_.jpg
          * /images/sports/ac?e/\*.jpg
  * All Files: Purge all caches all at once.
4. Specify a file to purge, in the selected purge type.
5. Click **Purge Cache** to request for purge.

It takes a few minutes to purge: may vary depending on the capacity.

> [Caution] Usage Limit of Purge Cache     
> Since each service limits the usage volume of purge cache, when restricted amount is exceeded, the usage volume is returned to default, and then, the service is resumed.   
>
>
> * Particular File Type: Restricted to 60 times per hour, with no more than 1000 paths at one request
> * Wildcard Type: Restricted to 60 times per hour, with no more than 10 paths at one request
> * All-File Type: Restricted to 5 times per hour  

### CDN Surveillance Setting

To prepare against unexpected traffic, surveillance setting can be registered. When traffic occurs more than specified value, email is sent; with forced-stop option, CDN service is suspended after email delivery.

1. Select a service to change on the list of CDN Service.
2. Click **Modify** on **Surveillance Setting**.
![cdn_07_201812](https://static.toastoven.net/prod_cdn/cdn_07_201812.png)
  * Specify traffic volume to restrict in the accumulated traffic type, in bytes.
  * Click + or -, to add or delete a number of surveillance settings.
  * To force stop a service in which more traffic than specified is detected, enable **Yes** for Forced Stop.
3. Click **OK** to apply changes.

## Statistics

You can find statistics on the network transfer volume, each HTTP status code, and the list of most downloaded content.
1. Go to **Content Delivery > CDN** and click **Statistics**.
    ![cdn_08_201812](https://static.toastoven.net/prod_cdn/cdn_08_201812.png)
2. Select CDN service to check statistics.
3. Enter search periods.
4. Data cycle within a search period is automatically selected depending on the period.
5. Click **Search**.
