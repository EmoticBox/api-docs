---
title: EmoticBox API

language_tabs: # must be one of https://git.io/vQNgJ
  - html
  - javascript
  - json

toc_footers:
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

search: true

code_clipboard: true
---

# 소개

EmoticBox API 문서입니다.

<aside class="notice">
API의 전반적인 흐름을 제외한 세부사항은 베타 버전 출시 전까지 수시로 변경될 수 있습니다.
</aside>

EmoticBox API에 대한 기술적 개선점 또는 EmoticBox API 문서에 대한 개선점은 <a href="mailto:tech@emoticbox.com">EmoticBox 기술지원(tech@emoticbox.com)</a>으로 보내주시면 면밀히 검토 후 회신드리겠습니다.

## 용어

### 고객(사)

Emoticon API를 사용하는 고객(사)를 의미합니다.

### 사용자

고객(사) 서비스를 사용하는 사용자를 의미합니다.

### 서비스

EmoticBox API를 사용하는 고객(사)의 서비스를 의미합니다.
EmoticBox API를 사용하기 위해서는 서비스를 등록하고 API 키를 발급받아야 합니다.

### 서비스 ID

서비스를 구분하기 위한 UUID 형식의 고유값입니다.
서비스 ID는 서비스 등록시 자동으로 생성되며 변경할 수 없습니다.
서비스를 삭제하고 새로 등록하는 경우 새 서비스 ID가 생성되며 서로 다른 서비스로 간주됩니다.

### API 키

고객사 서비스를 인증하기 위한 비밀키입니다.
한 서비스에 대해 여러 API 키를 발급받을 수 있습니다.

<aside class="warning">
API Key는 외부에 공개되지 않아야 합니다.
</aside>

<aside class="notice">
API Key가 외부에 유출되었거나 유출되었다고 판단되는 경우 즉시 <a href="mailto:tech@emoticbox.com">EmoticBox 기술지원(tech@emoticbox.com)</a>으로 문의해주시기 바랍니다.
</aside>

### 이모티콘

이모티콘 패키지에 포함되어 있는 각각의 이모티콘을 의미합니다.

### 이모티콘 패키지

여러 이모티콘의 묶음으로 이모티콘의 최소 판매 단위입니다.
한 이모티콘 패키지에는 16개, 24개, 32개 또는 40개의 이모티콘이 포함되어 있습니다.

### 브랜드 이모티콘 패키지

* 준비중입니다.

### 사용 가능한 이모티콘 패키지

사용자가 EmoticBox 회원인 경우 사용 가능한 이모티콘 패키지는 기본 제공 이모티콘 패키지와 사용자가 EmoticBox에서 구매한 이모티콘 패키지로 구성되며, 사용자가 EmoticBox 회원이 아닌 경우에는 기본 제공 이모티콘 패키지로만 구성됩니다.

### 탭 이미지

이모티콘 패키지의 대표 이미지로 사용자가 사용할 이모티콘 패키지를 선택할 때 표시됩니다.

<img src="attachments/tabimage.png" alt="Tab Image">

&#9651; 탭 이미지(박스 안)

# 인증

EmoticBox API를 사용하기 위해서는 서비스 ID와 API 키를 사용하여 엑세스 토큰을 발급받아야 합니다.
아래 시퀀스 다이어그램은 인증 및 엑세스 토큰 절차를 보여줍니다.

<img src="attachments/d1.png" alt="Service & User Authentication">

&#9651; 인증 및 엑세스 토큰 발급 절차(이미지를 새 탭에서 열면 크게 볼 수 있습니다)

## REST API

### Authorize User

```javascript
const fetch = require('node-fetch');

const endpoint = 'https://api.emoticbox.com/api-auth/v0';

fetch(`${endpoint}/auth`, {
  method: 'POST',
  body: {
    serviceId: 'your_service_id',
    apiKey: 'your_api_key',
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
이 API는 고객사 서버에서 호출하여야 합니다.
</aside>

#### HTTP Request

```json
{
  "serviceId": "your_service_id",
  "apiKey": "your_secret_key",
  "userEmail": "user_email@your_domain.com"
}
```

`POST /api-auth/v0/auth`

##### Body Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
serviceId | string | true | 고객사 서비스 ID.
apiKey | string | true | 고객사 서비스 API 키.
userEmail | string | true | 고객사 서비스에서 Access Token을 요청한 사용자를 식별할 수 있는 값(최대 64문자).

* `userEmail`은 한 서비스 내에서 고유한 값이어야 합니다(사용자 이메일, 아이디, PK, Hash 등).
* 서비스 연동시 `userEmail` 값이 EmoticBox에 저장되며 이 값을 기반으로 연동 여부를 판단합니다.

#### HTTP Response

```json
{
  "authToken": {String}
}
```

Field | Type | Description
----- | ---- | -----------
authToken | string | 인증 토큰

### Token Exchange

```javascript
const endpoint = 'https://api.emoticbox.com/api-auth/v0';

fetch(`${endpoint}/token`, {
  method: 'POST',
  body: {
    authToken: 'authentication-token',
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

<aside class="notice">
이 API는 클라이언트에서 호출하여야 합니다.
</aside>

#### HTTP Request

`POST /api-auth/v0/token`

```json
{
  "authToken": {String}
}
```

##### Body Parameters

Field | Type | Required | Description
------|------|----------|------------
authToken | string | true | Authorize User API를 통해 발급받은 Authorization Token

#### HTTP Response

```json
{
  "accessToken": {String},
  "isGuest": {Boolean}
}
```

Field | Type | Description
----- | ---- | -----------
accessToken | string | 엑세스 토큰
isGuest | boolean | 연동 여부(연동되지 않은 사용자인 경우 `true`, 연동된 사용자인 경우 `false`)

## API Rate Limit

TBD

# 이모티콘 API

아래 시퀀스 다이어그램은 이모티콘 패키지에 포함되어 있는 이모티콘을 불러와 전송하는 절차를 보여줍니다.

<img src="attachments/d2.png" alt="Sending an Emoticon">

&#9651; 이모티콘 전송 절차(이미지를 새 탭에서 열면 크게 볼 수 있습니다)

## REST API

### List Available Emoticon Packages

인증된 사용자가 사용 가능한 이모티콘 패키지 목록을 불러옵니다.
사용 가능한 이모티콘 패키지에는 아래 이모티콘 패키지가 포함됩니다.

* 기본 제공되는 이모티콘 패키지
* 사용자가 EmoticBox에서 구매한 이모티콘 패키지(사용자가 서비스 연동 설정을 한 경우)

```javascript
const endpoint = 'https://api.emoticbox.com/emoticon-api/v0';
const accessToken = 'access-token';

fetch(`${endpoint}/emoticon`, {
  method: 'GET',
  headers: {
    Authorization: `Bearer ${accessToken}`,
  },
})
  .then((res) => {
    return res.json();
  })
  .then((json) => {
    for (const i; i < json.numEmoticonPacks; i += 1) {
      console.log(json.emoticonPacks[i]);
    }
  })
  .catch((err) => {
    console.log(err);
  });
```

#### HTTP Request

`GET /emoticon-api/v0/emoticon`

##### Header Parameters

Field | Required | Description
------|----------|------------
Authorization | true | Token Exchange API를 통해 발급받은 엑세스 토큰(`Bearer example-access-token`)

#### HTTP Response

```json
{
  "isGuest": {Boolean},
  "numEmoticonPacks": {Integer},
  "emoticonPacks": [{
    "id": {Integer},
    "name": {String},
    "isAnimated": {Boolean},
    "tabImage": {
      "color": {String},
      "grayscale": {String},
    }
  },
  {
    "id": {Integer},
    "name": {String},
    "isAnimated": {Boolean},
    "tabImage": {
      "color": {String},
      "grayscale": {String},
    }
  },
  ...
  ]
}
```

Field | Type | Description
----- | ---- | -----------
isGuest | boolean | 연동 여부(연동되지 않은 사용자인 경우 `true`, 연동된 사용자인 경우 `false`)
numEmoticonPacks | integer | 이모티콘 패키지의 개수
emoticonPacks | emoticonPack[] | 이모티콘 패키지 리스트
emoticonPack.id | string | 이모티콘 패키지의 ID
emoticonPack.name | string | 이모티콘 패키지의 이름
emoticonPack.isAnimated | boolean | 이모티콘 패키지의 애니메이션 여부
emoticonPack.tabImage | tabImage{} | 이모티콘 패키지 탭 이미지
tabImage.color | string | 이모티콘 패키지 컬러 탭 이미지 ID
tabImage.grayscale | string | 이모티콘 패키지 흑백 탭 이미지 ID


### Get Emoticon Package

```javascript
const endpoint = 'https://api.emoticbox.com/emoticon-api/v0';
const accessToken = 'access-token';
const emoticonId = 123;

fetch(`${endpoint}/emoticon/${emoticonId}`, {
  method: 'GET',
  headers: {
    Authorization: `Bearer ${accessToken}`,
  },
})
  .then((res) => {
    return res.json();
  })
  .then((json) => {
    for (const i; i < json.numEmoticons; i += 1) {
      console.log(json.emoticons[i]);
    }
  })
  .catch((err) => {
    console.log(err);
  });
```

#### HTTP Request

`GET /emoticon-api/v0/emoticon/:emoticonPackId`

##### Path Parameters

Field | Description
------|------------
emoticonPackId | 정보를 요청할 이모티콘 패키지의 ID(UUID 형식)

##### Header Parameters

Field | Required | Description
------|----------|------------
Authorization | true | Token Exchange API를 통해 발급받은 엑세스 토큰(`Bearer example-access-token`)

#### HTTP Response

```json
{
  "id": {String},
  "name": {String},
  "numEmoticons": {Integer},
  "isAnimated": {Boolean},
  "tabImage": {
    "color": {String},
    "grayscale": {String},
  },
  "emoticons": [
    {
      "id": {String},
      "order": {Integer},
    },
    {
      "id": {String},
      "order": {Integer},
    },
    ...
  ]
}
```

Field | Type | Description
----- | ---- | -----------
id | string | 이모티콘 패키지의 ID
name | string | 이모티콘 패키지의 이름
numEmoticons | integer | 패키지에 포함된 이모티콘의 개수
tabImage | tabImage{} | 탭 이미지 정보
tabImage.color | string | 이모티콘 패키지 컬러 탭 이미지 ID
tabImage.grayscale | string | 이모티콘 패키지 흑백 탭 이미지 ID
emoticons | emoticon[] | 이모티콘 정보
emoticon.id | string | 이모티콘 ID
emoticon.order | integer | 이모티콘 번호(순서)

## 이모티콘 이미지 표시 방법

<a href="./#list-emoticon-packages">List Emoticon Packages API</a>를 통해 얻을 수 있는 이모티콘 패키지 목록 또는 <a href="./#get-emoticon-package">Get Emoticon Package API</a>를 통해 얻을 수 있는 이모티콘 패키지 정보에는 해당 패키지의 탭 이미지 ID(UUID 형식)가 포함되어 있습니다.

<a href="./#get-emoticon-package">Get Emoticon Package API</a>를 통해 얻을 수 있는 이모티콘 패키지 정보에는 해당 패키지에 포함된 모든 이모티콘의 ID(UUID 형식)가 포함되어 있습니다.
하나의 이모티콘 패키지에는 16개, 24개, 32개 또는 40개의 이모티콘이 포함되어 있을 수 있습니다.

이모티콘 패키지 파일에는 확장자가 없으며 각 파일 형식에 따라 적절한 `Content-Type` 헤더를 포함하여 응답합니다.

### 이모티콘 패키지 탭 이미지

이모티콘 패키지 탭 이미지는 아래와 같이 URL을 구성하여 불러올 수 있습니다.

`https://emoticon.emoticbox.com/dist/{format}/tab/{id}`

```html
<img src="https://emoticon.emoticbox.com/dist/webp/tab/123456789">
<img src="https://emoticon.emoticbox.com/dist/png/tab/123456789">
```

* 포맷: `webp`, `png`
* 크기: 96px &times; 74px 단일 크기로만 제공

### 정지한 이모티콘 이미지

정지한 이모티콘 이미지는 아래와 같이 URL을 구성하여 불러올 수 있습니다.
애니메이션 여부와 관련없이 모든 이모티콘 패키지에는 정지한 이모티콘 이미지가 존재합니다.

`https://emoticon.emoticbox.com/dist/{format}/size/static/{id}`

```html
<img src="https://emoticon.emoticbox.com/dist/webp/360/static/123456789">
<img src="https://emoticon.emoticbox.com/dist/png/240/static/123456789">
```

* 포맷: `webp`, `png`
* 크기: `320`(320px &times; 320px), `240`(240px &times; 240px), `160`(160px &times; 160px), `120`(120px &times; 120px), `80`(80px &times; 80px)

### 일정 횟수만 재생되는 움직이는 이모티콘 이미지

일정 횟수만 재생되는 움직이는 이모티콘 이미지는 아래와 같이 URL을 구성하여 불러올 수 있습니다.
재생 횟수는 크리에이터가 설정한 값을 따릅니다.
애니메이션이 없는 이모티콘 패키지(`isAnimated`가 `false`인 경우)는 움직이는 이모티콘 이미지가 존재하지 않습니다.

`https://emoticon.emoticbox.com/dist/{format}/size/animated/{id}`

```html
<img src="https://emoticon.emoticbox.com/dist/webp/240/animated/123456789">
<img src="https://emoticon.emoticbox.com/dist/png/160/animated/123456789">
```

* 포맷: `webp`, `png`
* 크기: `320`(320px &times; 320px), `240`(240px &times; 240px), `160`(160px &times; 160px), `120`(120px &times; 120px), `80`(80px &times; 80px)

### 반복 재생되는 움직이는 이모티콘 이미지

반복 재생되는 이모티콘 이미지는 아래와 같이 URL을 구성하여 불러올 수 있습니다.
애니메이션이 없는 이모티콘 패키지(`isAnimated`가 `false`인 경우)는 움직이는 이모티콘 이미지가 존재하지 않습니다.

`https://emoticon.emoticbox.com/dist/{format}/size/loop/{id}`

```html
<img src="https://emoticon.emoticbox.com/dist/webp/160/loop/123456789">
<img src="https://emoticon.emoticbox.com/dist/png/120/loop/123456789">
```

* 포맷: `webp`, `png`
* 크기: `320`(320px &times; 320px), `240`(240px &times; 240px), `160`(160px &times; 160px), `120`(120px &times; 120px), `80`(80px &times; 80px)

## API Rate Limit

TBD

# 서비스 연동

EmoticBox 사용자는 자신의 계정을 서비스와 연동 설정할 수 있습니다.
사용자가 서비스와 연동할 수 있도록 하기 위해서는 사용자를 아래 URL로 이동시키거나, Deeplink를 통해 EmoticBox 애플리케이션을 실행하면 됩니다.

<img src="attachments/d3.png" alt="Service Integration">
&#9651; 서비스 연동 절차(이미지를 새 탭에서 열면 크게 볼 수 있습니다)

사용자가 연동 설정하는 경우 <a href="./#authorize-user">Authorize User API</a> 호출시 설정한 `userEmail` 필드 값이 EmoticBox에 등록되어 EmoticBox 사용자를 식별할 수 있게 됩니다.
`userEmail` 필드는 각 서비스별로 고유해야 합니다.

### PC 웹

```html
<a href="https://emoticbox.com/deeplink/integration?token=example-access-token">서비스 연동하기</a>
```

`https://emoticbox.com/deeplink/integration?token={access_token}`

### EmoticBox 애플리케이션

```html
<a href="EmoticboxStoreApp://?token=example-access-token">서비스 연동하기</a>
```

`EmoticboxStoreApp://?token={access_token}`

# 스토어 링크

EmoticBox는 스토어 링크를 통해 접속한 사용자가 이모티콘 패키지를 구매하는 경우 매출의 일부를 고객사에 분배하고 있습니다.
이를 위해서는 서비스 사용자가 EmoticBox 스토어 버튼을 눌렀을 때 아래 URL로 접속되도록 구현하시면 됩니다.

```html
<a href="https://emoticbox.com/deeplink/store?service_id=example-service-id">EmoticBox 스토어로 이동</a>
```

`https://emoticbox.com/deeplink/store?service_id={service_id}`

## EmoticBox 스토어 아이콘

EmoticBox 스토어 아이콘은 [구글 드라이브](https://drive.google.com/drive/folders/1Z4AHwpy_EXd_Z0VDY5H2sOV_M_Qw2Cfd?usp=sharing)를 통해 다운로드할 수 있습니다.
링크가 작동하지 않는다면 아래 주소로 직접 접속하시기 바랍니다.

`https://drive.google.com/drive/folders/1Z4AHwpy_EXd_Z0VDY5H2sOV_M_Qw2Cfd?usp=sharing`

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
