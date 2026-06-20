---
date: 2025-12-18
tags:
  - "#DOM"
type: knowledge
contexts:
index:
  - "[[🎶Web Development]]"
  - "[[🔆인공지능]]"
YouTube:
scr_url:
scr:
---



# DOM(Document Object Model)의 이해

웹 애플리케이션 개발에서 DOM은 HTML 문서를 프로그래밍적으로 조작할 수 있게 해주는 핵심 인터페이스입니다. DOM의 본질과 작동 원리를 체계적으로 살펴보겠습니다.

## DOM의 본질과 구조

DOM은 HTML 문서를 트리 구조의 객체 모델로 표현한 것입니다. 브라우저가 HTML 파일을 읽으면, 각 태그와 텍스트를 노드(node)라는 객체로 변환하여 메모리에 트리 형태로 저장합니다. 이 과정을 통해 정적인 텍스트 문서가 프로그래밍 언어로 접근 가능한 살아있는 객체 구조로 변환됩니다.

예를 들어, 다음과 같은 간단한 HTML이 있다고 가정해보겠습니다.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>예제 페이지</title>
  </head>
  <body>
    <h1>제목입니다</h1>
    <p class="intro">첫 번째 문단입니다.</p>
  </body>
</html>
```

브라우저는 이 HTML을 파싱하여 다음과 같은 트리 구조를 생성합니다. 최상위에는 Document 객체가 있고, 그 아래 html 요소 노드가 있으며, html은 head와 body라는 자식 노드를 가집니다. 각 태그는 Element 노드가 되고, 텍스트 내용은 Text 노드가 됩니다. 이러한 계층 구조 덕분에 부모-자식-형제 관계를 통해 문서의 모든 요소에 접근할 수 있습니다.

## DOM의 작동 원리

브라우저가 웹 페이지를 로드하는 과정은 여러 단계로 이루어집니다. 먼저 HTML 파싱 단계에서 브라우저의 HTML 파서가 문서를 위에서 아래로 읽으면서 토큰화를 수행합니다. 각 태그, 속성, 텍스트를 토큰으로 분리한 후, 이 토큰들을 기반으로 DOM 트리를 구성합니다.

DOM 트리 구축이 완료되면, CSS 파싱을 통해 CSSOM(CSS Object Model)이 생성됩니다. DOM과 CSSOM이 결합되어 렌더 트리가 만들어지며, 이를 기반으로 화면에 실제로 그려질 요소들의 위치와 크기가 계산됩니다. 마지막으로 페인팅 과정을 거쳐 사용자에게 실제 화면이 표시됩니다.

중요한 점은 JavaScript가 DOM을 조작하면 이 전체 과정이 부분적으로 또는 전체적으로 다시 실행된다는 것입니다. 이를 리플로우(reflow)와 리페인트(repaint)라고 부르며, 성능 최적화에서 중요하게 다루어지는 개념입니다.

## JavaScript를 통한 DOM 조작

JavaScript는 DOM API를 통해 문서의 모든 요소에 접근하고 수정할 수 있습니다. 가장 기본적인 조작 방법들을 살펴보겠습니다.

요소를 선택하는 방법으로는 `document.getElementById('id')`로 특정 ID를 가진 요소를 찾거나, `document.querySelector('.intro')`로 CSS 선택자를 사용할 수 있습니다. 여러 요소를 선택할 때는 `document.querySelectorAll('p')`를 사용하면 NodeList를 반환받습니다.

요소의 내용을 변경하는 예시를 보겠습니다.

```javascript
const heading = document.querySelector('h1');
heading.textContent = '새로운 제목'; // 텍스트만 변경
heading.innerHTML = '<strong>강조된</strong> 제목'; // HTML 포함 가능
```

새로운 요소를 생성하고 추가하는 과정은 다음과 같습니다.

```javascript
const newParagraph = document.createElement('p');
newParagraph.textContent = '새로 추가된 문단입니다.';
newParagraph.classList.add('highlight');
document.body.appendChild(newParagraph);
```

이 코드는 새로운 p 요소를 메모리에 생성하고, 텍스트 내용을 설정한 후, CSS 클래스를 추가하고, 마지막으로 body의 자식으로 추가합니다. 이 순간 브라우저는 DOM 트리를 업데이트하고 화면을 다시 렌더링합니다.

## 이벤트와 DOM의 상호작용

DOM의 강력한 기능 중 하나는 이벤트 시스템입니다. 사용자의 클릭, 키보드 입력, 마우스 이동 등 모든 상호작용이 이벤트로 처리됩니다.

이벤트는 캡처링과 버블링이라는 두 단계로 전파됩니다. 사용자가 버튼을 클릭하면, 이벤트는 먼저 Document에서 시작하여 대상 요소까지 내려갑니다(캡처링 단계). 그 후 대상 요소에서 다시 Document로 올라갑니다(버블링 단계). 이러한 메커니즘 덕분에 부모 요소에서 자식 요소들의 이벤트를 한 번에 처리하는 이벤트 위임 패턴이 가능합니다.

```javascript
document.querySelector('button').addEventListener('click', function(event) {
  console.log('버튼이 클릭되었습니다.');
  event.target.style.backgroundColor = 'blue';
});
```

이 예시에서 `event.target`은 실제로 클릭된 요소를 가리키며, 이를 통해 동적으로 스타일을 변경할 수 있습니다.

## Virtual DOM과 현대적 접근

전통적인 DOM 조작의 주요 문제는 성능입니다. DOM 조작은 상대적으로 비용이 큰 작업이며, 특히 많은 요소를 동시에 변경할 때 브라우저의 리플로우와 리페인트가 반복적으로 발생하면 성능 저하가 심각해집니다.

React, Vue 같은 현대 프레임워크들은 Virtual DOM이라는 개념을 도입했습니다. Virtual DOM은 실제 DOM의 가벼운 복사본으로, JavaScript 객체로만 존재합니다. 상태가 변경되면 먼저 Virtual DOM에서 변경사항을 계산하고, 변경이 필요한 부분만 실제 DOM에 반영합니다. 이를 통해 불필요한 DOM 조작을 최소화하여 성능을 크게 개선합니다.

예를 들어, 100개의 항목을 가진 리스트에서 하나의 항목만 변경되었다면, 전통적인 방식은 전체 리스트를 다시 렌더링할 수 있지만, Virtual DOM 방식은 변경된 하나의 항목만 실제 DOM에서 업데이트합니다.

## DOM 조작 시 성능 최적화 고려사항

DOM 조작의 성능을 최적화하기 위해서는 몇 가지 원칙을 따라야 합니다. 첫째, DOM 쿼리를 반복문 안에서 실행하지 말고 결과를 변수에 저장해서 재사용해야 합니다. 둘째, 여러 요소를 추가할 때는 DocumentFragment를 사용하여 한 번에 추가하는 것이 효율적입니다.

```javascript
const fragment = document.createDocumentFragment();
for (let i = 0; i < 100; i++) {
  const li = document.createElement('li');
  li.textContent = `항목 ${i}`;
  fragment.appendChild(li);
}
document.querySelector('ul').appendChild(fragment);
```

이 방식은 100번의 개별적인 DOM 조작 대신 단 한 번의 추가 작업으로 모든 요소를 삽입하므로 리플로우를 최소화합니다.

셋째, 레이아웃에 영향을 주는 스타일 변경은 한꺼번에 처리하고, 가능하면 CSS 클래스를 추가/제거하는 방식으로 스타일을 변경하는 것이 개별 스타일 속성을 변경하는 것보다 효율적입니다.

DOM은 웹 개발의 가장 기초적이면서도 강력한 개념입니다. 브라우저가 문서를 어떻게 이해하고 처리하는지 이해하면, 더 효율적이고 성능이 좋은 웹 애플리케이션을 개발할 수 있습니다.

