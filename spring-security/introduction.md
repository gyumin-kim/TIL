# Introduction

**인증**(Authentication)과 **권한 부여**(Authorization)는 애플리케이션 보안을 구현하고 이해하는 데 필요한 핵심 개념이다.

## Authentication (인증)
인증이란, 자원을 요청하는 사용자를 식별하는 것이다.  
일반적으로 소프트웨어 시스템은 다음 그림과 같이 인증되지 않은 영역(Unauthenticated Realm)과 인증된 영역(Authenticated Realm)으로 나뉜다.
![Realm](https://user-images.githubusercontent.com/26949964/56081383-b68e9f80-5e47-11e9-8091-fd7e757fbfca.png)

인증되지 않은 영역은,
- 사용자의 ID 또는 권한과는 독립적이다(무관하다). *ex) 시작 페이지*
- 사용자가 시스템에 로그인하도록 요구하지 않는다.
- 이름, 주소, 신용카드 및 주문 정보와 같은 중요 정보를 표시하지 않는다.
- 시스템 또는 데이터의 전반을 조작하는 기능을 제공하지 않는다.
- 일회성 방문자를 포함한 모든 사용자가 사용할 수 있다.

## Authorization (권한 부여)
권한 부여는, 인증 과정에서 검증된 정보를 사용해 특정 자원에 대한 접근 가능 여부를 판별한다.