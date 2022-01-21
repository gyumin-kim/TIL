# Spring Cloud Bus

- 분산 시스템의 노드(마이크로 서비스)들을 경량 메시지 브로커(ex. RabbitMQ)와 연결
- 상태 및 구성에 대한 변경 사항을 연결된 노드에게 전달(broadcast)
- config 정보가 변경될 때, Spring Cloud Config 서버와 연결되어 있는 Spring Cloud Bus 서버가 해당 변경 사항을 AMQP 기반의 메시지로 개별 서비스에게 전송(push updates)한다.
    - 개별 서비스 한 곳에만 bus refresh 요청을 보내도 된다. 해당 서비스가 변경된 데이터를 받았음을 알려주면, config server와 연결된 다른 서비스들에게도 변경 사항이 push update를 통해 전달(broadcast)된다.
- AMQP(Advanced Message Queuing Protocol)
    - 메시지 지향 미들웨어를 위한 개방형 표준 응용 계층 프로토콜
    - 메시지 지향, queuing, routing(P2P, publisher-subscriber), 신뢰성, 보안