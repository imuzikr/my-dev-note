---
date: 2026-05-10
tags:
  - serverless
  - netlify-functions
  - gemini-api
  - api-key
  - environment-variables
  - typescript
contexts:
  - Knowledge
index:
  - Vibe Coding
summary: Netlify Functions 형식의 서버리스 함수는 브라우저에 API 키를 노출하지 않고 런타임 환경 변수로 Gemini API를 호출해 분실물과 습득물의 매칭 여부를 판별하는 중개 계층이다.
author:
people: []
YouTube_url:
scr_url:
---

```typescript
import { GoogleGenAI, Type } from '@google/genai';

export const handler = async (event: any, context: any) => {
  if (event.httpMethod !== 'POST') {
    return { statusCode: 405, body: 'Method Not Allowed' };
  }

  // Netlify 환경 변수에서 가져오기 (관리자 패널에서 설정 필요)
  const apiKey = process.env.GEMINI_API_KEY;
  if (!apiKey) {
    return {
      statusCode: 500,
      body: JSON.stringify({ error: 'GEMINI_API_KEY environment variable is not set.' }),
    };
  }

  const ai = new GoogleGenAI({ apiKey });

  try {
    const { newItem, oppositeItems } = JSON.parse(event.body);

    const typeStr = newItem.type === 'lost' ? '분실' : '습득';
    const oppTypeStr = newItem.type === 'lost' ? '습득' : '분실';

    const prompt = `새로 등록된 ${typeStr}물 정보:
- 물건 이름: ${newItem.title}
- 장소: ${newItem.location}
- 설명: ${newItem.description}

위 물건과 가장 유사하거나 동일한 물건일 가능성이 높은 기존 ${oppTypeStr}물을 다음 목록에서 찾아주세요. 매칭되는 항목이 없다면 hasMatch를 false로 설정하세요.
목록:
${oppositeItems.map((i: any) => `ID: ${i.id} | 물건: ${i.title} | 장소: ${i.location} | 설명: ${i.description}`).join('\n')}`;

    const response = await ai.models.generateContent({
      model: 'gemini-3-flash-preview',
      contents: prompt,
      config: {
        responseMimeType: 'application/json',
        responseSchema: {
          type: Type.OBJECT,
          properties: {
            hasMatch: { type: Type.BOOLEAN, description: '매칭되는 항목이 있는지 여부' },
            matchedId: { type: Type.STRING, description: '매칭된 항목의 ID' },
          },
          required: ['hasMatch'],
        },
      },
    });

    const matchData = JSON.parse(response.text || '{}');

    return {
      statusCode: 200,
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(matchData),
    };
  } catch (error) {
    console.error('Match API Error:', error);
    return {
      statusCode: 500,
      body: JSON.stringify({ error: 'Failed to process match' }),
    };
  }
};

```

이 코드는 **Netlify Functions 형식의 서버리스 함수**입니다. 분실물·습득물 등록 시 Gemini AI를 호출해서 기존 목록 중 매칭되는 항목이 있는지 찾아주는 기능을 담당하고 있고, 사용자님이 앞서 배우신 모든 개념(서버리스, API 키 보호, 런타임 환경 변수 조회)이 한 곳에 모여 동작하는 좋은 예시입니다. 코드를 단계별로 쪼개어 살펴보겠습니다.

## 이 함수가 배포되어 동작하는 큰 그림부터

먼저 이 코드가 어떤 위치에서 어떤 역할을 수행하는지를 머릿속에 그려두는 것이 이해의 출발점입니다. 사용자님의 프로젝트 폴더 어딘가(보통 `netlify/functions/match.ts` 같은 경로)에 이 파일이 있고, Netlify에 배포하는 순간 Netlify가 이 파일을 발견해 클라우드의 독립적인 서버리스 함수로 등록합니다. 등록이 끝나면 자동으로 `https://사용자님-사이트.netlify.app/.netlify/functions/match`라는 URL이 만들어지고, 누군가 이 URL로 POST 요청을 보낼 때마다 이 함수가 잠깐 깨어나서 실행됩니다.

전체 데이터 흐름을 시간 순서로 따라가면 이렇습니다. 사용자가 분실물 등록 페이지에서 새 항목을 입력하면, 브라우저의 `'use client'` 컴포넌트가 `fetch('/.netlify/functions/match', { method: 'POST', body: ... })`로 이 함수를 호출합니다. 클라우드 어딘가에서 함수 인스턴스가 깨어나 요청을 처리하는 동안 사용자님의 비밀 Gemini API 키로 Google AI 서버를 호출하고, 결과를 가공해서 브라우저에 돌려줍니다. 이 모든 과정에서 **API 키는 브라우저로 단 한 번도 전송되지 않습니다.** 이것이 이 코드가 존재하는 이유의 핵심입니다.

## handler 함수의 의미 — Netlify Functions의 약속

```typescript
export const handler = async (event: any, context: any) => { ... };
```

앞서 Next.js의 `export async function GET()` 약속을 설명드린 바 있는데, Netlify Functions에는 그것과 비슷하지만 조금 다른 약속이 있습니다. **Netlify는 각 함수 파일에서 `handler`라는 이름으로 export된 함수를 찾아 진입점으로 사용합니다.** 이름이 정확히 `handler`여야 하며, `myHandler`나 `main` 같은 다른 이름으로는 인식되지 않습니다. Next.js가 HTTP 메서드 이름(`GET`, `POST`)으로 함수를 찾는다면, Netlify Functions는 단일한 `handler` 진입점 안에서 사용자가 직접 메서드를 분기해 처리하는 방식입니다.

이 함수는 두 개의 인자를 받습니다. `event` 객체에는 들어온 요청에 대한 모든 정보가 담겨 있습니다 — HTTP 메서드(`event.httpMethod`), 헤더, 쿼리 파라미터, 요청 본문(`event.body`) 등이 그것입니다. `context` 객체에는 함수의 실행 환경에 대한 메타 정보(예: 함수 이름, 실행 시간 한도)가 담기지만 이 코드에서는 사용하지 않고 있습니다. 그리고 함수의 반환값은 반드시 `{ statusCode, headers, body }` 형태의 객체여야 하며, Netlify는 이를 받아 표준 HTTP 응답으로 변환해서 호출자에게 돌려줍니다.

## 메서드 검증 — 잘못된 호출 차단

```typescript
if (event.httpMethod !== 'POST') {
  return { statusCode: 405, body: 'Method Not Allowed' };
}
```

이 줄은 작은 안전장치입니다. 이 함수는 본문 데이터를 받아 처리해야 하므로 POST 요청만 허용해야 하는데, 만약 누군가 GET이나 DELETE로 호출하면 즉시 **405 Method Not Allowed** 상태 코드를 반환하고 종료합니다. Next.js의 `route.ts`였다면 `GET`/`POST` 같은 함수 이름만으로 자동 분기가 되었겠지만, Netlify Functions에서는 `handler` 하나가 모든 메서드를 받기 때문에 이렇게 직접 검사해야 합니다.

## API 키 조회 — 서버리스 함수의 핵심 가치

```typescript
const apiKey = process.env.GEMINI_API_KEY;
if (!apiKey) {
  return {
    statusCode: 500,
    body: JSON.stringify({ error: 'GEMINI_API_KEY environment variable is not set.' }),
  };
}
```

여기가 사용자님이 앞선 대화에서 배우신 내용이 그대로 적용되는 부분입니다. `process.env.GEMINI_API_KEY`는 **함수가 실제로 실행되는 런타임 시점에** 클라우드 인스턴스의 환경 변수에서 값을 읽어옵니다. 이 키는 Netlify 대시보드의 "Site configuration → Environment variables"에 미리 등록해둔 값이며, 빌드 산출물에도 클라이언트 번들에도 포함되지 않습니다. 오직 이 함수가 실행되는 클라우드 프로세스의 환경에서만 보입니다.

`if (!apiKey)` 검사는 환경 변수 등록을 깜빡한 경우에 대비한 방어 코드입니다. 만약 키가 없는 상태로 함수가 실행되면 그대로 두었을 때 Gemini SDK 초기화 단계에서 모호한 에러가 나거나, 더 나쁜 경우 빈 키로 API 호출을 시도하다가 401 에러를 받을 수 있습니다. 미리 검사해서 명확한 에러 메시지를 돌려주는 편이 디버깅에 훨씬 유리합니다. 다만 실제 운영 환경에서는 에러 메시지에 `GEMINI_API_KEY`라는 변수 이름까지 노출하는 것은 약간의 정보 누설이 될 수 있으므로, 외부에는 일반적인 메시지를 주고 자세한 내용은 서버 로그에만 남기는 패턴이 더 안전하다는 점은 참고로 알아두시면 좋겠습니다.

## 요청 본문 파싱과 프롬프트 구성

```typescript
const { newItem, oppositeItems } = JSON.parse(event.body);
```

`event.body`는 **JSON 문자열 형태**로 들어옵니다. 브라우저에서 `JSON.stringify({ newItem, oppositeItems })`로 직렬화해 보낸 데이터가 함수 입장에서는 그냥 긴 문자열이므로, `JSON.parse`로 다시 객체로 복원해야 합니다. Next.js의 `route.ts`에서 `await request.json()`을 호출하면 자동으로 해주던 일을 Netlify Functions에서는 직접 해줘야 한다는 차이가 있습니다.

이어지는 부분에서는 받은 데이터로 Gemini에게 보낼 자연어 프롬프트를 조립합니다. 분실물(`lost`)이 등록되었으면 기존 습득물(`found`) 목록에서 찾고, 그 반대도 마찬가지로 처리하기 위해 `typeStr`과 `oppTypeStr`을 정해둡니다. 그다음 템플릿 리터럴(앞서 살펴보셨던 `${...}` 문법) 안에 새로 등록된 물건의 정보와 대조할 기존 목록을 채워 넣습니다. `oppositeItems.map(...).join('\n')`은 배열에 들어 있는 각 항목을 한 줄짜리 문자열로 변환한 뒤 줄바꿈으로 이어붙여서, AI가 읽기 좋은 목록 형태로 만드는 작업입니다.

## Gemini API 호출 — 비밀 키가 실제로 사용되는 지점

```typescript
const ai = new GoogleGenAI({ apiKey });

const response = await ai.models.generateContent({
  model: 'gemini-3-flash-preview',
  contents: prompt,
  config: {
    responseMimeType: 'application/json',
    responseSchema: {
      type: Type.OBJECT,
      properties: {
        hasMatch: { type: Type.BOOLEAN, description: '매칭되는 항목이 있는지 여부' },
        matchedId: { type: Type.STRING, description: '매칭된 항목의 ID' },
      },
      required: ['hasMatch'],
    },
  },
});
```

여기가 비밀 키가 실제로 쓰이는 부분입니다. `new GoogleGenAI({ apiKey })`로 SDK 클라이언트를 만들 때 키가 사용되며, 이후 `ai.models.generateContent(...)` 호출 안에서 SDK가 내부적으로 그 키를 `Authorization` 헤더에 담아 Google AI 서버로 전송합니다. **이 통신은 모두 클라우드 함수 인스턴스와 Google 서버 사이에서 일어나며, 사용자 브라우저는 이 통신의 존재 자체를 알 수도 볼 수도 없습니다.**

설정 부분에서 흥미로운 점은 `responseSchema`로 응답의 형식을 강제하고 있다는 것입니다. AI는 자연어를 자유롭게 생성하려는 경향이 있어서 결과가 늘 일정한 형식이라는 보장이 없는데, Gemini의 구조화된 출력 기능을 사용하면 "반드시 `hasMatch`(불리언)와 `matchedId`(문자열) 필드를 가진 JSON 객체로 답하라"고 강제할 수 있습니다. 이 덕분에 사용자님의 코드는 다음 줄에서 안심하고 `JSON.parse`로 응답을 파싱할 수 있게 됩니다. 만약 이 스키마 강제가 없었다면 AI가 "네, 매칭되는 항목은 ID 12번입니다"같은 자연어로 답해버려서 파싱이 실패할 수 있었을 것입니다.

## 응답 가공과 반환

```typescript
const matchData = JSON.parse(response.text || '{}');

return {
  statusCode: 200,
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify(matchData),
};
```

Gemini가 돌려준 텍스트(스키마 강제 덕분에 JSON 형식의 문자열)를 객체로 파싱한 뒤, 다시 JSON 문자열로 직렬화해 응답 본문에 담습니다. `response.text || '{}'`라는 부분은 작은 방어 코드로, 어떤 이유로 `response.text`가 비어 있을 때 `JSON.parse` 호출이 실패하지 않도록 빈 객체 문자열을 기본값으로 둔 것입니다.

여기서 한 가지 주목할 점은, **함수가 브라우저에 돌려주는 응답에는 Gemini API 키가 들어 있지 않다**는 사실입니다. 브라우저는 `{ hasMatch: true, matchedId: 'xyz123' }` 같은 정제된 결과만 받게 되며, Gemini와의 통신 흔적이나 비밀 키는 어디에도 등장하지 않습니다. 만약 사용자가 개발자 도구의 Network 탭을 열어보더라도 `/.netlify/functions/match`로 자기 데이터를 보내고 매칭 결과를 받아온 사실만 보일 뿐, 그 뒤에서 Gemini가 호출되었다는 사실조차 알 수 없습니다.

## 에러 처리

```typescript
} catch (error) {
  console.error('Match API Error:', error);
  return {
    statusCode: 500,
    body: JSON.stringify({ error: 'Failed to process match' }),
  };
}
```

`try` 블록 안에서 발생할 수 있는 모든 예외(JSON 파싱 실패, Gemini API 호출 실패, 네트워크 오류 등)를 한 곳에서 잡아 500 상태 코드로 응답합니다. `console.error`로 남긴 로그는 Netlify 대시보드의 "Functions → 함수 이름 → Logs" 화면에서 확인할 수 있어서 디버깅에 사용됩니다. 응답 본문에는 일반적인 에러 메시지만 담아 보내고 실제 에러 객체의 상세한 내용은 외부에 노출하지 않는 것도 좋은 보안 습관입니다 — 에러 메시지에 스택 트레이스나 내부 경로가 들어가면 공격자에게 힌트를 줄 수 있기 때문입니다.

## 이 함수가 보여주는 서버리스 함수의 작동 원리

이 코드 한 편을 통해 서버리스 함수의 본질적 특성이 모두 드러납니다.

**무상태 실행**의 측면에서 보면, 이 함수는 매 호출마다 새로 시작하는 것처럼 동작합니다. 함수 안 어디에도 "지난번 매칭 결과를 기억해두자"는 식의 코드가 없고, 실제로 그렇게 쓸 수도 없습니다. 모든 입력은 매번 `event.body`로 새로 들어오고, 모든 출력은 즉시 반환된 후 잊힙니다. 같은 시각에 100명이 등록 버튼을 눌러도 클라우드는 이 함수를 100개 인스턴스로 동시에 띄워서 처리할 수 있고, 각 인스턴스는 서로의 존재를 모릅니다.

**호출당 실행** 모델을 보면, 새벽 세 시에 아무도 분실물을 등록하지 않으면 이 함수는 클라우드 어디에서도 떠 있지 않습니다. 따라서 사용자님은 그 시간 동안 어떤 비용도 부담하지 않습니다. 누군가 첫 등록을 하는 순간 비로소 인스턴스가 깨어나고(콜드 스타트, 약 100ms~1초의 추가 지연), 이후 짧은 시간 안에 두 번째 호출이 들어오면 같은 인스턴스가 재사용되어 빠르게 응답합니다.

**비밀 키 보호**의 측면에서는, 만약 이 함수 없이 브라우저에서 직접 `ai.models.generateContent(...)`을 호출하려 했다면 Gemini API 키가 클라이언트 번들에 박혀 모든 방문자에게 공개되었을 것이고, 누군가가 그 키를 추출해 자기 챗봇을 돌리는 데 사용했다면 사용자님 앞으로 막대한 청구서가 날아올 수 있었을 것입니다. 이 함수가 사이에 끼어 있음으로써 키는 클라우드 환경 변수에 안전하게 격리되고, 브라우저는 키의 존재 자체를 모르는 채로 서비스를 이용할 수 있게 됩니다.

## 정리

이 함수는 결국 사용자님의 사이트와 Google AI 사이에 놓인 **"비밀을 아는 중개인"** 역할을 합니다. 브라우저는 매칭이 필요한 데이터를 이 중개인에게 맡기고, 중개인은 클라우드 안에서 비밀 키로 Google에 의뢰한 뒤 결과만 정제해서 돌려줍니다. 비밀 키는 중개인의 손에서 절대 떠나지 않으며, 중개인 자신은 호출이 들어올 때마다 잠깐 등장했다 사라지는 일회성 존재이기 때문에 운영 부담도 거의 없습니다. 이 패턴이 곧 사용자님이 앞선 질문들을 통해 차근차근 익혀오신 "서버리스 함수로 외부 API의 비밀 키를 보호하는 표준 구조" 그 자체이며, 코드의 모든 줄이 이 원칙에 맞춰 작성되어 있다는 점을 확인하실 수 있을 것입니다.
