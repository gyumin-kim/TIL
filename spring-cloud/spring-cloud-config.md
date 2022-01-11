# Spring Cloud Config

- MSA 환경에서 개별 서비스들이 내부적으로 갖고 있는 설정 파일을, 외부의 별도 config 서버로 분리하여 사용하기 위함
    - https://cloud.spring.io/spring-cloud-config/reference/html/
    - https://spring.io/guides/gs/centralized-configuration/
- 각 서비스는 애플리케이션 구동 시 `bootstrap.yml`을 참고하여 config server로부터 설정 정보를 받아온다.
    - `bootstrap.yml`의 `spring.cloud.config.uri` 값에 config server의 URI를 명시한다.
- 실제 사용될 설정 값들이 들어 있는 configuration file의 위치는 config server의 설정 파일(`application.yml`)에 지정한다.
    - `spring.cloud.config.server.git.uri`={configuration file의 위치}
        - ex) `spring.cloud.config.server.git.uri=file://${HOME}/Desktop/config`
        - ex) `spring.cloud.config.server.git.uri=https://github.com/.../...`
    - configuration file을 저장할 repository로는 주로 'Git-based filesystem repository'를 사용한다. local directory 혹은 GitHub이나 GitLab 등을 활용할 수 있다.
- 준비사항
    - config server에는 `spring-cloud-config-server` dependency를 추가하고, `@EnableConfigServer`를 추가해줘야 한다.
    - client(config server로부터 설정 정보를 받는 쪽)에는 `spring-cloud-starter-config`와 `spring-cloud-starter-bootstrap`를 dependency로 추가해줘야 한다.
- configuration value가 변경될 때, 변경된 값을 각 서비스에 적용하기 위한 방법은 아래와 같다.
    - 각 서비스 재기동
    - Spring Actuator 'refresh' 옵션
    - Spring Cloud Bus 사용