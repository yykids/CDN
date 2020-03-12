## 마이그레이션 가이드

### 개요 
2020년 3월 24일 TOAST 정기점검 이전에 생성한 **[서비스ID].cdn.toastcloud.com** 도메인의 CDN 서비스는 **2020년 5월 26일 10:00:00 KST까지** 서비스가 제공됩니다.
서비스 제공 기간 이후 **[서비스ID].cdn.toastcloud.com** 도메인의 CDN 서비스는 종료됩니다. 

**[서비스ID].cdn.toastcloud.com** 도메인의 CDN 서비스 종료 이후에는 아래의 작업을 할 수 없습니다.

  - CDN 서비스 도메인으로 콘텐츠 다운로드
  - 콘솔과 API를 통한 서비스 조작 

CDN 서비스를 계속 이용하려면 아래 가이드를 참고해 마이그레이션 작업을 진행하시기 바랍니다. 

## 마이그레이션 진행 순서

### 1. CDN 서비스 마이그레이션 대상 확인
1. TOAST CDN 콘솔 페이지의 **CDN 서비스** 탭으로 이동합니다. 
2. 서비스 도메인이 **[서비스ID].cdn.toastcloud.com**인 경우 마이그레이션이 필요한 CDN 서비스입니다.
    ![마이그레이션-대상목록](https://static.toastoven.net/prod_cdn/v2/migration-target-list.png)
3. 서비스 이름 옆의 **마이그레이션** 버튼을 클릭합니다. 
4. **CDN 서비스 생성** 대화 상자가 나타납니다. **CDN 서비스 생성** 대화 상자에는 기존 CDN 서비스 정보가 기본으로 설정돼 있습니다. 

### 2. 신규 CDN 서비스 설정 확인 후 서비스 생성

1. **CDN 서비스 생성** 대화 상자에서 CDN 서비스 설정 내용을 확인합니다.
    CDN 서비스 설정에 대한 자세한 정보는 [사용자 콘솔 가이드](./console-guide/#cdn)를 참고합니다.

    ![마이그레이션-서비스생성1](https://static.toastoven.net/prod_cdn/v2/migration-create-modal.png)
    ![마이그레이션-서비스생성2](https://static.toastoven.net/prod_cdn/v2/migration-create-modal-options.png)    

    * **서비스 지역** 
        - KOREA(한국) 서비스 지역을 지원하지 않습니다. 
        - 한국 지역을 포함하고 있는 GLOBAL 서비스 지역만 제공됩니다. 
        - **중국과 러시아** 국가 지역은 GLOBAL 서비스 지역에 포함되지 않습니다. 

    * **도메인 별칭** 
        - 도메인 별칭에 설정된 도메인으로 보안 전송(HTTPS)을 이용하려면, 먼저 인증서를 발급하시기 바랍니다.
            - CDN 서비스를 생성하기 전 **인증서 관리** 탭에서 **신규 인증서 발급** 버튼을 클릭해 먼저 인증서를 발급하시기 바랍니다. 인증서를 발급하지 않은 경우 HTTPS 프로토콜 서비스를 이용할 경우 인증서 오류가 발생합니다.
            - 인증서에 대한 자세한 내용은 [사용자 콘솔 가이드 > 인증서 관리](./console-guide/#_5)를 참고합니다.
        
    * **원본 서버**
        - 기존 CDN 서비스는 HTTP 프로토콜 전송만 지원했으나, 신규 CDN 서비스는 **[서비스ID].toastcdn.net** 서비스 도메인에 대해 HTTP/HTTPS 프로토콜 전송을 지원합니다.
        - **원본 서버 포트**는 원본 서버의 운영 중인 HTTP/HTTPS 포트 번호를 설정합니다. 설정하지 않으면 미설정으로 표시되며, 기본 포트(HTTP:80, HTTPS:443)로 설정됩니다.
            - 원본 서버는 허용된 포트만 사용할 수 있습니다. 허용된 포트는 [사용자 콘솔 가이드 > 원본 서버](./console-guide/#_2)의 **[표 2] 사용 가능한 원본 서버 포트 번호**를 참고하시기 바랍니다.
        - CDN 서비스 도메인(또는 도메인 별칭)으로 보안 전송(HTTPS)을 지원하려면 원본 서버는 HTTPS 응답을 지원해야 합니다.
            - CDN 에지(edge) 서버는 'man-in-the-middle(MITM)' 공격을 예방하기 위해 원본 서버의 인증서 유효 여부를 확인합니다. 
            - 원본 서버에는 TOAST CDN이 신뢰하는 인증서 또는 인증 기관(CA, certificate authority)의 인증서가 설치되어야 합니다.
            - TOAST CDN이 신뢰하는 인증서 또는 인증 기관은 [사용자 콘솔 가이드 > 원본 서버](./console-guide/#_2)의 **[표 1] 신뢰하는 인증서 목록**을 참고합니다.
        - 만약 원본 서버가 HTTPS 응답을 지원하기 어려운 경우, **원본 요청 HTTP 프로토콜 다운그레이드** 설정을 이용하시기 바랍니다. 
            - 단, **원본 요청 HTTP 프로토콜 다운그레이드** 설정은 제약 사항이 있으므로 해당 내용을 반드시 확인하시기 바랍니다. 

    * **원본 요청 HTTP 프로토콜 다운그레이드**
        - 원본 서버가 HTTP 응답만 지원 가능한 경우, 원본 요청 HTTP 프로토콜 다운그레이드 설정을 **사용**으로 설정합니다. 
        - 원본 요청 HTTP 프로토콜 다운그레이드를 **사용**으로 설정한 경우, CDN 에지(edge) 서버에서 원본 요청 시 HTTPS 프로토콜 요청은 HTTP 프로토콜로 다운그레이드하여 요청합니다. 
        - 단, 이 설정은 다음과 같은 제약 사항이 있으므로 주의하여 사용하시기 바랍니다. 
            * 전체 사이트 주소는 프로토콜 다운그레이드를 할 수 없습니다. 예를 들어 원본 서버의 전체 사이트 주소인 'www.toast.com'은 다운그레이드를 할 수 없습니다.
            * GET, HEAD 및 OPTIONS 메서드 외 메서드는 지원되지 않습니다. 
            * CDN 서버에서 원본 서버로 다운그레이드 요청 시 다음의 헤더는 제외될 수 있습니다.
                - Origin, Referer, Cookie, Cookie2, sec-\*, proxy-\*

    * **Forward Host Header** 
    CDN 서버가 원본 서버에 원본 파일을 요청할 때 전달할 **Host** 헤더값을 설정합니다. 
    원본 서버가 Name-based virtual host로 운영 중이라면 **요청 호스트 헤더** 설정이 필요할 수 있습니다. 원본 서버의 운영 형태에 따라 적합하게 설정하시기 바랍니다.
        - 원본 호스트 이름: 원본 서버의 호스트네임을 Host 헤더로 설정합니다. 
        - 요청 호스트 헤더: 클라이언트 요청의 Host 헤더로 설정합니다.

    * **캐시** 
    CDN 캐시 서버의 캐시 운영 방법과 만료 시간을 설정합니다. 
        - **원본 설정 사용**: 원본 서버의 응답에서 제공한 캐시 제어 헤더(Cache-Control, Expires)를 우선하여 적용합니다. 만일 원본 서버의 응답에 캐시 제어 헤더(Cache-Control, Expires)가 유효하지 않거나 없는 경우, 캐시 만료 시간(초)에 지정한 시간 동안 캐시합니다.  **원본 설정 사용** 옵션이 기본값입니다.
        - **사용자 설정 사용**: 캐시 만료 시간(초)에 지정한 시간 동안 캐시합니다. 
        - **캐시 만료 시간(초)**: CDN 캐시 서버에 원본 파일을 얼마나 오래 캐시할 것인지 TTL을 설정합니다.

2. **확인** 버튼을 클릭해 CDN 서비스를 생성합니다. 
3. 새로 생성된 CDN 서비스는 **[서비스ID].toastcdn.net** 도메인으로 생성되며, 파란색 느낌표 아이콘이 표시됩니다.
    ![마이그레이션서비스생성후목록](https://static.toastoven.net/prod_cdn/v2/migration-new-create.png)

    
### 3. 신규 생성한 CDN 서비스 테스트와 운영 서비스 적용

신규 생성한 **[서비스ID].toastcdn.net** 서비스를 운영 중인 서비스에 적용하기 전에 테스트를 진행하고 서비스에 배포합니다.


#### 3.1 기본 서비스 도메인([서비스ID].cdn.toastcloud.com)으로 서비스 중인 경우 
1. 신규 생성한 CDN 서비스의 테스트용 빌드를 생성합니다. 
    - 테스트용 빌드는 기존 **[서비스ID].cdn.toastcloud.com** 의 서비스 도메인 대신, 신규 생성한 CDN 서비스의 **[서비스ID].toastcdn.net** 서비스 도메인 주소로 빌드합니다. 
3. 테스트용 빌드를 로컬 환경 또는 개발 환경 서버에서 구동해 보고 동작에 문제가 없는지 테스트합니다. 
    ![기본서비스도메인-테스트환경](https://static.toastoven.net/prod_cdn/v2/migration-test-test-build-before.png)
4. 테스트가 완료되면 운영 중인 서비스 빌드에 신규 생성한 CDN 서비스 도메인 **[서비스ID].toastcdn.net**을 적용해 배포합니다.
    ![기본서비스도메인-테스트환경-적용](https://static.toastoven.net/prod_cdn/v2/migration-test-test-build-after.png)

#### 3.2 도메인 별칭(domain alias)으로 서비스 중인 경우 
도메인 별칭은 기본으로 제공하는 서비스 도메인 주소 **[서비스ID].cdn.toastcloud.com**이 아닌 소유한 도메인으로 CDN 서비스를 이용하는 경우입니다.

##### [사전 작업] 클라이언트와 CDN 에지(edge) 구간에 보안 전송(HTTPS)을 지원하려면 먼저 '인증서 발급' 작업을 진행합니다.
1. 클라이언트와 CDN 에지 서버 구간에 보안 전송(HTTPS)를 지원 하려면 CDN 에지 서버에 소유한 도메인의 인증서가 배포되어야 합니다. 인증서 발급과 배포는 **인증서 관리** 탭에서 할 수 있습니다. [사용자 콘솔 가이드 > 인증서 관리](./console-guide/#_5)를 참고해 **인증서 발급 및 배포** 단계까지 진행합니다. 
2. 인증서 발급 및 배포가 완료되면 CDN 서비스 연동 단계에서 **CNAME 레코드 설정을 제외하고** 도메인 별칭 설정만 신규 생성된 CDN 서비스에 설정합니다. 
    - **CNAME 레코드 설정은 운영 중인 서비스에 영향이 발생하므로 테스트가 끝난 후 진행해야 합니다.**

##### 테스트 방법 1: 로컬 PC 환경의 호스트(hosts) 파일을 변조해 확인하는 방법
1. nslookup 명령어로 **[서비스ID].toastcdn.net**의 A 레코드에 설정된 CDN 에지(edge) 서버의 IP 주소를 확인합니다.  
    - nslookup 명령어는 운영체제에 따라 명령어가 다르거나 결과 형식이 다를 수 있습니다.
    A 레코드의 IP 주소를 확인합니다. 아래 예시와 같이 여러 개가 등록되어 있을 수 있습니다. 여러 개가 조회되는 경우 임의의 IP 하나만 사용하면 됩니다.

    ![nslookup-IP확인](https://static.toastoven.net/prod_cdn/v2/migration-nslookup.png)

2. 테스트를 수행할 로컬 또는 개발 환경의 호스트(hosts) 파일에 확인한 에지 IP 주소와 도메인을 입력합니다.
    ```
    xxx.xxx.xxx.xxx your-alias.domain.com
    ```
3. 로컬 또는 개발 환경에서 호스트(hosts) 파일을 변조해 동작에 문제가 없는지 테스트합니다.
   ![도메인별칭-hosts파일변조](https://static.toastoven.net/prod_cdn/v2/migration-test-alias-hosts-before.png)
4. 테스트가 완료되면 운영 중인 도메인 별칭의 CNAME 레코드를 **[서비스ID].toastcdn.net**으로 위임합니다. 
   ![도메인별칭-hosts파일변조-적용](https://static.toastoven.net/prod_cdn/v2/migration-test-alias-hosts-after.png)


##### 테스트 방법 2: 새 도메인 별칭(domain alias)를 생성해 테스트하는 방법 
1. 모바일 앱 등과 같이 호스트(hosts) 파일을 변경할 수 없을 때는 새 도메인 별칭을 테스트용으로 생성해 확인합니다 (예: alias-for-test.doamin.com).
2. 새 도메인 별칭도 인증서 발급이 필요하므로 인증서 관리에서 먼저 인증서 발급과 배포를 진행하시기 바랍니다. 문서 상단의 **[사전 작업] 클라이언트와 CDN 에지(edge) 구간에 보안 전송(HTTPS)을 지원하려면 먼저 [인증서 발급] 작업을 진행합니다.** 내용을 참고합니다.
3. 테스트용 빌드를 만들어 새 도메인 별칭으로 설정해 테스트용 빌드를 생성합니다. 
4. 로컬 또는 개발 환경에 서비스를 구동해 서비스 동작에 문제가 없는지 테스트합니다.
   ![도메인별칭-테스트빌드](https://static.toastoven.net/prod_cdn/v2/migration-test-alias-build-before.png)
5. 테스트가 완료되면 도메인 별칭의 CNAME 레코드를 **[서비스ID].toastcdn.net**으로 위임합니다. 
   ![도메인별칭-테스트빌드-적용](https://static.toastoven.net/prod_cdn/v2/migration-test-alias-build-after.png)

### 4. 기존 CDN 서비스 삭제 
1. 모든 마이그레이션 작업이 완료되면 **통계** 탭에서 기존 CDN 서비스 통계를 조회합니다. 
2. 기존 CDN 서비스의 통계를 확인해 더 이상 트래픽 유입이 없다면 기존 **[서비스ID].cdn.toastcloud.com** 서비스를 삭제합니다. 통계 데이터 생성에는 약 30분이 걸리므로 충분한 시간을 두고 트래픽 유입을 확인합니다.
    ![마이그레이션완료후삭제](https://static.toastoven.net/prod_cdn/v2/migration-old-delete.png)
