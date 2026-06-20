---
date: 2026-05-19
tags:
contexts:
index:
summary:
author:
people:
YouTube_url:
scr_url:
---
전체 내용을 자연스럽게 이어지는 서술형 요약으로 정리해서 summary 속성에 입력해 주세요. 목록, 제목, “이 노트는…” 같은 메타 설명 없이 바로 핵심 내용만 쓰고 3~4문장으로 구성되도록 작성하고 단순한 요약문의 나열이 아니라 내용의 흐름이 자연스럽게 연결되도록 해야 합니다.
## GitHub Actions로 HTML 아티클 자동 배포하기

#### 전체 그림 먼저 보기
![GitHub Pages.png](file:///G:/%EB%82%B4%20%EB%93%9C%EB%9D%BC%EC%9D%B4%EB%B8%8C/Obsidian.library/images/MPBKVU4FC4JO6.info/GitHub%20Pages.png)

이 프로젝트는 **HTML로 작성한 글(아티클)을 웹에 자동으로 발행하는 시스템**이다. 글을 정해진 폴더에 넣기만 하면, 나머지는 자동으로 처리된다. 목차 페이지가 만들어지고, 인터넷에 공개되는 것까지 사람이 추가로 할 일이 없다.

블로그 플랫폼(네이버 블로그, 티스토리 등)을 직접 만든 것과 비슷한데, 외부 서비스에 의존하지 않고 **GitHub**라는 개발자 도구 위에서 모든 것을 운영한다는 차이가 있다.

---

#### 프로젝트 폴더 구조

my-html/  
├── .claude ← Claude Code 설정  
├── .github/workflows/deploy.yml ← 자동 배포 규칙 (GitHub Actions)  
├── scripts/generate_index.py ← 목차 페이지 생성 스크립트  
├── articles/  
│ └── 2026-05-19-ontology.html ← 아티클 (글)  
├── design-system.html ← 디자인 규칙 참고 파일  
├── [html-skill-for-claude-ai.md](http://html-skill-for-claude-ai.md/) ← Claude에게 주는 글 작성 지시문  
└── (index.html) ← 자동 생성되는 목차 페이지



---

#### 각 파일이 하는 일

##### `deploy.yml` — 자동화의 두뇌

> [!info] 위치
> `.github/workflows/deploy.yml`

GitHub Actions에게 **"언제, 무엇을, 어떻게 실행할지"** 알려주는 설계도이다. 이 파일이 `.github/workflows/` 폴더 안에 존재하는 것만으로 자동화가 등록된다. 별도로 활성화 버튼을 누를 필요가 없다.

이 파일에 담긴 핵심 지시:

| 항목 | 내용 |
|------|------|
| **언제?** | `main` 브랜치에 코드가 푸시되면 |
| **어디서?** | GitHub가 제공하는 클라우드 서버(Ubuntu)에서 |
| **무엇을?** | Python 스크립트를 실행하고, 결과를 웹에 배포 |

---

##### `generate_index.py` — 목차 자동 생성기

> [!info] 위치
> `scripts/generate_index.py`

`articles/` 폴더를 스캔해서 안에 있는 HTML 파일 목록을 모두 수집한 뒤, 각 글로 연결되는 링크가 포함된 `index.html`을 자동으로 만들어낸다.

> [!important] 핵심 포인트
> 기존 `index.html`에 링크를 하나 추가하는 것이 아니라 **매번 처음부터 새로 생성**한다. 글을 추가하든, 삭제하든, 파일명을 바꾸든, 항상 그 시점의 최신 상태가 반영된다. 단, 페이지의 디자인과 구성은 스크립트 안에 고정되어 있으므로 달라지는 건 아티클 링크 목록뿐이다.

---

##### `articles/` 폴더 — 글이 저장되는 곳

실제 아티클 HTML 파일이 들어가는 폴더이다. 새 글을 발행하고 싶으면 이 폴더에 HTML 파일을 넣으면 된다. 파일명은 보통 `2026-05-19-ontology.html`처럼 날짜와 주제를 조합해서 짓는다.

---

##### `design-system.html` — 디자인 규칙서

아티클의 색상, 폰트, 간격, 카드 모양 같은 **시각 디자인 규칙**이 정의된 참고 파일이다. 새 글을 만들 때 이 파일을 기준으로 삼으면 모든 아티클의 디자인이 통일된다.

---

##### `html-skill-for-claude-ai.md` — Claude를 위한 글 작성 지시문

Claude에게 HTML 아티클을 만들어달라고 요청할 때 사용하는 **작성 규칙서**이다. `html report ○○○`처럼 명령하면, Claude가 이 규칙에 맞춰 디자인 시스템을 적용한 완성도 높은 HTML을 만들어준다.

> [!tip] 두 파일의 관계
> `design-system.html`은 **"어떤 모양으로 만들지"** 를 정의하고, `html-skill-for-claude-ai.md`는 **"어떤 내용 구성으로 만들지"** 를 정의한다. 잡지로 비유하면, 하나는 아트디렉터의 디자인 매뉴얼이고, 다른 하나는 편집장의 기사 작성 가이드이다.

---

#### 새 글이 발행되는 전체 과정

##### 1단계: 글 작성

Claude에게 `html report ○○○`라고 요청하면, 디자인 규칙에 맞는 HTML 파일이 만들어진다. 또는 직접 HTML을 작성해도 된다.

##### 2단계: 새 브랜치에서 작업

글을 바로 `main` 브랜치에 넣지 않고, **별도의 브랜치**(예: `article/ai-ethics`)를 만들어서 작업한다. 이렇게 하면 미완성 글이 실수로 배포되는 것을 막을 수 있다.

##### 3단계: articles/ 폴더에 파일 추가

만들어진 HTML 파일을 `articles/` 폴더에 넣고 커밋한다. 이 시점에서는 아직 배포되지 않는다.

##### 4단계: Pull Request → main에 머지

글이 완성되면 GitHub에서 Pull Request(PR)를 만들어 `main` 브랜치에 머지한다. 머지되는 순간 `main`에 푸시 이벤트가 발생한다.

##### 5단계: 자동 배포

> [!success] 이 단계부터는 사람이 할 일이 없다

```

main에 머지됨 (push 이벤트 발생)  
↓  
GitHub Actions가 deploy.yml을 읽고 워크플로 시작  
↓  
generate_index.py가 articles/ 폴더를 스캔  
↓  
새 글 링크가 포함된 index.html을 자동 생성  
↓  
GitHub Pages로 배포 완료  
↓  
[https://username.github.io/my-html/](https://username.github.io/my-html/) 에서 확인 가능

```

---

###### 배포를 위해 필요한 GitHub 설정

자동 배포가 작동하려면 두 가지 설정이 필요하다.

> [!warning] 이 설정이 빠지면 배포가 실패한다

**① Settings → Pages → Source**
- **"GitHub Actions"** 로 선택한다
- GitHub에게 "배포는 워크플로가 처리한다"고 알려주는 것이다

**② Settings → Environments → github-pages**
- `main` 브랜치의 배포를 허용한다
- 이 설정이 빠져 있으면 `Branch "main" is not allowed to deploy` 에러가 발생한다

---

#### 한 문장 요약

> [!quote] 핵심
> **글을 `articles/` 폴더에 넣고 main에 머지하면, 나머지는 GitHub Actions가 알아서 목차를 만들고 웹에 배포해준다.**


