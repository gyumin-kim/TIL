# JWT
> JSON Web Token

## Cookie, Session 기반 인증 처리(전통적인 인증 시스템)의 문제
- "모든 서버들 사이에서 session이 공유되지 않은 경우 문제가 될 수 있음"
- HTTP는 무상태 프로토콜이고 어떤 정보도 저장하지 않음
- 서버는 인증된 사용자 정보를 저장하기 위한 용도로 session을 사용
- session은 서버 메모리를 사용하므로, 사용자 정보가 너무 많아질 경우 서버 메모리가 부족해질 수 있음
- 서버 장애 시, 복제본이 없는 session 정보는 유실됨
