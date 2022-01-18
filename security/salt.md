# Salt

- 어떤 정보를 해시 함수로 암호화할 때 추가되는 랜덤한 값을 salt라고 한다.
- 해커가 레인보우 테이블을 통해 해시 함수의 결과를 예측할 위험이 있으므로, 암호화 하고자 하는 평문에 임의의 랜덤한 값인 salt를 추가하여 그 값에 해시 함수를 적용하는 것이다.
    - 레인보우 테이블이란, 해싱 함수의 결과를 미리 캐시해 둔 테이블
    - https://en.wikipedia.org/wiki/Rainbow_table

## Spring Security
- https://www.baeldung.com/spring-security-registration-password-encoding-bcrypt
- https://www.baeldung.com/java-password-hashing
- https://reflectoring.io/spring-security-password-handling/