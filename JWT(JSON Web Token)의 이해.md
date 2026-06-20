---
date: 2025-11-02
tags:
  - JWT
  - "#토큰기반인증"
  - "#웹보안"
  - "#API인증"
  - vibe_coding
type: knowledge
contexts:
  - "[[🏷️Artificial Intelligence]]"
  - "[[🏷️Vibe Coding]]"
index:
  - "[[🎶Web Development]]"
YouTube:
scr_url:
scr:
---




> [!summary]
> JWT(JSON Web Token)는 정보를 안전하게 전송하기 위한 토큰 기반의 인증 방식입니다. 웹이나 앱 환경에서 사용자의 신원을 증명하는 '디지털 신분증'과 같으며, 서버가 사용자 인증 상태를 직접 저장하지 않는 '상태 비저장(stateless)' 방식이라는 특징이 있습니다. 이는 놀이공원 입장 시 팔찌를 받아 팔찌 자체로 이용 권한을 증명하는 방식에 비유할 수 있습니다.
> 
> JWT는 `헤더(Header)`, `페이로드(Payload)`, `서명(Signature)`의 세 부분으로 구성되며 각 부분은 점(.)으로 구분됩니다.
> *   **헤더**는 토큰의 타입(JWT)과 암호화 알고리즘 정보를 담습니다.
> *   **페이로드**는 실제 전달할 데이터를 포함합니다. 사용자 ID, 권한, 토큰 만료 시간(`exp`) 등의 정보가 여기에 담기지만, Base64로 인코딩되므로 민감한 정보는 포함하지 않아야 합니다.
> *   **서명**은 헤더와 페이로드를 비밀키(secret_key)로 암호화하여 생성되며, 토큰의 위변조 여부를 검증하는 데 사용됩니다.
> 
> JWT의 동작 과정은 다음과 같습니다. 사용자가 ID/PW로 로그인 요청을 하면, 서버는 사용자 정보를 확인한 후 JWT를 발급하여 클라이언트에게 전송합니다. 클라이언트는 이 토큰을 저장하고 있다가, 인증이 필요한 API 요청 시 `Authorization` 헤더에 토큰을 담아 서버로 보냅니다. 서버는 전달받은 토큰의 서명을 비밀키로 검증하여 유효성을 확인하고, 토큰이 유효하면 요청을 처리하고 응답을 보냅니다.
> 
> JWT 방식의 주요 장점은 다음과 같습니다.
> *   **서버 부담 감소**: 서버가 사용자의 세션 정보를 직접 저장하고 관리할 필요가 없어 서버 자원 소모를 줄일 수 있습니다.
> *   **확장성**: 여러 서버가 있거나 마이크로서비스 아키텍처 환경에서도 토큰 자체로 인증이 가능해 유연한 확장이 가능합니다.
> *   **다양한 플랫폼 지원**: 웹, 모바일 앱, IoT 등 다양한 환경에서 범용적으로 사용될 수 있습니다.
> 
> 반면 단점 및 주의사항도 존재합니다.
> *   **토큰 크기**: 세션 ID 방식보다 토큰 크기가 커서 네트워크 트래픽이 다소 증가할 수 있습니다.
> *   **보안 문제**: 토큰이 탈취될 경우 만료 시간 전까지는 권한이 유효하게 유지될 수 있으며, 페이로드에 민감한 정보가 포함되면 Base64 디코딩으로 노출될 위험이 있습니다.
> *   **토큰 무효화 어려움**: 한 번 발급된 토큰은 만료되기 전까지는 유효하므로, 로그아웃 등의 경우 별도의 블랙리스트 관리가 필요할 수 있습니다.
> 
> 따라서 JWT 사용 시에는 HTTPS 통신을 통한 토큰 전송, 적절한 토큰 만료 시간 설정, 그리고 페이로드에 민감한 정보를 포함하지 않는 등의 보안 대책이 중요합니다.

# JWT(JSON Web Token)란?

JWT는 **정보를 안전하게 전송하기 위한 토큰 기반의 인증 방식**입니다. 웹이나 앱에서 사용자 인증 정보를 주고받을 때 사용하는 일종의 "디지털 신분증"이라고 생각하면 쉽습니다.

## 실생활 비유로 이해하기

놀이공원 입장권을 예로 들어볼까요?

**전통적인 세션 방식** (놀이공원 입구에서 손도장 찍기)

- 입장할 때 손에 도장을 찍어줍니다
- 놀이기구를 탈 때마다 직원이 손도장을 확인합니다
- 놀이공원(서버)이 누가 입장했는지 계속 기억해야 합니다

**JWT 방식** (팔찌형 입장권)

- 입장할 때 팔찌를 줍니다
- 팔찌에는 "이름, 입장시간, 이용권 종류" 등이 암호화되어 있습니다
- 놀이기구마다 팔찌만 확인하면 됩니다
- 놀이공원은 일일이 기억할 필요가 없습니다

## JWT의 구조

JWT는 **세 부분**으로 구성되며, 점(`.`)으로 구분됩니다:

```
xxxxx.yyyyy.zzzzz
```

### 1. Header (헤더)

토큰의 타입과 암호화 알고리즘 정보를 담습니다.

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

### 2. Payload (페이로드)

실제 전달할 데이터를 담습니다. 사용자 정보나 권한 등이 들어갑니다.

```json
{
  "userId": "12345",
  "username": "hong",
  "role": "admin",
  "exp": 1735689600
}
```

### 3. Signature (서명)

토큰이 위조되지 않았음을 증명하는 서명입니다.

```
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secret_key
)
```

## 실제 JWT 예시

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiIxMjM0NSIsInVzZXJuYW1lIjoiaG9uZyIsInJvbGUiOiJhZG1pbiJ9.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

위 토큰을 점(`.`)으로 나누면:

- **Header**: `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9`
- **Payload**: `eyJ1c2VySWQiOiIxMjM0NSIsInVzZXJuYW1lIjoiaG9uZyIsInJvbGUiOiJhZG1pbiJ9`
- **Signature**: `SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c`

## JWT 동작 과정

### 1단계: 로그인

```
사용자 → 서버: "ID: hong, PW: 1234"
```

### 2단계: JWT 발급

```
서버 → 사용자: "여기 토큰이야"
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### 3단계: 인증이 필요한 요청

```
사용자 → 서버: "내 정보 보여줘"
Header: Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### 4단계: 토큰 검증 후 응답

```
서버: "토큰 확인했어. 여기 정보야!"
```

## JWT의 장점

**1. 서버 부담 감소**

- 서버가 세션을 저장하지 않아도 됩니다
- 토큰 자체에 모든 정보가 있습니다

**2. 확장성**

- 여러 서버가 있어도 문제없이 작동합니다
- 마이크로서비스 아키텍처에 적합합니다

**3. 다양한 플랫폼 지원**

- 웹, 모바일 앱, IoT 등 어디서든 사용 가능합니다

## JWT의 단점

**1. 토큰 크기**

- 세션 ID보다 크기가 큽니다
- 매 요청마다 전송되므로 트래픽이 증가합니다

**2. 보안 문제**

- 토큰이 탈취되면 만료 전까지 막을 방법이 없습니다
- 민감한 정보는 Payload에 넣으면 안 됩니다 (Base64로 디코딩하면 보입니다)

**3. 토큰 무효화 어려움**

- 로그아웃해도 토큰이 만료될 때까지 유효합니다
- 별도의 블랙리스트 관리가 필요할 수 있습니다

## 실전 사용 예시 (JavaScript)

```javascript
// 1. 로그인 시 JWT 생성 (서버)
const jwt = require('jsonwebtoken');

const token = jwt.sign(
  { userId: '12345', username: 'hong' }, // payload
  'my-secret-key', // 비밀키
  { expiresIn: '1h' } // 1시간 후 만료
);

// 2. 클라이언트에서 저장
localStorage.setItem('token', token);

// 3. API 요청 시 토큰 전송
fetch('/api/user/profile', {
  headers: {
    'Authorization': `Bearer ${localStorage.getItem('token')}`
  }
});

// 4. 서버에서 토큰 검증
const decoded = jwt.verify(token, 'my-secret-key');
console.log(decoded.userId); // '12345'
```

## 핵심 정리

JWT는 사용자 인증 정보를 안전하게 주고받기 위한 자가 수용적(self-contained) 토큰입니다. 서버가 상태를 저장하지 않아도 되는 장점이 있지만, 탈취 시 위험이 있으므로 HTTPS 사용과 적절한 만료 시간 설정이 중요합니다.

