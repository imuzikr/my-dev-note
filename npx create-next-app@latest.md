---
date: 2025-11-05
tags:
  - npx
  - npm
  - init
  - git
  - shadcn
  - vibe_coding
  - React
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


`npx create-next-app@latest`는 Next.js 프로젝트를 생성하는 명령어입니다. 이 명령어를 실행하면 Next.js 애플리케이션 개발에 필요한 모든 기본 설정과 파일 구조가 자동으로 구성됩니다. 각 부분을 자세히 살펴보겠습니다.

## npx의 역할

`npx`는 Node Package Execute의 약자로, npm 패키지를 설치하지 않고도 실행할 수 있게 해주는 도구입니다. npm 5.2.0 버전부터 기본으로 포함되어 있습니다.

일반적으로 명령어 도구를 사용하려면 먼저 전역으로 설치해야 합니다. 예를 들어 `npm install -g create-next-app`으로 설치한 후 `create-next-app` 명령어를 실행하는 방식입니다. 하지만 npx를 사용하면 이런 설치 과정 없이 바로 실행할 수 있습니다.

npx는 명령어를 실행할 때 임시로 패키지를 다운로드하고, 실행이 끝나면 자동으로 정리합니다. 이는 다음과 같은 장점을 제공합니다. 첫째, 컴퓨터에 불필요한 전역 패키지가 쌓이지 않습니다. 둘째, 항상 최신 버전을 사용할 수 있습니다. 셋째, 프로젝트 생성 같은 일회성 작업에 매우 적합합니다.

## create-next-app의 기능

`create-next-app`은 Next.js 팀에서 공식적으로 제공하는 프로젝트 생성 도구입니다. React의 `create-react-app`과 유사한 역할을 하지만, Next.js에 특화되어 있습니다.

이 도구가 수행하는 작업들을 구체적으로 살펴보면, 먼저 프로젝트 디렉토리 구조를 생성합니다. Next.js의 파일 기반 라우팅 시스템에 필요한 `app` 또는 `pages` 디렉토리, 정적 파일을 위한 `public` 디렉토리 등이 자동으로 만들어집니다.

다음으로 필수 의존성 패키지들을 설치합니다. `next`, `react`, `react-dom` 같은 핵심 패키지들이 `package.json`에 추가되고 자동으로 설치됩니다. TypeScript를 선택했다면 관련 타입 정의 패키지들도 함께 설치됩니다.

또한 설정 파일들도 생성됩니다. `next.config.js`는 Next.js 프레임워크 설정을, `tsconfig.json`은 TypeScript 설정을, `tailwind.config.js`는 Tailwind CSS 설정을 담당합니다.

## @latest의 의미

`@latest`는 npm 버전 지정자입니다. 이는 create-next-app 패키지의 최신 안정 버전을 사용하겠다는 의미입니다.

만약 `@latest`를 생략하고 단순히 `npx create-next-app`만 실행하면, npx는 로컬 캐시에 있는 버전을 사용하거나 최신 버전을 다운로드합니다. 하지만 `@latest`를 명시하면 캐시를 무시하고 반드시 최신 버전을 가져옵니다.

특정 버전을 원한다면 `@13.4.0` 같은 형태로 버전 번호를 직접 지정할 수도 있습니다. 이는 프로젝트 간 일관성을 유지하거나 특정 버전의 기능을 사용하고 싶을 때 유용합니다.

## 실행 과정과 대화형 설정

이 명령어를 터미널에서 실행하면 대화형 프롬프트가 나타납니다. 실제 과정을 예시로 보면 다음과 같습니다:

```
✔ What is your project named? … my-next-app
✔ Would you like to use TypeScript? … No / Yes
✔ Would you like to use ESLint? … No / Yes
✔ Would you like to use Tailwind CSS? … No / Yes
✔ Would you like to use `src/` directory? … No / Yes
✔ Would you like to use App Router? (recommended) … No / Yes
✔ Would you like to customize the default import alias (@/*)? … No / Yes
```

프로젝트 이름을 입력하면 해당 이름의 디렉토리가 생성됩니다. TypeScript 사용 여부를 선택하면 `.ts`, `.tsx` 파일로 구성되거나 `.js`, `.jsx` 파일로 구성됩니다. Tailwind CSS를 선택하면 관련 설정 파일과 기본 스타일이 자동으로 추가됩니다.

App Router는 Next.js 13 이상에서 도입된 새로운 라우팅 시스템입니다. 이를 선택하면 `app` 디렉토리가 생성되고, 선택하지 않으면 기존 `pages` 디렉토리 방식이 사용됩니다.

## 명령어에 플래그 추가하기

대화형 프롬프트 없이 모든 옵션을 한 번에 지정할 수도 있습니다:

```bash
npx create-next-app@latest my-app --typescript --tailwind --app --src-dir --import-alias "@/*"
```

이렇게 실행하면 중간에 질문을 받지 않고 바로 프로젝트가 생성됩니다. 이는 자동화 스크립트를 작성하거나 정확히 원하는 설정을 알고 있을 때 유용합니다.

## 생성 후 결과

명령어 실행이 완료되면 다음과 같은 파일 구조가 만들어집니다:

```
my-next-app/
├── app/
│   ├── layout.tsx
│   ├── page.tsx
│   └── globals.css
├── public/
├── node_modules/
├── package.json
├── next.config.js
├── tsconfig.json
└── tailwind.config.js
```

이제 해당 디렉토리로 이동해서 `npm run dev`를 실행하면 개발 서버가 시작되고, `http://localhost:3000`에서 Next.js 애플리케이션을 확인할 수 있습니다.

결론적으로 이 명령어는 Next.js 개발을 시작하기 위한 모든 번거로운 초기 설정 과정을 자동화해주는 매우 편리한 도구입니다. 수동으로 설정 파일을 작성하고 패키지를 일일이 설치하는 대신, 단 한 줄의 명령어로 프로덕션 레벨의 프로젝트 구조를 갖출 수 있습니다.

## 올바른 작업 순서

**1단계: 원하는 위치로 이동** 
- 먼저 프로젝트를 생성하고 싶은 상위 디렉토리로 이동합니다. 예를 들어 바탕화면이나 Documents 폴더 등입니다.

```bash
cd ~/Desktop
# 또는
cd ~/Documents/projects
```

**2단계: Next.js 프로젝트 생성**
- 이 위치에서 `create-next-app`을 실행합니다. 이때 프로젝트 이름을 지정하면 해당 이름의 폴더가 자동으로 생성됩니다.

```bash
npx create-next-app@latest my-project
```

- 이 명령어를 실행하면 다음과 같은 일이 일어납니다:
	- `my-project`라는 이름의 새 폴더가 생성됩니다
	- 그 폴더 안에 Next.js의 모든 파일과 폴더 구조가 만들어집니다
	- 필요한 npm 패키지들이 설치됩니다

**3단계: 생성된 프로젝트 폴더로 이동**
- 이제 방금 생성된 프로젝트 폴더 안으로 들어가야 합니다.

```bash
cd my-project
```

**4단계: shadcn/ui 초기화** 
- 프로젝트 폴더 안에서 shadcn/ui를 초기화합니다.

```bash
npx shadcn@latest init
```

## 현재 디렉토리에 직접 설치하는 방법

만약 이미 폴더를 만들었고, 그 안에 직접 Next.js 파일들을 설치하고 싶다면, 프로젝트 이름에 `.` (현재 디렉토리)를 입력하면 됩니다:

bash

```bash
$ mkdir my-project
$ cd my-project
$ npx create-next-app@latest .
```

이렇게 하면 현재 폴더(`my-project`) 안에 직접 파일들이 생성됩니다. 하지만 이 경우 폴더가 완전히 비어있어야 합니다. 이미 파일이 있으면 오류가 발생할 수 있습니다.