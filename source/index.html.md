---
title: Emoticon API v0 Documentation

language_tabs: # must be one of https://git.io/vQNgJ
  - json
  - javascript

toc_footers:
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

search: true

code_clipboard: true
---

# Introduction
EmoticBox Emoticon API 문서입니다.

<aside class="notice">
API의 전반적인 흐름을 제외한 세부사항은 베타 버전 출시 전까지 수시로 변경될 수 있습니다.
</aside>

EmoticBox API에 대한 기술적 개선점 또는 EmoticBox API 문서에 대한 개선점은 <a href="mailto:tech@emoticbox.com">EmoticBox 기술지원(tech@emoticbox.com)</a>으로 보내주시면 면밀히 검토 후 회신드리겠습니다.

## Glossary

### 고객(사)

Emoticon API를 사용하는 고객(사)를 의미합니다.

### 사용자

고객(사) 서비스를 사용하는 사용자를 의미합니다.

유의어: User

### 서비스

Emoticon API를 사용하는 고객(사)의 서비스를 의미합니다.
Emoticon API를 사용하기 위해서는 서비스를 등록하고 API 키를 발급받아야 합니다.

유의어: Service

### 서비스 ID

서비스를 구분하기 위한 UUID 형식의 고유값입니다.
서비스 ID는 서비스 등록시 자동으로 생성되며 변경할 수 없습니다.
서비스를 삭제하고 새로 등록하는 경우 새 서비스 ID가 생성되며 서로 다른 서비스로 간주됩니다.

유의어: Service ID

### API Secret

고객사 서비스를 인증하기 위한 비밀키입니다.

<aside class="warning">
API Secret은 외부에 공개되지 않아야 합니다.
</aside>

<aside class="notice">
API Secret이 외부에 유출되었거나 유출되었다고 판단되는 경우 즉시 <a href="mailto:tech@emoticbox.com">EmoticBox 기술지원(tech@emoticbox.com)</a>으로 문의해주시기 바랍니다.
</aside>

유의어: Service Secret, Secret

### API 키

서비스가 Emoticon API를 사용할 때 인증을 위해 사용되는 값으로 Service ID와 Secret의 쌍으로 구성됩니다.
한 서비스에 여러 개의 API 키를 발급받아 고객(사) 정책에 따라 활용할 수 있습니다.
한 서비스에 발급된 API 키들의 Service ID 값은 동일합니다.
API 키가 외부에 유출되었거나 기타 사정으로 폐기하고자 하는 때에는 API 키를 폐기할 수 있습니다.

<aside class="warning">
API Secret은 외부에 공개되지 않아야 합니다.
</aside>

유의어: API Key

### 이모티콘

이모티콘 패키지에 포함되어 있는 각각의 이모티콘을 의미합니다.

### 이모티콘 패키지

여러 이모티콘의 묶음으로 이모티콘의 최소 판매 단위입니다.
한 이모티콘 패키지에는 16개, 24개, 32개 또는 40개의 이모티콘이 포함되어 있습니다.

### 브랜드 이모티콘 (패키지)

EmoticBox 제휴사에서 마케팅을 위해 배포하는 이모티콘 또는 이모티콘 패키지를 의미합니다.
브랜드 이모티콘 (패키지)은 브랜드 이모티콘 (패키지) 사용을 동의한 서비스에만 제공되며, 사용자가 서비스에서 브랜드 이모티콘 (패키지)을 사용하는 경우 고객(사)는 수익을 분배받게 됩니다.

### 사용 가능한 이모티콘 (패키지)

사용자가 EmoticBox 회원인 경우 사용 가능한 이모티콘 (패키지)는 다음과 같이 구성됩니다.

브랜드 이모티콘 (패키지) + 기본 제공 이모티콘 (패키지) + 사용자가 보유한 이모티콘 (패키지)

사용자가 EmoticBox 회원이 아닌 경우 사용 가능한 이모티콘 (패키지)는 다음과 같이 구성됩니다.

브랜드 이모티콘 (패키지) + 기본 제공 이모티콘 (패키지)

단, 브랜드 이모티콘 (패키지)는 브랜드 이모티콘 (패키지) 사용을 동의한 서비스에만 제공됩니다.

### 사용자가 보유한 이모티콘 (패키지)

사용자가 보유한 이모티콘 (패키지)는 다음과 같이 구성됩니다.

사용자가 구입한 이모티콘 (패키지) + 사용자가 선물받은 이모티콘 (패키지)

### 썸네일

이모티콘 패키지의 대표 이미지로 사용자가 사용할 이모티콘 패키지를 선택할 때 표시됩니다.

유의어: 탭 이미지

# Authentication

아래 시퀀스 다이어그램은 이모티콘 API의 서비스 및 사용자 인증 절차를 보여줍니다.

<img src="attachments/auth_sequence_diagram.png" alt="Service & User Authentication">
&#9651; 서비스 및 사용자 인증 절차(이미지를 새 탭에서 열면 크게 볼 수 있습니다)

## Authentication API

```javascript
const fetch = require('node-fetch');

const endpoint = 'https://emoticon.api.emoticbox.com/v0';

fetch(`${endpoint}/auth`, {
  method: 'POST',
  body: {
    serviceId: 'your_service_id',
    serviceSecret: 'your_secret_key',
    userEmail: 'user_email@your_domain.com',
  },
})
  .then((res) => {
    return res.json();
  })
  .then((json) => {
    console.log(json.authenticationToken);
  })
  .catch((err) => {
    console.log(err);
  });
```

<aside class="notice">
Authentication API는 고객사 서버에서 호출하여야 합니다.
</aside>

### HTTP Request

```json
{
  "serviceId": {String},
  "serviceSecret": {String},
  "userEmail": {String}
}
```

`POST /auth`

#### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
serviceId | string | true | 고객사 서비스 ID.
serviceSecret | string | true | 고객사 서비스 API 키.
userEmail | string | true | Access Token을 요청한 사용자의 이메일.

### HTTP Response

```json
{
  "authenticationToken": {String}
}
```

Field | Type | Description
----- | ---- | -----------
authenticationToken | string | 인증 토큰

## Token Exchange API

```javascript
const endpoint = 'https://emoticon.api.emoticbox.com/v0';

fetch(`${endpoint}/token`, {
  method: 'POST',
  body: {
    authenticationToken: 'your_authentication_token',
  },
})
  .then((res) => {
    return res.json();
  })
  .then((json) => {
    console.log(json.accessToken);
  })
  .catch((err) => {
    console.log(err);
  });
```

### HTTP Request

`POST /token`

```json
{
  "authenticationToken": {String}
}
```

#### Body Parameters

Field | Type | Required | Description
------|------|----------|------------
authenticationToken | string | true | Authentication API를 통해 발급받은 인증 토큰

### HTTP Response

```json
{
  "accessToken": {String}
}
```

Field | Type | Description
----- | ---- | -----------
accessToken | string | 엑세스 토큰

# Request Emoticons

아래 시퀀스 다이어그램은 이모티콘 패키지에 포함되어 있는 이모티콘을 불러와 전송하는 절차를 보여줍니다.

<img src="attachments/sending_sequence_diagram.png" alt="Sending an Emoticon">
&#9651; 이모티콘 전송 절차(이미지를 새 탭에서 열면 크게 볼 수 있습니다)

## List Emoticon Packages API

### HTTP Request

`GET /emoticon`

#### Header Parameters

Field | Required | Description
------|----------|------------
Authorization | false | Token Exchange API를 통해 발급받은 엑세스 토큰(`Bearer abcd...`)

#### Body Parameters

Field | Type | Required | Description
------|------|----------|------------
accessToken | string | false | Token Exchange API를 통해 발급받은 엑세스 토큰(`abcd`)
 
<aside class="notice">
엑세스 토큰은 <code>Authorization</code> 헤더 또는 Body의 <code>accessToken</code> 필드 둘 중 하나로 설정되어야 하며, 어느 것도 설정되지 않은 경우 <code>401 Unauthorized</code> 응답을 받게 됩니다.
</aside>

### HTTP Response

```json
[
    {
      "id": {String},
      "name": {String},
      "thumbnail": {String}
    },
    {
      "id": {String},
      "name": {String},
      "thumbnail": {String}
    },
    ...
]
```

Field | Type | Description
----- | ---- | -----------
id | string | 이모티콘 패키지의 ID
name | string | 이모티콘 패키지의 이름
thumbnail | string | 이모티콘 썸네일 이미지 ID

## Get Emoticon Package API

### HTTP Request

`GET /emoticon/:emoticonPackId`

#### Path Parameters

Field | Description
------|------------
emoticonPackId | 정보를 요청할 이모티콘 패키지의 ID(UUID 형식)

#### Header Parameters

Field | Required | Description
------|----------|------------
Authorization | false | Token Exchange API를 통해 발급받은 엑세스 토큰(`Bearer abcd...`)

#### Body Parameters

Field | Type | Required | Description
------|------|----------|------------
accessToken | string | false | Token Exchange API를 통해 발급받은 엑세스 토큰(`abcd`)

<aside class="notice">
엑세스 토큰은 <code>Authorization</code> 헤더 또는 Body의 <code>accessToken</code> 필드 둘 중 하나로 설정되어야 하며, 어느 것도 설정되지 않은 경우 <code>401 Unauthorized</code> 응답을 받게 됩니다.
</aside>

### HTTP Response

```json
{
  "id": {String},
  "name": {String},
  "thumbnail": {String},
  "isAnimated": {Boolean},
  "emoticons": [
    {
      "id": {String},
      "number": {Integer}
    },
    {
      "id": {String},
      "number": {Integer}
    },
    ...
  ]
}
```

Field | Type | Description
----- | ---- | -----------
id | string | 이모티콘 패키지의 ID
name | string | 이모티콘 패키지의 이름
thumbnail | string | 이모티콘 썸네일 이미지 ID
emoticons | emoticon[] | 이모티콘 정보
emoticon.id | string | 이모티콘 ID
emoticon.number | integer | 이모티콘 번호(순서)

# How to Display Emoticons

당사가 제공하는 이모티콘 및 썸네일 이미지는 아래와 같은 URL을 구성하여 불러올 수 있습니다.

```html
<img src="https://emoticon.emoticbox.com/{format}/{size}/{id}">
```

`https://emoticon.emoticbox.com/{format}/{size}/{id}`

이모티콘 패키지 파일에는 확장자가 없으며 각 파일 형식에 따라 적절한 `Content-Type` 헤더를 포함하여 응답합니다.

#### Path Parameters

Field | Description
------|------------
format | 이모티콘 및 썸네일 이미지 포맷
size | 이모티콘 및 썸네일 이미지 크기
id | 이모티콘 및 썸네일 이미지 ID

## Emoticon Package Thumbnails

<a href="./#list-emoticon-packages-api">List Emoticon Packages API</a>를 통해 얻을 수 있는 이모티콘 패키지 목록 또는 <a href="./#get-emoticon-package-api">Get Emoticon Package API</a>를 통해 얻을 수 있는 이모티콘 패키지 정보에는 해당 패키지의 썸네일 이미지 ID(UUID 형식)가 포함되어 있습니다.

당사가 제공하는 이모티콘 패키지 썸네일 이미지 파일의 포맷 및 크기는 아래와 같습니다.

<table>
<tr>
    <th>Format</th>
    <th>Size</th>
</tr>
<tr>
    <td>webp</td>
    <td>TBD</td>
</tr>
<tr>
    <td>png</td>
    <td>TBD</td>
</tr>
</table>

## Emoticons

<a href="./#get-emoticon-package-api">Get Emoticon Package API</a>를 통해 얻을 수 있는 이모티콘 패키지 정보에는 해당 패키지에 포함된 모든 이모티콘의 ID(UUID 형식)가 포함되어 있습니다.
하나의 이모티콘 패키지에는 16개, 24개, 32개 또는 40개의 이모티콘이 포함되어 있을 수 있습니다.

당사가 제공하는 이모티콘 파일의 포맷 및 크기는 아래와 같습니다.

<table>
<tr>
    <th>Format</th>
    <th>Size</th>
</tr>
<tr>
    <td>webp</td>
    <td>TBD</td>
</tr>
<tr>
    <td>png</td>
    <td>TBD</td>
</tr>
<tr>
    <td>gif</td>
    <td>TBD</td>
</tr>
</table>

<aside class="notice">
GIF 포맷 이미지는 투명한 색상이 흰색으로 표시됩니다.
</aside>

<aside class="notice">
당사는 WEBP 및 PNG의 애니메이션 재생이 불가능한 환경(Internet Explorer 등)에서의 호환성을 위하여 GIF 포맷 이미지를 제공합니다.
GIF 포맷 이미지는 이미지의 품질이 낮고, 투명 색상이 흰색으로 표시되는 제약사항이 있습니다.
당사는 다른 포맷의 사용이 불가능한 환경에서만 GIF 포맷을 사용하는 것을 권장합니다.
</aside>

# API Rate Limit

TBD

# Errors

아래는 일반적인 오류 상황에서 돌려받는 [HTTP 상태 코드](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)입니다.

Error Code | Meaning
---------- | -------
400 Bad Request | 요청이 올바르지 않습니다(필요한 파라미터가 없는 경우, 파라미터가 올바르지 않은 경우 등).
401 Unauthorized | 엑세스 토큰이 존재하지 않거나 올바르지 않습니다.
403 Forbidden | 접근 권한이 없는 리소스입니다.
404 Not Found | 요청한 리소스가 존재하지 않습니다.
405 Method Not Allowed | 잘못된 HTTP Method로 요청하였습니다.
429 Too Many Request | API 요청이 너무 많습니다. <a href="./#api-rate-limit">API Rate Limit</a>을 참조하세요.
500 Internal Server Error | EmoticBox 서버 오류가 발생했습니다. 반복해서 발생하면 <a href="mailto:tech@emoticbox.com">EmoticBox 기술지원(tech@emoticbox.com)</a>으로 문의해주세요.
