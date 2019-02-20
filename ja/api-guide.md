## Content Delivery > CDN > APIガイド

TOAST CDNで提供するPublic APIを説明します。

## API共通情報

### ドメイン

| 名前           | ドメイン                                |
| --------------- | ------------------------------------- |
| CDN Public APIドメイン | https://api-gw.cloud.toast.com/tc-cdn |

### 事前準備

APIを使用するには、アプリキー(Appkey)とセキュリティーキー(SecretKey)が必要です。 
アプリキーとセキュリティーキーは、コンソール右上の**URL & Appkey**メニューで確認できます。

### リクエスト共通情報

#### リクエストヘッダ

| 名前         | 説明                     |
| ------------- | ------------------------- |
| Authorization | コンソールで発行されたセキュリティーキー(SecretKey) |

#### Pathパラメータ

すべてのAPIは、appKeyをpathパラメータに指定する必要があります。
* 例) /v1.5/appKeys/**{appKey}**/distributions

| 名前  | 説明                 |
| ------ | --------------------- |
| appKey | コンソールで発行されたアプリキー(Appkey) | 

### レスポンス共通情報

#### ヘッダ

すべてのAPIリクエストに対して、**200 OK**でレスポンスします。詳細なレスポンス結果は、次の例のようなレスポンス本文のヘッダを参考にしてください。

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Success"
    }
}
```


[フィールド]

| フィールド                | タイプ   | 説明  |
| -------------------- | ------- | ------ |
| header               | Object  | ヘッダ領域     |
| header.isSuccessful  | Boolean | 成功したか |
| header.resultCode | Integer | 結果コード |
| header.resultMessage | String | 結果メッセージ |

#### CDN状態コード

次はCDNサービスの状態を表す状態コードで、サービス照会時にサービス状態を確認できます。

| 値      | 説明                  |
| ---------- | ------------------------ |
| OPENING    | サービス起動中        |
| OPEN       | サービス中             |
| MODIFYING  | 修正中               |
| RESUME     | 開始                  |
| SUSPENDING | 停止進行中          |
| SUSPEND    | 停止                  |
| CLOSING    | 使用終了中          |
| CLOSE      | 使用終了             |
| ERROR      | サービス作成中にエラーが発生 |


## サービスAPI

### サービス作成

#### リクエスト


[URI]

| メソッド | URI                                  |
| ---- | ------------------------------------ |
| POST | /v1.5/appKeys/{appKey}/distributions |


[リクエスト本文]

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

[フィールド]

| 名前                                | タイプ | 必須化 | デフォルト値 | 有効範囲                | 説明                                                      |
| -------------------------------------- | ------- | --------- | ------ | --------------------------- | ------------------------------------------------------------ |
| distributions                          | List    | 必須      |        |                             | 作成するCDNのオブジェクトリスト                               |
| distributions[0].region                | String  | 必須      |        | LOCAL/GLOBAL                | サービス地域("LOCAL"：韓国、"GLOBAL"：グローバル)           |
| distributions[0].useOrigin             | String  | 必須      |        | Y/N                         | キャッシュ満了設定("Y"：原本設定を使用、 "N"：ユーザー設定を使用)   |
| distributions[0].referrerType          | String  | 必須      |        | BLACKLIST/WHITELIST         | リファラーアクセス管理("BLACKLIST"：ブラックリスト、 "WHITELIST"：ホワイトリスト) |
| distributions[0].description           | String  | 任意      |        | 最大255文字               | 説明                                                     |
| distributions[0].domainAlias           | String  | 任意      |        | 最大255文字               | ドメインエイリアス(個人または会社が所有しているドメイン使用、複数入力時\nトークンで区分して入力してください。) |
| distributions[0].maxAge                | Integer | 任意      | 0      | 0～2,147,483,647             | キャッシュ満了時間(秒)、デフォルト値0は604,800秒です。             |
| distributions[0].referrers             | String  | 任意      |        | '\n'トークンを含め、最大1024文字 | リファラー(複数入力時\nトークンで区分して入力してください。)    |
| distributions[0].origins               | List    | 必須      |        |                             | 原本サーバーオブジェクトリスト                                  |
| distributions[0].origins[0].origin     | String  | 必須      |        | 最大255文字               | 原本サーバー(domainまたはIP)                                     |
| distributions[0].origins[0].port       | String  | 必須      |        | 0～65,536                    | 原本サーバーポート                                           |
| distributions[0].origins[0].originPath | String  | 任意      |        | 最大8192文字              | 原本サーバーの下層パス(/を含むパスで入力してください。)        |
| distributions[0].callback              | Object  | 任意      |        |                             | CDN作成処理結果の通知を受けるコールバックURL(コールバック設定は任意入力です。) |
| distributions[0].callback.httpMethod   | String  | 必須      |        | GET/POST/PUT                | コールバックのHTTP Method                                           |
| distributions[0].callback.url          | String  | 必須      |        | 最大1024文字              | コールバックURL                                                     |




#### レスポンス


[レスポンス本文]

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


[フィールド]

| フィールド                               | タイプ | 説明                                                     |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | ヘッダ領域                                                |
| header.isSuccessful                    | Boolean | 成功したか                                                 |
| header.resultCode                      | Integer | 結果コード                                                |
| header.resultMessage                   | String  | 結果メッセージ                                              |
| distributions                          | List    | 作成されたCDNオブジェクトリスト                               |
| distributions[0].domain                | String  | 作成されたドメイン(サービス)名                               |
| distributions[0].domainAlias           | String  | 所有ドメイン                                              |
| distributions[0].region                | String  | サービス地域("LOCAL"：韓国、"GLOBAL"：グローバル)            |
| distributions[0].description           | String  | 説明                                                     |
| distributions[0].status                | String  | CDN状態コード([表] CDN状態コード参照)                                 |
| distributions[0].createTime            | String  | 作成日時                                                |
| distributions[0].useOrigin             | String  | 原本サーバー設定を使用するか("Y"：原本サーバー設定を使用、 "N"：ユーザー設定) |
| distributions[0].maxAge                | String  | キャッシュ満了時間(秒)                                           |
| distributions[0].referrerType          | String  | リファラーアクセス管理("BLACKLIST"：ブラックリスト、 "WHITELIST"：ホワイトリスト) |
| distributions[0].referrers             | String  | リファラーリスト                                              |
| distributions[0].origins               | List    | 原本サーバーオブジェクトリスト                                  |
| distributions[0].origins[0].origin     | String  | 原本サーバー(domainまたはIP)                                      |
| distributions[0].origins[0].originPath | String  | 原本サーバーの下層パス                                      |
| distributions[0].origins[0].port       | Integer | 原本サーバーポート                                           |
| distributions[0].callback              | Object  | サービス作成処理結果の通知を受けるコールバック                    |
| distributions[0].callback.httpMethod   | String  | コールバックのHTTP Method                                           |
| distributions[0].callback.url          | String  | コールバックURL                                                     |


### サービス照会

#### リクエスト


[URI]

| メソッド | URI                                  |
| ---- | ------------------------------------ |
| GET | /v1.5/appKeys/{appKey}/distributions |


[パラメータ]

| 名前 | タイプ | 必須か | 有効範囲 | 説明                     |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| domain | String | 任意      | 最大255文字 | 照会するドメイン(サービス名)   |
| status | String | 任意      | CDN状態コード | CDN状態コード([表] CDN状態コード参考) |

[例]
```
curl -X GET "https://api-gw.cloud.toast.com/tc-cdn/v1.5/appKeys/{appKey}/distributions?domain={domain}" \
 -H "Authorization: {secretKey}" \
 -H "Content-Type: application/json"
```

#### レスポンス


[レスポンス本文]

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


[フィールド]

| フィールド                               | タイプ | 説明                                                     |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | ヘッダ領域                                                |
| header.isSuccessful                    | Boolean | 成功したか                                                 |
| header.resultCode                      | Integer | 結果コード                                                |
| header.resultMessage                   | String  | 結果メッセージ                                              |
| distributions                          | List    | 作成されたCDNオブジェクトリスト                                 |
| distributions[0].domain                | String  | ドメイン名(サービス名)                                     |
| distributions[0].domainAlias           | String  | 所有ドメイン                                              |
| distributions[0].region                | String  | サービス地域("LOCAL"：韓国、"GLOBAL"：グローバル)             |
| distributions[0].description           | String  | 説明                                                     |
| distributions[0].status                | String  | CDN状態コード([表] CDN状態コード参考)                                 |
| distributions[0].createTime            | String  | 作成日時                                                |
| distributions[0].useOrigin             | String  | 原本サーバー設定を使用するか("Y"：原本サーバー設定を使用、 "N"：ユーザーを設定) |
| distributions[0].maxAge                | String  | キャッシュ満了時間(秒)                                           |
| distributions[0].referrerType          | String  | リファラーアクセス管理("BLACKLIST"：ブラックリスト、 "WHITELIST"：ホワイトリスト) |
| distributions[0].referrers             | String  | リファラーリスト                                              |
| distributions[0].origins               | List    | 原本サーバーオブジェクトリスト                                  |
| distributions[0].origins[0].origin     | String  | 原本サーバー(domainまたはIP)                                      |
| distributions[0].origins[0].originPath | String  | 原本サーバーの下層パス                                      |
| distributions[0].origins[0].port       | Integer | 原本サーバーポート                                           |
| distributions[0].callback              | Object  | サービス配布処理結果の通知を受けるコールバック                    |
| distributions[0].callback.httpMethod   | String  | コールバックのHTTP Method                                           |
| distributions[0].callback.url          | String  | コールバックURL                                                     |


### サービス修正

#### リクエスト


[URI]

| メソッド | URI                                  |
| ---- | ------------------------------------ |
| PUT | /v1.5/appKeys/{appKey}/distributions |


[リクエスト本文]

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


[フィールド]

| 名前              | タイプ | 必須か | デフォルト値 | 有効範囲                                                | 説明                                                     |
| --------------------- | ------- | --------- | ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| domain                | String  | 必須      |        | 最大255文字                                                | 修正するドメイン(サービス名)                                   |
| useOrigin             | String  | 必須      |        | Y/N                                                          | キャッシュ満了設定(Y：原本設定を使用、 "N"：ユーザー設定を使用)      |
| referrerType          | String  | 必須      |        | BLACKLIST/WHITELIST                                          | リファラーアクセス管理("BLACKLIST"：ブラックリスト、 "WHITELIST"：ホワイトリスト) |
| description           | String  | 任意      |        | 最大255文字                                                | 説明                                                     |
| domainAlias           | String  | 任意      |        | 最大255文字                                                | ドメインエイリアス(個人または会社が所有しているドメイン使用、複数入力時\nトークンで区分して入力してください。) |
| maxAge                | Integer | 任意      | 0      | 0 ～ 2,147,483,647                                            | キャッシュ満了時間(秒)、デフォルト値0は604,800秒です。              |
| referrers             | String  | 任意      |        | '\n'トークンを含めて最大1024文字                               | リファラー(複数入力時\\nトークンで区分して入力してください。 )  |
| origins               | List    | 必須      |        |                                                              | 原本サーバー                                                |
| origins[0].origin     | String  | 必須      |        | 最大255文字                                                | 原本サーバー(domainまたはIP)                                      |
| origins[0].port       | Integer | 必須      |        | 0～65,536                                                     | 原本サーバーポート                                           |
| origins[0].originPath | String  | 任意      |        | 最大8192文字                                               | 原本サーバーの下層パス                                      |
| callback              | Object  | 任意      |        | CDNサービス配布結果の通知を受けるコールバックURL(コールバック設定は任意入力です。) |                                                              |
| callback.httpMethod   | String  | 必須      |        | GET/POST/PUT                                                 | コールバックのHTTP Method                                           |
| callback.url          | String  | 必須      |        | 最大1024文字                                               | コールバックURL                                                     |


#### レスポンス


[レスポンス本文]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    }
}
```


[フィールド]

| フィールド               | タイプ  | 説明 |
| -------------------- | ------- | ------ |
| header               | Object  | ヘッダ領域     |
| header.isSuccessful  | Boolean | 成功したか |
| header.resultCode    | Integer | 結果コード |
| header.resultMessage | String  | 結果メッセージ |


### サービスの部分修正

サービスの一部設定を変更する場合、部分修正APIを利用できます。

#### リクエスト


[URI]

| メソッド | URI                                  |
| ----- | ------------------------------------ |
| PATCH | /v1.5/appKeys/{appKey}/distributions |


[リクエスト本文]

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


[フィールド]

| 名前              | タイプ | 必須か | デフォルト値 | 有効範囲                                                | 説明                                                     |
| --------------------- | ------- | --------- | ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| domain                | String  | 必須      |        | 最大255文字                                                | 修正するドメイン(サービス名)                                   |
| useOrigin             | String  | 任意      |        | Y/N                                                          | キャッシュ満了設定(Y：原本設定使用、 N：ユーザー設定使用)        |
| referrerType          | String  | 任意      |        | BLACKLIST / WHITELIST                                        | リファラーアクセス管理("BLACKLIST"：ブラックリスト、 "WHITELIST"：ホワイトリスト) |
| description           | String  | 任意      |        | 最大255文字                                                | 説明                                                     |
| domainAlias           | String  | 任意      |        | 最大255文字                                                | ドメインエイリアス(個人または会社が所有しているドメイン使用、複数入力時\nトークンに分離して入力してください。) |
| maxAge                | Integer | 任意      | 0      | 0～2,147,483,647                                              | キャッシュ満了時間(秒)、デフォルト値0は604,800秒です。           |
| referrers             | String  | 任意      |        | '\n'トークンを含めて最大1024文字                               | リファラー(複数入力時\\nトークンに分離して入力してください。 )  |
| origins               | List    | 任意      |        |                                                              | 原本サーバー                                                |
| origins[0].origin     | String  | 任意      |        | 最大255文字                                                | 原本サーバー(domainまたはIP)                                     |
| origins[0].port       | Integer | 任意      |        | 0～65,536                                                   | 原本サーバーポート                                           |
| origins[0].originPath | String  | 任意      |        | 最大8192文字                                               | 原本サーバー下層パス                                      |
| callback              | Object  | 任意      |        | CDNサービス配布結果の通知を受けるコールバックURL(コールバック設定は任意入力です。) |                                                              |
| callback.httpMethod   | String  | 任意      |        | GET/POST/PUT                                                 | コールバックのHTTP Method                                           |
| callback.url          | String  | 任意      |        | 最大1024文字                                               | コールバックURL                                                     |

- originsフィールドを設定する時、origin,port,originPathフィールドは必須入力値です。
- callbackフィールドを設定する時、httpMethod、urlフィールドは必須入力値です。 

#### レスポンス


[レスポンス本文]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    }
}
```


[フィールド]

| フィールド               | タイプ  | 説明 |
| -------------------- | ------- | ------ |
| header               | Object  | ヘッダ領域     |
| header.isSuccessful  | Boolean | 成功したか |
| header.resultCode    | Integer | 結果コード |
| header.resultMessage | String  | 結果メッセージ |


### サービス削除

#### リクエスト


[URI]

| メソッド | URI                                  |
| ------ | ------------------------------------ |
| DELETE | /v1.5/appKeys/{appKey}/distributions |


[リクエスト本文]

```json
{
    "domains" : [
        "lhcsxuo0.cdn.toastcloud.com"
    ]
}
```


[フィールド]

| 名前  | タイプ | 必須か | デフォルト値 | 有効範囲 | 説明                |
| ------- | ------ | ----- | ---- | ----- | --------------------- |
| domains | String | 必須    |      |       | 削除するドメイン、複数ドメイン入力可 |

**\* 複数のドメイン入力時、該当サービスはすべて終了します。**

#### レスポンス


[レスポンス本文]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    }
}
```


[フィールド]

| フィールド               | タイプ  | 説明 |
| -------------------- | ------- | ------ |
| header               | Object  | ヘッダ領域     |
| header.isSuccessful  | Boolean | 成功したか |
| header.resultCode    | Integer | 結果コード |
| header.resultMessage | String  | 結果メッセージ |

## キャッシュ再配布API

### キャッシュ再配布(Purge)

#### リクエスト

[URI]

| メソッド | URI                           |
| ---- | ----------------------------- |
| POST | /v1.5/appKeys/{appKey}/purges |


[リクエスト本文]

```json
{
	"domain": "sample.cdn.toastcloud.com",
	"purgeType": "ITEM",
	"purgeList":"/img_01.png"
}
```


[フィールド]

| 名前  | タイプ | 必須か | デフォルト値 | 有効範囲         | 説明                                                     |
| --------- | ------ | --------- | ------ | --------------------- | ------------------------------------------------------------ |
| domain    | String | 必須      |        | 最大255文字         | 再配布するドメイン(サービス)名                             |
| purgeType | List   | 必須      |        | ITEM / WILDCARD / ALL | 再配布タイプ("ITEM"、"WILDCARD"、"ALL")                       |
| purgeList | String | 任意      |        |                       | 再配布対象項目リスト(複数入力する時は\\nトークンで区分して入力してください。purgeTypeがALLの場合は入力しなくても構いません。) |

#### レスポンス

[レスポンス本文]

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


[フィールド]

| フィールド               | タイプ  | 説明    |
| -------------------- | ------- | --------- |
| header               | Object  | ヘッダ領域     |
| header.isSuccessful  | Boolean | 成功したか     |
| header.resultCode    | Integer | 結果コード     |
| header.resultMessage | String  | 結果メッセージ    |
| purgeSeq             | Integer | 再配布要請番号 |

### キャッシュ再配布(Purge)照会

#### リクエスト


[URI]

| メソッド | URI                           |
| ---- | ----------------------------- |
| GET  | /v1.5/appKeys/{appKey}/purges |


[パラメータ]

| 名前       | タイプ  | 必須か | デフォルト値 | 有効範囲                                | 説明          |
| ------------ | ------- | ----- | ---- | ---------------------------------------- | --------------- |
| domain       | String  | 必須    |      | 最大255文字                               | ドメイン(サービス)名 |
| page         | Integer | 任意    |      |                                          | ページ番号      |
| itemsPerPage | Integer | 任意    |      |                                          | 1ページ当たりの再配布された項目個数 |
| startTime    | String  | 任意    |      | "yyyy-MM-ddTHH:mm:ss.SSSZ" ex)"2018-02-22T09:00:00.000Z"(UTC) | 期間検索       |
| endTime      | String  | 任意    |      | "yyyy-MM-ddTHH:mm:ss.SSSZ" ex)"2018-02-22T09:00:00.000Z"(UTC) | 期間検索       |


```
curl -X GET "https://api-gw.cloud.toast.com/tc-cdn/v1.5/appKeys/{appKey}/purges?domain={domain}" \
 -H "Authorization: {secretKey}" \
 -H "Content-Type: application/json"
```

#### レスポンス


[レスポンス本文]

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


[フィールド]

| フィールド                  | タイプ  | 説明                            |
| ----------------------- | ------- | --------------------------------- |
| header                  | Object  | ヘッダ領域                         |
| header.isSuccessful     | Boolean | 成功したか                          |
| header.resultCode       | Integer | 結果コード                         |
| header.resultMessage    | String  | 結果メッセージ                        |
| totalItems              | Integer | 再配布した総個数                      |
| purges                  | List    | 再配布項目リスト                    |
| purges[0].seq           | Integer | 再配布要請番号                     |
| purges[0].progress      | Integer | 再配布進行率                        |
| purges[0].purgeTime     | Long    | 再配布要請時間                     |
| purges[0].lastCheckTime | Long    | 再配布実行最後のチェック時間              |
| purges[0].type          | String  | 再配布タイプ("ITEM"、"WILDCARD"、"ALL") |
| purges[0].path          | String  | 再配布要請項目                     |
