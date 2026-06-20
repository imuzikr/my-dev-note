---
date: 2025-12-20
tags:
  - 비동기
  - Promise
  - JavaScript
  - vibe_coding
  - catch
  - async
  - await
  - try
type: knowledge
contexts:
  - "[[🏷️Vibe Coding]]"
index:
  - "[[🎶Web Development]]"
NotebookLM:
  - https://notebooklm.google.com/notebook/5add1275-0bf7-405a-ade2-48afa7e2675e
YouTube:
scr_URL:
scr:
share_link: https://share.note.sx/xguqc7wi#EbSCaqycDUMnXUpAzlXcTlGz6LARJ80ALwqVY7TJo/A
share_updated: 2025-12-21T01:05:40+09:00
---


# 🔍 코드에서 "Promise" 단번에 찾아내는 법


![](https://i.imgur.com/TN2pgkT.jpeg)


### 💡 들어가기 전: Promise란?
**Promise는 한마디로 "진동벨"입니다.**
식당에서 주문하고 받은 진동벨은 "지금 당장 음식은 없지만, 준비되면 알려줄게!"라는 약속이죠. 코드에서 진동벨(Promise)이 쓰이는 곳을 찾아봅시다.

---

### 1️⃣ 단서 하나: 뒤에 붙은 `.then()`
코드 끝에 `.then()`이 붙어 있다면 100% Promise입니다.
"이게 끝나면(then) 다음 걸 해!"라는 뜻입니다.

*   **코드 모습:** `언젠가끝날작업().then(결과 => { ... })`
*   **해석:** "진동벨이 울리면(작업 완료) 음식을 가져와라!"

### 2️⃣ 단서 둘: 뒤에 붙은 `.catch()`
코드 끝에 `.catch()`가 보인다면 이것도 Promise입니다.
"만약 작업하다가 문제가 생기면 여기서 잡아내!"라는 뜻입니다.

*   **코드 모습:** `주문하기().catch(에러 => { ... })`
*   **해석:** "음식 재료가 떨어졌으면(에러 발생) 손님에게 사과해라!"

### 3️⃣ 단서 셋: 마법의 단어 `await`
코드 줄 맨 앞에 `await`가 써져 있다면, 그 뒤에 오는 것은 무조건 Promise입니다.
"이 작업(진동벨)이 완료될 때까지 잠시 여기서 멈춰서 기다려!"라는 뜻입니다.

*   **코드 모습:** `const 결과 = await 서버에서불러오기();`
*   **해석:** "음식이 나올 때까지(await) 여기서 기다렸다가 받아서 자리에 앉아라!"

### 4️⃣ 단서 넷: 데이터 통신 `fetch()`
웹에서 데이터를 가져오는 함수인 `fetch()`는 그 자체가 Promise라는 진동벨을 뱉어냅니다.

*   **코드 모습:** `fetch("https://...");`
*   **해석:** "서버에 데이터 달라고 주문을 넣고 진동벨을 받아와라!"


### 🖼️ 그림으로 비교하기

| 일반 코드 (동기) | Promise 코드 (비동기) |
| :--- | :--- |
| **"바로 실행"** | **"시간이 걸리는 약속"** |
| `const 명단 = 이름들;` | `const 명단 = await fetch(주소);` |
| `인사하기();` | `인사하기().then(...);` |
| 결과가 바로 나옴 | 결과가 나올 때까지 기다려야 함 |


### 📝 실전 연습! (아래 코드에서 Promise는 어디일까요?)

```javascript
// 1번 줄
console.log("시작!");

// 2번 줄 (여기에 Promise가 있네요!)
const response = await fetch("/api/data");

// 3번 줄
const data = await response.json();

// 4번 줄 (여기도 .then이 있으니 Promise 처리네요!)
보여주기(data).then(() => {
  console.log("완료!");
});
```

**정답:** 2번, 3번, 4번 줄이 모두 Promise와 관련된 코드입니다!

---

### 📌 요약
코드를 훑어볼 때 이 **4가지 키워드**만 기억하세요!
1.  **`.then`** : "끝나면 이거 해"
2.  **`.catch`** : "안 되면 이거 해"
3.  **`await`** : "기다려"
4.  **`fetch`** : "데이터 주문하기"

