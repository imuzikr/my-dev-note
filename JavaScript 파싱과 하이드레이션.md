---
date: 2026-01-22
tags:
  - CSR
  - SSR
  - MPA
  - SPA
  - 자바스크립트파싱
  - 파싱
  - 하이드레이션
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





## JavaScript 파싱과 하이드레이션

**JavaScript 파싱**

JavaScript 파싱은 브라우저가 JavaScript 텍스트 코드를 실행 가능한 내부 구조로 변환하는 과정입니다. 

- 텍스트 형태의 JavaScript 코드를 AST(Abstract Syntax Tree)로 변환합니다
- 문법 오류를 검사합니다
- 바이트코드나 기계어로 컴파일합니다
- 모든 JavaScript 애플리케이션(CSR, SSR 모두)에서 필수적으로 일어나는 과정입니다

**하이드레이션**

하이드레이션은 SSR 환경에서만 일어나는 특별한 과정입니다. JavaScript가 파싱되고 실행된 후에 일어나는 작업으로

- 서버에서 만든 정적 HTML과 클라이언트의 JavaScript를 연결합니다
- 이미 화면에 있는 DOM 요소들에 이벤트 리스너를 부착합니다
- React의 상태 관리 시스템을 초기화합니다
- 가상 DOM을 구성하고 실제 DOM과 매칭합니다

**정확한 순서**

SSR 환경에서의 정확한 순서는 다음과 같습니다:

```
1. HTML 다운로드 → 화면 렌더링 (정적, 상호작용 불가)
   ↓
2. JavaScript 파일 다운로드
   ↓
3. JavaScript 파싱 (텍스트 → AST 변환)
   ↓
4. JavaScript 컴파일 (AST → 바이트코드)
   ↓
5. JavaScript 실행 (React 코드 실행 시작)
   ↓
6. 하이드레이션 (HTML과 JavaScript 연결)
   ↓
7. 상호작용 가능한 상태
```

**구체적인 예시**

서버에서 다음과 같은 HTML을 만들었다고 가정해봅시다:

```html
<button id="cart-button">장바구니 추가</button>
```

사용자는 이 버튼을 볼 수 있지만 클릭해도 아무 일도 일어나지 않습니다.

**파싱 단계**

```javascript
function handleCartClick() {
  addToCart(productId);
}
```

이런 코드를 브라우저가 이해할 수 있는 내부 구조로 변환합니다. 하지만 이 함수가 버튼과 연결되지는 않았습니다.

**하이드레이션 단계** 

React가 실행되면서

1. 화면에 있는 버튼 요소를 찾습니다
2. 그 버튼에 `handleCartClick` 함수를 이벤트 리스너로 연결합니다
3. 이제 버튼을 클릭하면 함수가 실행됩니다

**왜 구분이 중요한가**

파싱은 브라우저 레벨의 기술적 과정이고, 하이드레이션은 React/Vue 같은 프레임워크가 수행하는 애플리케이션 레벨의 작업입니다.

파싱 없이는 JavaScript를 실행할 수 없고, JavaScript 실행 없이는 하이드레이션도 불가능합니다. 하지만 파싱이 완료되었다고 해서 자동으로 하이드레이션이 되는 것은 아닙니다. React가 명시적으로 `hydrateRoot()` 같은 함수를 호출해야 하이드레이션이 시작됩니다.

**CSR에서는 하이드레이션이 없음**

CSR 방식에서는 HTML이 처음부터 거의 비어있고, React가 DOM을 처음부터 생성합니다. 이미 있는 HTML과 연결할 필요가 없으므로 하이드레이션 과정이 없습니다. 대신 `createRoot().render()` 같은 함수를 사용해서 새로 DOM을 만듭니다.

**정리**

- **JavaScript 파싱**: 코드를 실행 가능한 형태로 변환하는 브라우저의 기본 작업 (CSR, SSR 모두 필수)
- **하이드레이션**: 서버에서 만든 HTML에 JavaScript 기능을 연결하는 프레임워크의 특별한 작업 (SSR에만 해당)

둘은 연속적으로 일어나지만, 목적과 수행 주체가 완전히 다른 별개의 과정입니다.

## 기술적 과정


**CSR**
- 빈 HTML 다운로드 → 빈 화면 또는 로딩 스피너만 보임
- JavaScript 파일 다운로드 → 여전히 빈 화면
- JavaScript 파싱 → 여전히 빈 화면
- JavaScript 컴파일 → 여전히 빈 화면
- JavaScript 실행 → 화면에 콘텐츠가 나타남
- 화면 완성(상호작용 가능) → 버튼 클릭, 입력 등 모든 기능 작동

**SSR**
- 완성된 HTML 다운로드 → 화면에 콘텐츠 보임 (상호작용 불가)
- JavaScript 파일 다운로드 → 여전히 상호작용 불가
- JavaScript 파싱 → 여전히 상호작용 불가
- JavaScript 컴파일 → 여전히 상호작용 불가
- JavaScript 실행 → 여전히 상호작용 불가
- 하이드레이션 완료 → 이제 상호작용 가능!