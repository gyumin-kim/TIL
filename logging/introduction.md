# Logging 기초 정리

## `System.out.println()`으로 메시지 출력하기
- 파일에 메시지를 출력하므로 비용이 큰 작업이다.
- 실제 배포할 때는 기존 `System.out.println()` 코드를 지웠다가, 추후 디버깅 할 때 다시 포함시키는 과정을 반복하기는 너무 번거로움.

## Logging Library
- Java 진영의 logging 프레임웤은 Log4J -> Logback -> Log4J2 흐름으로 발전해 왔다.
- **Slf4J**(Simple Logging Facade for Java)는 다양한 logging 프레임웤들을 위한 추상(일종의 인터페이스)이며, 
개발자는 원하는 logging 프레임웤(logback, log4j2 등)을 plug in하고 코드 상에서는 Slf4J를 이용해 디버깅 메시지를 출력하면 된다. 
즉 다른 logging 프레임웤을 사용하고 싶다면 다른 Slf4J의 구현체로 변경하기만 하면 되고, 소스코드 상에서는 변화가 필요 없다.

## Log Level
- 대표적인 로그 레벨은 `TRACE, DEBUG, INFO, WARN, ERROR`가 있다(레벨이 낮은 것부터).
- 로그 레벨이 높을 수록 출력되는 메시지는 적어지고, 레벨이 낮을 수록 더 많은 로깅 레벨이 출력된다.
  - ex) WARN 로그 레벨로 설정하면 WARN, ERROR 레벨의 메시지만 출력
  - ex) DEBUG 로그 레벨로 설정하면 DEBUG, INFO, WARN, ERROR 레벨의 메시지만 출력
- `log.info()`는 INFO 레벨, `log.error()`는 ERROR 레벨

## 설정 파일
- Logback의 설정 파일은 `logback.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration>
<configuration>
	<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
		<layout class="ch.qos.logback.classic.PatternLayout">
			<Pattern>%d{HH:mm:ss.SSS} [%-5level] [%thread] [%logger{36}] - %m%n</Pattern>
		</layout>
	</appender>
	
	<root level="DEBUG">
		<appender-ref ref="STDOUT" />
	</root>
</configuration>
```
- 출력할 로그 메시지의 패턴을 지정할 수 있다.
- 위 코드의 경우 로그 레벨을 DEBUG로 설정했다. 따라서 DEBUG, INFO, WARN, ERROR 레벨의 메시지들이 출력될 것이다.
- 개발 단계에서는 DEBUG 정도의 낮은 로그 레벨로 설정하다가, 실서비스로 배포할 때는 INFO나 WARN 정도의 레벨로 설정한다. 개발 단계에서 필요한 로그는 출력하지 않음으로써 **성능을 개선**할 수 있다.

## String concatenation 대신 parameterized messages 사용하기
아래와 같이 구현했다고 치자.
```
logger.debug("Entry number: " + i + " is " + String.valueOf(entry[i]));
```

이 때 로그 레벨이 INFO 혹은 WARN일 경우, 굳이 `debug()` 메소드에 인자를 전달하기 위해 비용이 큰 String concatenation('`+`' 기호)를 수행할 필요가 없다. 어차피 DEBUG 레벨의 메시지는 출력되지 않을 것이기 때문이다.

Slf4J는 이 같은 단점을 보완하기 위해 동적인 메시지를 구현하기 위한 별도의 메소드를 제공한다. 성능을 떨어뜨리지 않으면서 동적인 메시지를 구현하려면 다음과 같이 한다.
```
logger.debug("The new entry is {}. It replaces {}.", entry, oldEntry);
```

위와 같이 하면, 해당 로그문이 사용 불가능한 경우에는 매개변수 생성 비용을 발생시키지 않는다. 즉 `{}` 대신 메시지를 포함시켜야 하는지 여부를 로그 레벨에 따라 판단하고, 그럴 필요가 없다면 해당 작업을 수행하지 않는다.
