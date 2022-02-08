# Domain-Driven Design

- layer 간의 참조 관계에서는 **단방향 의존을 유지**하고, 계층 간 호출에서는 인터페이스를 통한 호출이 되도록 한다.

## layer별 특징과 역할
- 사용자 인터페이스 (interfaces)
    - 사용자에게 정보를 보여주고, 사용자의 명령을 해석하는 책임을 진다.
    - ex) controller, dto, mapper(converter)
- 응용 계층 (application)
    - 수행할 작업을 정의하고 표현력 있는 도메인 객체가 문제를 해결하게 한다. 이 계층에서 책임지는 작업은 업무상 중요하거나 다른 시스템의 응용 계층과 상호작용하는 데 필요한 것들이다. 이 계층은 얇게 유지되고, 오직 작업을 조정하고 아래에 위치한 계층에 포함된 도메인 객체의 협력자에게 작업을 위임한다.
    - ex) facade
- 도메인 계층 (domain)
    - 업무 개념과 업무 상황에 대한 정보, 업무 규칙을 표현하는 일을 책임진다. 이 계층에서는 업무 상황을 반영하는 상태를 제어하고 사용하며, 그와 같은 상태 저장과 관련된 기술적인 세부사항은 인프라스트럭쳐에 위임한다. 이 계층이 업무용 소프트웨어의 핵심이다.
    - ex) entity, service, command, criteria, info, reader, store, executor, factory(interface)
- 인프라스트럭쳐 계층 (infrastructure)
    - 상위 계층을 지원하는 일반화된 기술적 기능을 제공한다. 이러한 기능에는 애플리케이션에 대한 메시지 전송, 도메인 영속화, UI에 위젯을 그리는 것 등이 있다.

## layer 간 참조 관계
- layer 간의 참조 관계에서 application과 infrastructure는 domain layer를 바라보게 하고, 양방향 참조는 허용하지 않게 한다.
- domain layer는 low level의 기술에 상관 없이 독립적으로 존재할 수 있어야 한다.
    - 이를 위해 대부분의 주요 로직은 추상화되고, runtime에는 DIP 개념을 활용하여 실제 구현체가 동작하게 한다.
    ![image](https://user-images.githubusercontent.com/26949964/152808468-d97b5986-bb2b-4f51-a0d7-89ebd72791b0.png)

## Domain Layer
- 높은 추상화 수준을 지속적으로 유지하는 것이 매우 중요하다.
- entity
- service
- 그 외 infrastructure layer와 통신하기 위한 interface들
- 다른 layer로부터 데이터를 받거나 다른 layer로 데이터를 넘기기 위한 그릇