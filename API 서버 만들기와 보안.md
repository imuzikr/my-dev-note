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

## API 서버 함수 만들기

### 함수가 하는 일

**"브라우저와 외부 API 사이에서 데이터를 중계하는 심부름꾼"**

**역할:**

```
브라우저
  ↓ "날씨 데이터 가져와줘"
함수 (심부름꾼)
  ↓ "알겠습니다. 제가 대신 가져올게요"
외부 API
  ↓ 데이터 전송
함수
  ↓ "여기 날씨 데이터입니다"
브라우저
```

### 함수 작성하기

**netlify/functions/weather.js 파일:**

```javascript
// 1단계: 도구 준비
const fetch = require('node-fetch');

// 2단계: 함수 정의
exports.handler = async (event) => {
  
  // 3단계: 외부 API 호출
  const response = await fetch('https://api.weather.com/data');
  
  // 4단계: 응답을 읽을 수 있는 형태로 변환
  const data = await response.json();
  
  // 5단계: 브라우저에게 반환
  return {
    statusCode: 200,
    body: JSON.stringify(data)
  };
};
```

### 각 단계 설명

**1단계: 도구 준비**

```javascript
const fetch = require('node-fetch');
```

- `require`: 내 컴퓨터(node_modules)에서 도구 가져오기
- `node-fetch`: 인터넷에서 데이터를 가져오는 도구
- `fetch`: 앞으로 이 이름으로 사용

**비유:**

```
공구함에서 망치 꺼내기
const 망치 = require('망치');
```

**2단계: 함수 정의**

```javascript
exports.handler = async (event) => {
```

- `exports.handler`: 이 함수의 시작을 알림
- `async`: "시간 걸리는 작업 있어요"
- `event`: 브라우저가 보낸 정보

**3단계: 외부 API 호출**

```javascript
const response = await fetch('https://api.weather.com/data');
```

- `await`: "응답 올 때까지 기다려"
- `fetch(...)`: 외부 API에 요청
- `response`: 받은 응답 저장

**흐름:**

```
함수 → 외부 API 서버 방문
     → "데이터 주세요" 요청
     → 기다림...
     → 응답 받음
     → response에 저장
```

**4단계: 데이터 변환**

```javascript
const data = await response.json();
```

- `.json()`: 응답을 JavaScript 객체로 변환
- `data`: 변환된 데이터

**비유:**

```
response = 봉투 (아직 안 뜯음)
data = 편지 내용 (봉투 뜯어서 읽음)
```

**5단계: 브라우저에 반환**

```javascript
return {
  statusCode: 200,
  body: JSON.stringify(data)
};
```

- `statusCode: 200`: "성공했어요!"
- `body`: 실제 데이터
- `JSON.stringify`: 데이터를 문자열로 변환

---

## require vs fetch 이해하기

### 둘 다 "가져오기"지만 위치가 다름

**require = 로컬(내 컴퓨터)에서 가져오기**

```javascript
const fetch = require('node-fetch');
```

**위치:**

```
내 컴퓨터:
  my-project/
    └─ node_modules/
        └─ node-fetch/  ← 여기서 가져옴
```

**비유:** 내 책장에서 책 꺼내기

**fetch = 원격(인터넷)에서 가져오기**

```javascript
const response = await fetch('https://api.weather.com/data');
```

**위치:**

```
인터넷:
  https://api.weather.com/  ← 여기서 가져옴
```

**비유:** 도서관 가서 책 빌리기

### 속도 차이

```
require:
  - 내 컴퓨터에 이미 있음
  - 즉시 사용 (0.001초)
  - await 필요 없음

fetch:
  - 인터넷 어딘가에 있음
  - 기다려야 함 (0.5~2초)
  - await 필요
```

### 함께 사용하는 이유

```javascript
// 1. 도구 준비 (require)
const fetch = require('node-fetch');
// "전화기 꺼내기"

// 2. 도구로 데이터 가져오기 (fetch)
const data = await fetch('https://...');
// "전화기로 주문하기"
```

---

## node-fetch 패키지 이해하기

### 패키지명 vs 변수명

```javascript
const fetch = require('node-fetch');
      ↑              ↑
   변수명        패키지명
  (내 마음대로)  (정확해야 함)
```

**패키지명 (`node-fetch`):**

- npm 저장소에 등록된 실제 이름
- 설치할 때 사용: `npm install node-fetch`
- 바꿀 수 없음

**변수명 (`fetch`):**

- 코드에서 사용할 이름
- 원하는 대로 지을 수 있음
- 관습적으로 `fetch`라고 씀

**비유:**

```
마트에서 물건 사기:

const 우유 = require('서울우유');
      ↑              ↑
   내가 부를 이름   제품 실제 이름

- 마트에서: "서울우유 주세요" (정확한 이름)
- 집에서: "우유 좀 가져와" (편한 이름)
```

### 설치와 사용

**1. 설치:**

```bash
npm install node-fetch
```

**의미:** npm 창고에서 `node-fetch` 다운로드

**2. 사용:**

```javascript
const fetch = require('node-fetch');
```

**의미:** 다운로드된 `node-fetch`를 `fetch`라는 이름으로 사용

---

## 제3의 서버와 통신하기

### 함수는 어디든 요청 가능

**브라우저의 제약:**

```
브라우저 → 다른 도메인 ❌ (CORS 제약)
```

**함수는 자유로움:**

```
함수 (서버 환경)
  ↓
  ├─→ 날씨 API ✅
  ├─→ 뉴스 API ✅
  ├─→ Google API ✅
  ├─→ MongoDB 데이터베이스 ✅
  └─→ 어떤 서버든 ✅
```

### 여러 서버에서 데이터 통합

**함수 예시:**

```javascript
exports.handler = async () => {
  // 3개의 다른 서버에서 데이터 가져오기
  const weather = await fetch('https://api.weather.com/data');
  const news = await fetch('https://newsapi.org/headlines');
  const stocks = await fetch('https://api.stocks.com/prices');
  
  // 데이터 통합
  return {
    statusCode: 200,
    body: JSON.stringify({
      weather: await weather.json(),
      news: await news.json(),
      stocks: await stocks.json()
    })
  };
};
```

**흐름:**

```
브라우저
  ↓ 한 번 요청
함수
  ├─→ 날씨 API
  ├─→ 뉴스 API
  └─→ 주식 API
  ↓ 데이터 통합
브라우저 (모든 데이터 한 번에 받음)
```

### 데이터베이스 연결도 가능

**MongoDB 예시:**

```javascript
exports.handler = async () => {
  // MongoDB 연결
  const client = await MongoClient.connect('mongodb://...');
  
  // 데이터 조회
  const users = await client.db('myapp')
    .collection('users')
    .find({})
    .toArray();
  
  return {
    statusCode: 200,
    body: JSON.stringify(users)
  };
};
```

---

## API 인증과 보안

### 제3의 서버도 보안이 있음

**잘못된 생각:**

```
"함수가 요청하면 서버가 무조건 데이터 줄 거야" ❌
```

**현실:**

```
"인증 정보가 없으면 거부당함" ✅
```

### 인증 방법들

**1. API 키 (가장 흔함)**

```javascript
const response = await fetch(
  'https://api.weather.com/data?key=abc123xyz'
  //                               ↑ API 키
);
```

**API 키 없이 요청하면:**

```
{
  "error": "Invalid API key",
  "message": "You need a valid API key"
}
```

**2. 데이터베이스 인증**

```javascript
const client = await MongoClient.connect(
  'mongodb://username:password@server.com/mydb'
  //         ↑ 사용자명  ↑ 비밀번호
);
```

**3. Bearer Token**

```javascript
const response = await fetch('https://api.example.com/data', {
  headers: {
    'Authorization': 'Bearer abc123xyz'
    //                      ↑ 토큰
  }
});
```

### API 키 발급 과정

**1단계: 서비스 가입**

```
OpenWeather 사이트 방문
→ 회원가입
→ API Keys 메뉴
→ 키 생성
→ 복사: "a1b2c3d4e5f6g7h8"
```

**2단계: 함수에서 사용**

```javascript
const apiKey = 'a1b2c3d4e5f6g7h8';

const response = await fetch(
  `https://api.weather.com/data?key=${apiKey}`
);
```

---

## 환경 변수로 API 키 보호하기

### 잘못된 방법

**❌ 코드에 직접 입력:**

```javascript
exports.handler = async () => {
  const apiKey = 'a1b2c3d4e5f6g7h8';  // 위험!
  
  const response = await fetch(
    `https://api.weather.com/data?key=${apiKey}`
  );
};
```

**문제점:**

- GitHub에 코드 올리면 → API 키 노출
- 다른 사람이 보면 → 키 유출
- 키 변경하려면 → 코드 수정 후 재배포

### 올바른 방법: 환경 변수

**✅ 환경 변수 사용:**

```javascript
exports.handler = async () => {
  const apiKey = process.env.WEATHER_API_KEY;  // 안전!
  
  const response = await fetch(
    `https://api.weather.com/data?key=${apiKey}`
  );
};
```

**장점:**

- API 키가 코드에 없음
- GitHub에 올려도 안전
- 키 변경 시 재배포 불필요

### 환경 변수 설정하기

**Netlify Dashboard에서:**

```
1. 내 사이트 선택
2. Site settings
3. Environment variables
4. Add a variable

Key: WEATHER_API_KEY
Value: a1b2c3d4e5f6g7h8

5. Save
```

**환경 변수 사용:**

```javascript
const apiKey = process.env.WEATHER_API_KEY;
// Netlify 서버에 저장된 값을 가져옴
```

### 환경 변수의 위치

**저장 위치:**

```
브라우저: 환경 변수 없음 ❌
GitHub: 환경 변수 없음 ❌
Netlify 서버: 환경 변수 있음 ✅
```

**브라우저는 API 키를 모름 → 안전!**

### 전체 흐름

```
1단계: API 키 발급
  OpenWeather → 키 받음: "a1b2c3d4e5f6g7h8"

2단계: Netlify에 저장
  Dashboard → 환경 변수 추가
  WEATHER_API_KEY = "a1b2c3d4e5f6g7h8"

3단계: 함수에서 사용
  const apiKey = process.env.WEATHER_API_KEY;

4단계: API 요청
  fetch(`...?key=${apiKey}`)

5단계: 서버 검증
  "키 확인... 유효함!" → 데이터 전송
```

### 여러 환경 변수 사용

**Netlify 설정:**

```
WEATHER_API_KEY = abc123
GEMINI_API_KEY = xyz789
MONGODB_URI = mongodb://...
```

**함수 코드:**

```javascript
exports.handler = async () => {
  const weatherKey = process.env.WEATHER_API_KEY;
  const geminiKey = process.env.GEMINI_API_KEY;
  const mongoUri = process.env.MONGODB_URI;
  
  // 여러 API 사용
  const weather = await fetch(`...?key=${weatherKey}`);
  const ai = await fetch(`...?key=${geminiKey}`);
  const db = await MongoClient.connect(mongoUri);
};
```

---

## 보안 체크리스트

### 해야 할 것 ✅

```
✅ 환경 변수 사용 (process.env.API_KEY)
✅ .env 파일을 .gitignore에 추가
✅ GitHub에 환경 변수 업로드 안 함
✅ Netlify Dashboard에 환경 변수 설정
✅ API 키를 정기적으로 변경
```

### 하면 안 되는 것 ❌

```
❌ 코드에 직접 키 입력
❌ .env 파일을 Git에 커밋
❌ 브라우저 코드에 키 노출
❌ 공개 저장소에 키 업로드
❌ 스크린샷에 키 노출
```

---

## 완전한 예시

### 프로젝트 구조

```
my-weather-app/
  ├─ index.html
  ├─ app.js
  ├─ vite.config.js          (개발용)
  └─ netlify/
      └─ functions/
          └─ weather.js       (배포용)
```

### 개발 설정 (vite.config.js)

```javascript
export default {
  server: {
    proxy: {
      '/api': 'https://api.openweathermap.org'
    }
  }
}
```

**의미:** 개발 중에는 Vite Proxy 사용

### 배포용 함수 (netlify/functions/weather.js)

```javascript
const fetch = require('node-fetch');

exports.handler = async (event) => {
  // 환경 변수에서 API 키 가져오기
  const apiKey = process.env.OPENWEATHER_API_KEY;
  
  // 도시 이름 받기
  const city = event.queryStringParameters.city || 'Seoul';
  
  try {
    // 외부 API 호출
    const response = await fetch(
      `https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=${apiKey}`
    );
    
    // 응답 확인
    if (!response.ok) {
      return {
        statusCode: response.status,
        body: JSON.stringify({ error: 'API request failed' })
      };
    }
    
    const data = await response.json();
    
    // 성공 응답
    return {
      statusCode: 200,
      body: JSON.stringify(data)
    };
    
  } catch (error) {
    // 오류 처리
    return {
      statusCode: 500,
      body: JSON.stringify({ error: error.message })
    };
  }
};
```

### 프론트엔드 코드 (app.js)

```javascript
// 개발 중
fetch('/api/data/2.5/weather?q=Seoul')
  .then(res => res.json())
  .then(data => console.log(data));

// 배포 후
fetch('/.netlify/functions/weather?city=Seoul')
  .then(res => res.json())
  .then(data => console.log(data));
```

---

## 핵심 요약

### CORS 해결 방법

```
문제:
  브라우저 → 외부 API (다른 도메인)
  → CORS 오류 ❌

해결:
  브라우저 → 함수 (같은 도메인) → 외부 API
  → 문제 없음 ✅
```

### API 서버 함수의 역할

```
1. 브라우저와 같은 도메인 (CORS 해결)
2. 외부 API에 자유롭게 요청 (서버 환경)
3. API 키 안전하게 보관 (환경 변수)
4. 데이터 통합 및 가공
5. 오류 처리
```

### 보안 관리

```
API 키:
  ❌ 코드에 직접 입력
  ✅ 환경 변수 사용 (process.env.API_KEY)

저장 위치:
  ❌ GitHub
  ✅ Netlify Dashboard
```

### 개발부터 배포까지

```
1. 개발 단계:
   - vite.config.js에 Proxy 설정
   - 빠른 개발과 테스트

2. 배포 준비:
   - Netlify Functions 작성
   - 환경 변수 설정

3. 배포:
   - Git push
   - Netlify 자동 배포
   - CORS 문제 없이 작동
```
