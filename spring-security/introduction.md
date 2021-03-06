# Introduction

**인증**(Authentication)과 **권한 부여**(Authorization)는 애플리케이션 보안을 구현하고 이해하는 데 필요한 핵심 개념이다.

## Authentication (인증)
인증이란, **자원을 요청하는 사용자를 식별하는 것**이다.  
일반적으로 소프트웨어 시스템은, 다음 그림과 같이 인증되지 않은 영역(Unauthenticated Realm)과 인증된 영역(Authenticated Realm)으로 나뉜다.
![Realm](https://user-images.githubusercontent.com/26949964/56081383-b68e9f80-5e47-11e9-8091-fd7e757fbfca.png)

인증되지 않은 영역은,
- 사용자의 ID 또는 권한과는 독립적이다(무관하다). *ex) 시작 페이지*
- 사용자가 시스템에 로그인하도록 요구하지 않는다.
- 이름, 주소, 신용카드 및 주문 정보와 같은 중요 정보를 표시하지 않는다.
- 시스템 또는 데이터의 전반을 조작하는 기능을 제공하지 않는다.
- 일회성 방문자를 포함한 모든 사용자가 사용할 수 있다.

## Authorization (권한 부여)
권한 부여는, 인증 과정에서 검증된 정보를 사용해 특정 자원에 대한 **접근 가능 여부를 판별**한다.  
일반적으로 보안 시스템에 대한 접근 가능 여부를 판단할 때는, 다음의 2가지 측면을 사용한다.
1. 인증된 주체에게 하나 또는 그 이상의 **권한(역할; roles)을 부여**하는 것이다. 예를 들어 웹 사이트의 일반적인 사용자에게는 방문자 권한을 부여하지만, 관리자에게는 관리자 권한을 부여한다.
2. 시스템의 보안 자원(secured resource; *권한에 따라 조건적으로 사용되는 시스템의 모든 요소*)에 접근할 때 **권한 체크 기능**을 구현하는 것이다. 일반적으로 권한 체크 기능은 코드를 통해 명시적으로 선언 하거나 매개변수를 통해 구현한다. 예를 들어 다른 사용자의 이벤트를 볼 수 있는 화면에는 관리자 권한이 있는 사용자만 접근할 수 있다.

웹 사이트 관리자가 보안 자원에 접근하려고 할 때, 접근 가능 여부를 판단하는 로직은 실제 사용자 권한(User Authorities)과 필요 권한(Required Authorities)에 대한 두 집합으로 간단하게 표현할 수 있다.  

![authorities1](https://user-images.githubusercontent.com/26949964/56086384-39881800-5e91-11e9-9a55-5f764f365839.png)  
위 그림을 보면 'All Events'라는 페이지에 대한 사용자 권한과, 페이지에 접근하기 위한 필요 권한의 교집합이 존재한다. 즉 사용자는 페이지에 접근하기 위한 필요 권한을 갖고 있다는 의미이며 해당 페이지에 접근할 수 있다.

![authorities2](https://user-images.githubusercontent.com/26949964/56086424-08f4ae00-5e92-11e9-83e7-fbe704d5b65e.png)  
반면 위 그림은 집합에서 교집합이 존재하지 않는다. 즉 '방문자 권한'을 가진 사용자는 'All Events' 페이지에 접근 권한이 없으므로 접근이 불가능하다. 