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


## 🏭 Vercel의 역할

### 배포 과정 전체

```
개발자
  ↓ (코드 작성)
route.ts
  ↓ (vercel deploy)
Vercel
  ↓ (변환 + 배포)
서버리스 함수
  ↓ (실행)
사용자에게 응답
```

---

## 🔧 구체적인 변환 과정

### 1단계: 코드 업로드

```
개발자:
vercel deploy

→ route.ts 파일이 Vercel로 전송됨
```

---

### 2단계: Vercel이 분석

```
Vercel: "어떤 파일들이 있나 볼까?"

발견:
✅ app/api/books/route.ts
✅ app/api/users/route.ts
✅ app/books/page.tsx

Vercel: "route.ts 파일들은 API니까
         서버리스 함수로 만들어야겠다!"
```

---

### 3단계: 빌드 (변환)

**입력: route.ts (TypeScript)**

```typescript
// app/api/books/route.ts
export async function GET(request: Request) {
  const books = await db.collection('books').get();
  return Response.json({ books });
}
```

**Vercel의 작업:**

```
1. TypeScript → JavaScript 컴파일
2. 의존성 패키지 번들링
3. 서버리스 함수 래퍼로 감싸기
4. 최적화
```

**출력: 서버리스 함수 (JavaScript)**

```javascript
// Vercel이 생성한 서버리스 함수
export default async function handler(request, context) {
  // route.ts의 GET 함수
  const books = await db.collection('books').get();
  
  return new Response(JSON.stringify({ books }), {
    status: 200,
    headers: {
      'Content-Type': 'application/json'
    }
  });
}
```

---

### 4단계: 배포

```
Vercel: "AWS Lambda에 업로드!"

결과:
┌────────────────────────────────┐
│  AWS Lambda (또는 Vercel Edge) │
│                                │
│  /api/books → handler()        │
│  /api/users → handler()        │
└────────────────────────────────┘
```

---

### 5단계: URL 연결

```
Vercel: "URL 매핑 완료!"

https://myproject.vercel.app/api/books
  ↓
Lambda 함수 (변환된 route.ts)
```

---


## 📦 패키징 과정

### Vercel이 만드는 것

```
route.ts 하나
  ↓ Vercel 변환
서버리스 함수 패키지:
  ├── handler.js (변환된 코드)
  ├── node_modules/ (필요한 라이브러리)
  ├── config.json (설정)
  └── .vercel/
      └── output/
          └── functions/
              └── api/
                  └── books.func/
```

---

## 🌐 배포 아키텍처

### 전체 구조

```
┌─────────────────────────────────────┐
│  개발자 컴퓨터                      │
│                                     │
│  route.ts (TypeScript 코드)        │
└──────────────┬──────────────────────┘
               │
               │ vercel deploy
               ↓
┌─────────────────────────────────────┐
│  Vercel 빌드 서버                   │
│                                     │
│  1. TypeScript → JavaScript         │
│  2. 번들링                          │
│  3. 서버리스 함수로 패키징          │
└──────────────┬──────────────────────┘
               │
               │ 업로드
               ↓
┌─────────────────────────────────────┐
│  AWS Lambda / Vercel Edge           │
│                                     │
│  서버리스 함수 (실행 준비 완료)    │
└──────────────┬──────────────────────┘
               │
               │ 사용자 요청
               ↓
         실행 & 응답
```

---

## 🎯 Vercel이 하는 일 정리

### 1. 인식

```
"route.ts 파일을 발견했다!"
→ API 엔드포인트로 인식
```

### 2. 변환

```
TypeScript → JavaScript
Next.js API → 서버리스 함수 형식
```

### 3. 최적화

```
불필요한 코드 제거
번들 크기 최소화
성능 최적화
```

### 4. 패키징

```
실행에 필요한 모든 것을 하나로
- 코드
- 라이브러리
- 설정
```

### 5. 배포

```
AWS Lambda / Vercel Edge에 업로드
URL 연결
```

### 6. 관리

```
자동 스케일링
모니터링
로그 수집
```

---

## 💡 왜 이렇게 하나?

### 장점

**1. 개발자는 간단하게**

```
개발자: route.ts만 작성
Vercel: 나머지 알아서 처리
```

**2. 자동 최적화**

```
Vercel이 최선의 방법으로 변환
개발자는 신경 쓸 필요 없음
```

**3. 관리 불필요**

```
서버 관리: Vercel이 담당
개발자는 코드에만 집중
```

---


## ✨ 최종 정리

### 정확한 표현

**✅ 완벽한 표현:**

```
"Vercel은 route.ts를 서버리스 함수로 만들어 배포한다"
```

**과정:**

```
route.ts (소스 코드)
  ↓ Vercel이
서버리스 함수로 변환
  ↓ Vercel이
AWS Lambda에 배포
  ↓
사용자가 접근 가능한 API
```

---

### 비유

```
레시피 (route.ts)
  ↓ 식품 공장 (Vercel)이
통조림 (서버리스 함수)로 제조
  ↓ 식품 공장이
마트 (AWS Lambda)에 배포
  ↓
소비자가 구매 가능
```

---

## 🎓 핵심

**Vercel의 역할:**

1. ✅ route.ts를 받는다
2. ✅ 서버리스 함수로 변환한다
3. ✅ AWS Lambda에 배포한다
4. ✅ URL을 연결한다
5. ✅ 자동으로 관리한다

**개발자:**

- route.ts만 작성하면 됨
- 나머지는 Vercel이 알아서!

**사용자:**

- 그냥 URL 접속하면 됨
- 서버리스인지 모름
- 빠르고 안정적으로 작동

