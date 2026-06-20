---
date: 2026-01-29
tags:
  - vibe_coding
  - API서버
  - CORS
  - 요청
  - 라우터
  - Vercel
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

## 🔄 Vercel vs Netlify 비교

### 공통점: 둘 다 서버리스로 배포

```
개발자
  ↓
route.ts 작성
  ↓
배포 플랫폼 (Vercel 또는 Netlify)
  ↓
서버리스 함수로 변환 & 배포
  ↓
사용자에게 서비스
```

---

## 🏗️ Netlify의 배포 과정

### 1단계: 코드 업로드

```bash
netlify deploy
```

**Netlify의 반응:**

```
"route.ts 파일을 발견했어!"
"Next.js 프로젝트구나!"
"API 경로들을 서버리스 함수로 만들어야겠다!"
```

---

### 2단계: 빌드 & 변환

**입력: route.ts**

```typescript
// app/api/books/route.ts
export async function GET(request: Request) {
  const books = await db.collection('books').get();
  return Response.json({ books });
}
```

**Netlify의 작업:**

```
1. Next.js 빌드 실행
2. route.ts → Netlify Function으로 변환
3. AWS Lambda 호환 형식으로 패키징
```

**출력: Netlify Function**

```javascript
// Netlify가 생성
exports.handler = async (event, context) => {
  // route.ts의 로직
  const books = await db.collection('books').get();
  
  return {
    statusCode: 200,
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({ books })
  };
};
```

---

### 3단계: 배포

```
Netlify: "AWS Lambda에 배포 완료!"

https://myproject.netlify.app/api/books
  ↓
AWS Lambda 함수
```

---

## 📊 세부 비교

### Vercel

**기술 스택:**

```
- Vercel Edge Functions (자체 기술)
- 또는 AWS Lambda
- V8 Isolates 사용 가능
```

**변환 형식:**

```javascript
// Vercel 스타일
export default async function handler(req, res) {
  const books = await getBooks();
  return new Response(JSON.stringify({ books }));
}
```

**특징:**

- Next.js 만든 회사
- Next.js에 최적화
- Edge 네트워크 강점
- 더 빠른 Cold Start

---

### Netlify

**기술 스택:**

```
- Netlify Functions
- AWS Lambda 기반
- Node.js 런타임
```

**변환 형식:**

```javascript
// Netlify 스타일
exports.handler = async (event, context) => {
  const books = await getBooks();
  return {
    statusCode: 200,
    body: JSON.stringify({ books })
  };
};
```

**특징:**

- 정적 사이트 호스팅 강점
- 다양한 프레임워크 지원
- 간단한 설정
- 무료 티어 넉넉함

---

## 🎬 실제 배포 로그 비교

### Vercel 배포

```bash
$ vercel deploy

🔍 Inspecting...
   Next.js detected
   
📦 Building...
   ✓ app/api/books/route.ts
     → Serverless Function
   
🚀 Deploying...
   ✓ Edge Functions: 3
   ✓ Serverless Functions: 5
   
✅ https://myproject.vercel.app
```

---

### Netlify 배포

```bash
$ netlify deploy

🔍 Analyzing...
   Next.js site detected
   
📦 Building...
   ✓ app/api/books/route.ts
     → Netlify Function
   
🚀 Deploying...
   ✓ Functions: 5 created
   ✓ Edge Functions: 0
   
✅ https://myproject.netlify.app
```

---

## 🏢 최종 배포 구조

### Vercel 배포 후

```
┌───────────────────────────────┐
│  Vercel Edge Network          │
│                               │
│  ┌─────────────────────────┐ │
│  │ Serverless Functions    │ │
│  │                         │ │
│  │ /api/books → Lambda     │ │
│  │ /api/users → Lambda     │ │
│  └─────────────────────────┘ │
│                               │
│  CDN + Edge Locations         │
└───────────────────────────────┘
```

---

### Netlify 배포 후

```
┌───────────────────────────────┐
│  Netlify Platform             │
│                               │
│  ┌─────────────────────────┐ │
│  │ Netlify Functions       │ │
│  │ (AWS Lambda)            │ │
│  │                         │ │
│  │ /api/books → Function   │ │
│  │ /api/users → Function   │ │
│  └─────────────────────────┘ │
│                               │
│  CDN + Global Network         │
└───────────────────────────────┘
```

---

## 🔍 차이점

### 1. 함수 이름

**Vercel:**

```
✓ Serverless Functions
✓ Edge Functions (선택사항)
```

**Netlify:**

```
✓ Netlify Functions
✓ Edge Functions (선택사항)
```

---

### 2. 설정 파일

**Vercel:**

```json
// vercel.json
{
  "functions": {
    "api/**/*.ts": {
      "memory": 1024,
      "maxDuration": 10
    }
  }
}
```

**Netlify:**

```toml
# netlify.toml
[functions]
  directory = ".netlify/functions"
  node_bundler = "esbuild"

[[redirects]]
  from = "/api/*"
  to = "/.netlify/functions/:splat"
  status = 200
```

---

### 3. 배포 위치

**Vercel:**

```
주로:
- Vercel Edge (자체 인프라)
- AWS Lambda (일부)
```

**Netlify:**

```
주로:
- AWS Lambda
- Google Cloud Functions (일부)
```

---

### 4. Cold Start 성능

**Vercel:**

```
Edge Functions: ~50ms
Serverless Functions: ~100-300ms
```

**Netlify:**

```
Functions: ~100-500ms
(AWS Lambda 기반)
```

---

## 🎯 핵심: 거의 동일!

### 공통 프로세스

```
1️⃣ route.ts 코드 작성
   └─ 개발자가 작성

2️⃣ 플랫폼에 업로드
   └─ git push 또는 deploy 명령

3️⃣ 서버리스 함수로 변환
   └─ Vercel/Netlify가 자동 처리

4️⃣ 클라우드에 배포
   └─ AWS Lambda 등

5️⃣ URL 연결
   └─ /api/books 접근 가능

6️⃣ 사용자 요청 처리
   └─ 서버리스 함수 실행
```

---

## 💰 비용 비교

### Vercel

```
무료 티어:
- 100GB 대역폭
- 100시간 실행 시간
- 12개 서버리스 함수

Pro ($20/월):
- 1TB 대역폭
- 1000시간 실행 시간
- 무제한 함수
```

---

### Netlify

```
무료 티어:
- 100GB 대역폭
- 125,000 함수 호출/월
- 300분 빌드 시간

Pro ($19/월):
- 400GB 대역폭
- 2,000,000 함수 호출/월
- 300분 빌드 시간
```

---

## 🤔 어느 것을 선택할까?

### Vercel을 선택하면

**장점:**

```
✅ Next.js에 최적화 (같은 회사)
✅ 더 빠른 Edge Functions
✅ 최신 기능 빠르게 지원
✅ 개발 경험 우수
```

**단점:**

```
❌ 가격이 약간 비쌈
❌ Next.js 외 지원 제한적
```

---

### Netlify를 선택하면

**장점:**

```
✅ 무료 티어 넉넉함
✅ 다양한 프레임워크 지원
✅ 간단한 설정
✅ 좋은 문서화
```

**단점:**

```
❌ Next.js 최적화는 Vercel보다 약함
❌ Edge Functions 제한적
```

---

## ✨ 결론

### 핵심 답변

**질문: "Netlify는 다른가요?"**

**답변: 거의 같습니다!**

**공통점:**

```
✅ 둘 다 route.ts를 서버리스 함수로 변환
✅ 둘 다 자동으로 배포
✅ 둘 다 AWS Lambda 사용 (기본)
✅ 둘 다 자동 스케일링
✅ 둘 다 사용량 기반 과금
```

**차이점:**

```
Vercel:
- Next.js 전문
- 자체 Edge 네트워크
- 약간 더 빠름
- 약간 더 비쌈

Netlify:
- 정적 사이트 전문
- AWS Lambda 기반
- 무료 티어 넉넉
- 다양한 프레임워크
```

---

### 비유

```
Vercel = BMW (성능 특화)
Netlify = 현대 (가성비 좋음)

둘 다:
✅ 자동차 (서버리스 플랫폼)
✅ 목적지까지 데려다줌 (배포)
✅ 안전하고 편리함

차이:
- 브랜드가 다를 뿐
- 기본 원리는 동일
```

---

### 최종 정리

**route.ts 관점에서:**

```
개발자: route.ts 작성
  ↓
Vercel 또는 Netlify: "알아서 할게요!"
  ↓
서버리스 함수로 변환 & 배포
  ↓
사용자: 빠르게 접근 가능

→ 어느 것을 쓰든 결과는 거의 동일!
```

**핵심:**

- 둘 다 route.ts를 서버리스 함수로 만들어줌
- 방식만 살짝 다를 뿐
- 개발자는 route.ts만 신경 쓰면 됨!

선택은 프로젝트 요구사항과 예산에 따라 하면 됩니다! 🎯
