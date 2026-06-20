---
date: 2026-01-28
tags:
  - CORS
  - 프록시
  - 동일출처정책
  - 웹보안
  - HTTP헤더
  - API
  - vibe_coding
  - Proxy
type: knowledge
contexts:
  - "[[🏷️Vibe Coding]]"
index:
  - "[[🎶Web Development]]"
  - "[[🔆인공지능]]"
YouTube:
scr_url:
scr:
---

# CORS와 Proxy 서버 이해하기

## CORS(Cross-Origin Resource Sharing)란?

CORS는 **"다른 출처의 리소스를 안전하게 공유하기 위한 브라우저의 보안 정책"**입니다.

### 출처(Origin)란?

출처는 **프로토콜 + 도메인 + 포트**로 구성됩니다.

```
출처의 구성:
https://myapp.netlify.app:443
  ↓        ↓              ↓
프로토콜  도메인         포트
```

같은 출처인 경우:

- `https://myapp.netlify.app/`
- `https://myapp.netlify.app/about`
- `https://myapp.netlify.app/.netlify/functions/api`

다른 출처인 경우:

- `https://myapp.netlify.app` vs `https://api.example.com`
- `http://localhost:5173` vs `https://api.weather.com`

### CORS 문제가 발생하는 상황

**시나리오:**

```
현재 페이지: https://myapp.netlify.app
JavaScript에서 실행:
  fetch('https://api.weather.com/data')
```

**브라우저의 판단:**

```
1. "myapp.netlify.app에서 api.weather.com으로 요청하네?"
2. "출처가 다르네! CORS 검사 해야겠다"
3. 요청 헤더에 Origin 추가
4. 응답 헤더의 Access-Control-Allow-Origin 확인
5. 허가 없으면 → JavaScript에게 데이터 전달 차단
```

### CORS의 핵심 개념

#### 브라우저가 강제하지만 서버가 허가함

```
브라우저(경비원):
  "들어가도 될까요?"
  
API 서버(건물 주인):
  "네, 괜찮습니다" (Access-Control-Allow-Origin 헤더)
  
브라우저:
  "주인이 허락했으니 통과!"
```

#### 요청은 완료되지만 접근이 차단됨

**흐름:**

```
1. 브라우저 → API 서버 (요청 전송 완료) ✅
2. API 서버 → 브라우저 (응답 전송 완료) ✅
3. 브라우저가 응답 받음 (데이터 도착) ✅
4. 브라우저가 CORS 헤더 확인
5. 허가 없으면 JavaScript 접근 차단 ❌
```

**중요:** 데이터는 브라우저까지 왔지만, JavaScript가 읽을 수 없습니다!

### HTTP 요청/응답 예시

**요청 (브라우저 → API 서버):**

```
GET /data HTTP/1.1
Host: api.weather.com
Origin: https://myapp.netlify.app  ← 브라우저가 자동 추가
```

**응답 (API 서버 → 브라우저):**

```
HTTP/1.1 200 OK
Content-Type: application/json
Access-Control-Allow-Origin: *  ← 서버가 허가 선언
```

브라우저: "Access-Control-Allow-Origin이 있네! 통과!" ✅

**응답에 CORS 헤더가 없는 경우:**

```
HTTP/1.1 200 OK
Content-Type: application/json
(Access-Control-Allow-Origin 없음)
```

브라우저: "허가가 없네! 차단!" ❌

---

## Proxy 서버로 CORS 해결하기

### Proxy 서버란?

**"클라이언트와 서버 사이에서 요청을 중계하는 서버"**

**비유:**

```
브라우저(나) → Proxy(친구) → 외부 API(편의점)

나는 편의점에 직접 못 가지만,
친구에게 부탁하면 친구가 대신 사준다
```

### 개발 중: Vite Proxy

**vite.config.js 파일 작성:**

```
export default {
  server: {
    proxy: {
      '/api': 'https://api.weather.com'
    }
  }
}
```

**의미:**

```
"/api"로 시작하는 요청을 
"https://api.weather.com"으로 전달해줘
```

**브라우저 코드:**

```javascript
fetch('/api/data')  // 같은 출처로 요청
```

**실제 흐름:**

```
브라우저
  ↓ fetch('/api/data')
  ↓ (출처: localhost:5173)
Vite 개발 서버 (localhost:5173)
  ↓ "이건 Proxy 설정된 경로네"
  ↓ fetch('https://api.weather.com/data')
외부 API (api.weather.com)
  ↓ 응답
Vite 개발 서버
  ↓
브라우저 (데이터 받음)
```

**브라우저 관점:**

```
"나는 localhost:5173/api/data에 요청했어"
"같은 출처네! CORS 문제 없어!"
(실제로 api.weather.com에서 온 데이터인지 모름)
```

### 중요: Proxy는 개발 전용

**Vite Proxy의 한계:**

```
npm run dev ✅ 작동
npm run build → 배포 ❌ Proxy 없음
```

**이유:**

- Vite 개발 서버는 개발 중에만 실행됨
- 빌드 결과물(dist 폴더)에는 Proxy 서버가 없음
- 정적 파일(HTML, CSS, JS)만 있음

---

## 배포 후: Netlify Functions로 해결

### Netlify Functions란?

**"같은 도메인에 만드는 작은 서버 함수"**

**구조:**

```
같은 도메인: https://myapp.netlify.app
├─ /                           (정적 파일, CDN)
└─ /.netlify/functions/api     (함수, AWS Lambda)
```

### 함수 작성하기

**netlify/functions/weather.js 파일:**

```javascript
const fetch = require('node-fetch');

exports.handler = async (event) => {
  // 외부 API 호출 (서버 환경, CORS 제약 없음)
  const response = await fetch('https://api.weather.com/data');
  const data = await response.json();
  
  // 브라우저에게 반환
  return {
    statusCode: 200,
    body: JSON.stringify(data)
  };
};
```

### 전체 흐름

```
1단계: 브라우저 → Netlify Function
  브라우저: fetch('/.netlify/functions/weather')
  출처: myapp.netlify.app
  대상: myapp.netlify.app
  → 같은 출처! CORS 문제 없음 ✅

2단계: Netlify Function → 외부 API
  함수(서버 환경): fetch('https://api.weather.com/data')
  → 서버 간 통신, CORS 제약 없음 ✅

3단계: 외부 API → Netlify Function
  응답 데이터 받음

4단계: Netlify Function → 브라우저
  같은 출처에서 응답
  → CORS 문제 없음 ✅
```

### 브라우저가 보는 것

**브라우저 관점:**

```
"나는 myapp.netlify.app/.netlify/functions/weather에 요청했어"
"같은 출처 myapp.netlify.app이네!"
"CORS 문제 없어!"
```

**브라우저는 함수가 외부 API를 호출하는지 모릅니다.**

### 왜 작동하는가?

**핵심 원리:**

```
브라우저 (CORS 제약 있음)
  ↓ 같은 도메인으로만 요청
Netlify Function (서버 환경, CORS 제약 없음)
  ↓ 어디든 자유롭게 요청
외부 API
```

- 브라우저는 같은 출처로만 통신 → 안전
- 함수는 서버 환경이라 CORS 없음 → 자유로움

---

## 리버스 프록시 개념

### Netlify의 역할

**Netlify 라우터가 경로에 따라 자동 분기:**

```
브라우저 요청: https://myapp.netlify.app/...
  ↓
Netlify 라우터
  ↓
경로 확인:
  ├─ /index.html → CDN 서버로
  ├─ /app.js → CDN 서버로
  └─ /.netlify/functions/api → Lambda 서버로
```

### 브라우저는 뒤를 모름

**브라우저가 보는 것:**

```
모든 요청 → myapp.netlify.app
모든 응답 ← myapp.netlify.app
```

**실제로 일어나는 일:**

```
정적 파일 요청 → CDN 서버 (서울)
함수 요청 → Lambda 서버 (미국)
```

**브라우저는 모릅니다. 알 필요도 없습니다.**

### 비유

**아파트 관리실:**

```
방문자(브라우저): "123동에 택배 보내주세요"
  ↓
관리실(Netlify): "알겠습니다"
  ↓
관리실 내부 처리:
  - 편지면 → 우편함으로
  - 택배면 → 택배 보관함으로
  - 음식이면 → 경비실로
  ↓
방문자: "잘 받았습니다"
(내부에서 어떻게 처리됐는지 모름)
```

---

## CDN과 엣지 서버

### CDN(Content Delivery Network)

**"전 세계에 파일을 분산 저장해서 빠르게 전달하는 네트워크"**

**구조:**

```
오리진 서버 (미국, 중앙)
  ↓ 파일 복사
전 세계 CDN 엣지 서버
  ├─ 서울 CDN
  ├─ 도쿄 CDN
  ├─ 싱가포르 CDN
  └─ 런던 CDN
```

**사용자 접속 시:**

```
한국 사용자 → 서울 CDN (빠름!)
일본 사용자 → 도쿄 CDN (빠름!)
영국 사용자 → 런던 CDN (빠름!)
```

### 엣지 서버와 엣지 함수

**엣지 서버:**

- CDN 네트워크의 개별 서버
- 사용자와 가까운 곳에 위치
- 주 역할: 정적 파일 캐싱 및 전달

**엣지 함수 (Edge Functions):**

- 엣지 서버에서 실행되는 코드
- 사용자와 가까운 곳에서 동적 처리
- 예: Netlify Edge Functions, Cloudflare Workers

**일반 서버리스 함수 (Functions):**

- 중앙 서버에서 실행 (예: 미국 AWS Lambda)
- 상대적으로 느림
- 하지만 더 강력한 기능

**비교:**

```
정적 파일 (CDN): 20-50ms ⚡⚡⚡
엣지 함수: 50-150ms ⚡⚡
일반 함수: 200-800ms ⚡
```

### CDN은 출처를 바꾸지 않음

**오해:**

```
"CDN을 거쳐서 출처가 같아진다" ❌
```

**사실:**

```
"같은 도메인을 사용하기 때문에 출처가 같다" ✅

정적 파일: myapp.netlify.app (CDN에서)
함수: myapp.netlify.app/.netlify/functions/api (Lambda에서)

물리적 위치는 다르지만
도메인이 같으므로 같은 출처!
```
