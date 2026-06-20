---
date: 2026-01-07
tags:
  - HTML
  - DOM
  - API
  - API서버
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
## STEP 1. API란 무엇인가?

API(Application Programming Interface)는 서로 다른 소프트웨어가 **정해진 규칙에 따라 데이터를 주고받을 수 있도록 만들어진 접점(체계)**입니다.

식당에 비유하면 이해가 쉽습니다. 손님은 주방에 직접 들어가서 요리를 만들 수 없고, 메뉴판을 보고 웨이터에게 주문하면 웨이터가 주방에 전달하고 완성된 요리를 가져다줍니다. 여기서 손님은 요청을 보내는 프로그램(클라이언트), 주방은 요청을 처리하는 서버, 메뉴판은 어떤 요청이 가능한지 정리한 API 문서, 그리고 이 주문 체계 전체가 API에 해당합니다.

중요한 것은, 주고받는 데이터 자체가 API가 아니라는 점입니다. 메뉴판에 적힌 음식이 API가 아니라, 메뉴판과 웨이터를 포함한 주문 체계 전체가 API입니다.

---

## STEP 2. API에 특별한 문법이 있는가?

별도의 프로그래밍 언어나 특수한 문법 체계가 있는 것은 아닙니다. API는 이미 존재하는 HTTP 통신과 JSON 같은 기술 위에서 동작합니다.

API의 "약속"이란 **"어떤 URL로, 어떤 방식으로, 어떤 데이터를 보내면, 어떤 형태의 응답이 돌아온다"**를 사전에 정해놓은 것입니다. 예를 들어 도서 검색 API의 문서에는 이런 내용이 적혀 있을 것입니다.

```
■ 도서 검색
- URL: https://api.books.com/v1/search
- 방식: GET
- 필수 파라미터: query (검색어)
- 응답 형식: JSON
- 인증: Authorization 헤더에 API 키 포함
```

개발자는 이 약속에 맞춰 자신이 사용하는 프로그래밍 언어로 HTTP 요청을 보내면 됩니다. JavaScript든 Python이든 본질은 동일합니다.

규칙은 두 가지 층위로 나뉩니다. 첫째는 대부분의 웹 API가 따르는 REST라는 통신 방식의 관례(조회는 GET, 생성은 POST, 수정은 PUT, 삭제는 DELETE)이고, 둘째는 카카오, Google 등 각 서비스가 개별적으로 정한 세부 규칙(인증 방식, URL 구조 등)입니다. 후자는 API마다 다르기 때문에 새로운 API를 사용할 때마다 해당 문서를 읽어보아야 합니다.

---

## STEP 3. "호출한다", "제공한다"는 무슨 뜻인가?

API 관련 용어가 어렵게 느껴지는 이유는, 업계에서 API를 마치 능동적인 주체처럼 표현하기 때문입니다. 실제로는 단순한 HTTP 통신을 달리 표현한 것일 뿐입니다.

**"API를 호출한다"**는 내가 상대 서버에 정해진 규격으로 HTTP 요청을 보낸다는 뜻이고, **"API를 제공한다"**는 상대방이 자기 서버에 요청을 받을 수 있는 접점을 열어두었다는 뜻이며, **"API가 응답을 반환한다"**는 상대 서버가 내 요청에 대해 데이터를 돌려보낸다는 뜻입니다.

이처럼 API를 주어로 세운 문장을 만나면, 주어를 "서버" 또는 "클라이언트"로 바꿔 읽으면 훨씬 명확해집니다.

---

## STEP 4. API 서버란 무엇인가?

"API 서버"는 특별한 종류의 서버가 아닙니다. 서버라는 단어가 큰 컴퓨터 장비를 떠올리게 하지만, 실제로 서버란 **특정 포트에서 HTTP 요청을 기다리고 있는 프로그램**입니다. 카카오가 운영하는 수많은 서버 프로그램 중에서, 외부 개발자들이 HTTP 요청을 보낼 수 있도록 접점을 열어둔 것을 "카카오의 API 서버"라고 부르는 것입니다.

그리고 이 서버 프로그램 안에는 **"이 URL로 요청이 들어오면 이 함수를 실행하라"**는 연결 규칙이 정의되어 있습니다. 이것을 라우팅(routing)이라고 합니다.

```javascript
app.get("/weather/current", getCurrentWeather);
app.get("/weather/forecast", getWeeklyForecast);
app.post("/users/signup", createNewUser);
```

`/weather/current`라는 URL로 GET 요청이 들어오면 `getCurrentWeather`라는 함수가 실행됩니다. 이렇게 외부에서 접근할 수 있도록 열어둔 URL 각각을 **엔드포인트(endpoint)**라고 부릅니다. 결국 API 서버란 여러 개의 엔드포인트를 가지고 있고, 각 엔드포인트에 특정 함수가 연결되어 있어서 요청이 오면 해당 함수가 실행되어 결과를 돌려주는 프로그램입니다.

---

## STEP 5. API 키는 무엇인가?

아무나 서버의 함수를 실행할 수 있다면 문제가 되기 때문에, API 키라는 식별 수단을 사용합니다. 요청을 보내는 쪽은 HTTP 요청에 API 키를 함께 포함시키고, 서버는 본래 할 일을 하기 전에 먼저 이 키가 유효한지 확인합니다.

```javascript
// 요청을 보내는 쪽
fetch("https://api.weather.com/current?city=seoul", {
  headers: { "Authorization": "Bearer abc123xyz" }
});

// 서버 쪽 (키 확인 후 처리)
function getCurrentWeather(request) {
  const apiKey = request.headers["Authorization"];
  if (!isValidKey(apiKey)) {
    return { status: 401, message: "유효하지 않은 API 키" };
  }
  return { status: 200, data: lookupWeather(request.query.city) };
}
```

API 키도 특별한 기술이 아니라, 서버 함수 안에서 요청에 포함된 문자열을 꺼내 등록된 값과 비교하는 로직일 뿐입니다. API 키, 검색어, 응답 데이터 모두 HTTP 요청과 응답이라는 하나의 구조 안에서 오가는 데이터이며, 같은 통신 구조 안에서 목적에 따라 역할이 나뉘어 있을 뿐입니다.

---

## 전체 요약

API의 본질은 서버와 클라이언트가 HTTP라는 약속된 방식으로 데이터를 주고받는 것이며, "API 서버", "엔드포인트", "API 키", "호출", "제공" 같은 모든 관련 용어들은 이 단순한 구조의 각 부분을 부르는 이름일 뿐입니다.