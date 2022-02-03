# Feign Client

- REST call을 추상화 한 Spring Cloud Netflix 라이브러리
- 호출하려는 HTTP endpoint에 대한 interface를 생성하고, `@FeignClient`를 선언
- load balanced 지원

## 설정 방법
- dependency 추가
    - Maven
    ```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-openfeign</artifactId>
    </dependency>
    ```
    - Gradle
    ```gradle
    implementation 'org.springframework.cloud:spring-cloud-starter-openfeign:3.1.0'
    ```
- main class에 `@EnableFeignClients` 추가
- client interface 생성
    ```java
    // Service Discovery(ex. Eureka) 서버에 해당 name으로 등록된 서비스로 요청을 보낸다
    @FeignClient(name = "order-service")
    public interface OrderServiceClient {

        @GetMapping("/order-service/{userId}/orders")
        List<ResponseOrder> getOrders(@PathVariable String userId);
    }
    ```

## 예외 처리
- ErrorDecoder 타입의 객체를 생성하고, `decode()` 메서드를 구현하는 방법이 있다. 
    ```java
    @Component
    public class FeignErrorDecoder implements ErrorDecoder {
        @Override
        public Exception decode(String methodKey, Response response) {
            switch (response.status()) {
                case 400:
                    break;
                case 404:
                    if (methodKey.equals("getOrders")) {
                        // return some Exception ...
                    }
                    break;
                default:
                    return new Exception(response.reason());
            }
            return null;
        }
    }
    ```

## Logging
```java
@Bean
public Logger.Level feignLoggerLevel() {
    return Logger.Level.FULL;
}

```