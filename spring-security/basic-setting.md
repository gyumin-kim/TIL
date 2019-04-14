# Spring Security 기본 세팅

## 의존성 업데이트
프로젝트에 필요한 Spring Security JAR 파일을 포함하도록 의존성을 업데이트 한다.

`build.gradle:`
```gradle
dependencies {
  compile "org.springframework.security:spring-securityconfig:${springSecurityVersion}"
  compile "org.springframework.security:spring-securitycore:${springSecurityVersion}"
  compile "org.springframework.security:spr1ng-securityweb:${springSecurityVersion}"
}
```

## Spring Security XML 설정 파일 구현
표준 웹 요청을 처리하는 데 필요한 모든 Spring Security의 설정을 포함하는 Java Config 파일을 생성한다.

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

/**
 * Spring Security Config Class
 * @see WebSecurityConfigurerAdapter
 */
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

	private static final Logger logger = LoggerFactory
			.getLogger(SecurityConfig.class);

	/**
	 * Configure AuthenticationManager with inMemory credentials.
	 *
	 * @param  auth AuthenticationManagerBuilder
                  Spring Security가 사용자를 인증하는 방법.
                  아래의 경우, in-memory DB를 이용해 사용자명과 패스워드를 비교
	 * @throws Exception Authentication exception
	 */
	@Override
	public void configure(final AuthenticationManagerBuilder auth) throws Exception {

		auth.inMemoryAuthentication().withUser("user1@example.com")
				.password("user1").roles("USER");
		logger.info("***** Password for user 'user1@example.com' is 'user1'");
	}

	/**
	 * HTTP Security configuration
	 *
	 * @param http HttpSecurity configuration. 
                  현재 로그인한 사용자가 적절한 역할과 연결되어 있는지 확인하는 서블릿 필터를 생성한다.
	 * @throws Exception Authentication configuration exception
	 */
	@Override
	protected void configure(final HttpSecurity http) throws Exception {

		// 사용자가 ROLE_USER와 연결돼 있는지 확인
		http.authorizeRequests()
		    .antMatchers("/**").access("hasRole('USER')")
		    .and().formLogin()
		    .and().httpBasic()
		    .and().logout()
                    .and().csrf().disable();  // CSRF is enabled by default, with Java Config
	}
}
```

위 코드는 웹 애플리케이션을 보호하는 데 필요한 최소한의 Spring Security 설정이다. `configure(AuthenticationManagerBuilder)` 메소드는 Spring Security가 사용자를 인증하는 방법이며, `configure(HttpSecurity)` 메소드의 `HttpSecurity` 객체는 현재 로그인한 사용자가 적절한 역할과 연결돼 있는지 확인하는 서블릿 필터를 생성한다.


## web.xml 업데이트

### ContextLoaderListener & DispatcherServlet
Spring MVC는 기본적으로 `WebApplicationInitializer` 인터페이스를 제공하는데, 구현체인 `AbstractAnnotationConfigDispatcherServletInitializer` 클래스를 상속받는 방식을 권장한다. `WebApplicationInitializer`의 구현체는 `SpringServletContainerInitializer`에 의해 자동으로 탐지되며, Servlet 3.0 컨테이너에 의해 구동된다.  
Servlet v3.0 이상에서는 `WebApplicationInitializer` 인터페이스의 구현체를 상속받는 클래스와 `ServletContainerInitializer`를 같이 사용하는 방식을 통해, 전통적인 방식의 `web.xml`을 대체한다.

- `WebApplicationInitializer` 인터페이스  
Spring MVC에서 기본적으로 제공하는 인터페이스. 
`onStartup(ServletContext)` 메소드를 유일하게 가짐(→ 구현 클래스에서 구현 필요).
- `AbstractContextLoaderInitializer` 클래스 (추상클래스)  
`WebApplicationInitializer`의 구현체.  
- `ContextLoaderListener`  
Spring의 root `WebApplicationContext`를 구동하고 정지시키기 위한 Bootstrap listener  

![ContextLoaderListener&DispatcherServlet](https://user-images.githubusercontent.com/26949964/56093336-6d4f5600-5f02-11e9-9e02-f1ab4995d312.png)  
스프링 MVC 관련 컴포넌트는 DispatcherServlet의 ApplicationContext 인터페이스에서 초기화되고, 나머지는 Context LoaderListener에 의해 로드된다. 자식 빈즈는 루트 빈즈를 참조할 수 있지만, 루트 빈즈는 자식 빈즈를 참조할 수 없다. 일반적으로는 Spring Security가 MVC 선언 빈즈를 참조할 필요가 없다. 그러므로 ContextLoaderListener가 Spring Security의 모든 설정을 초기화하도록 설정한다.

### springSecurityFilterChain 필터
`AbstractSecurityWebApplicationInitializer`를 상속받아, 모든 요청을 인터셉트 하도록 springSecurityFilterChain을 구현한다. 

```java
import org.springframework.core.annotation.Order;
import org.springframework.security.web.context.AbstractSecurityWebApplicationInitializer;
import org.springframework.web.filter.DelegatingFilterProxy;

import javax.servlet.Filter;

/**
 * Registers the {@link DelegatingFilterProxy} to use the springSecurityFilterChain before
 * any other registered {@link Filter}
 */
@Order(1)
public class SecurityWebAppInitializer
        extends AbstractSecurityWebApplicationInitializer {

    public SecurityWebAppInitializer() {
        super();
    }
}
```
`@Order(1)`을 통해, 다른 로직이 호출되기 전에 springSecurityFilterChain이 먼저 로드되도록 한다. 위의 `SecurityWebAppInitializer` 클래스는 애플리케이션의 모든 URL에 대해 springSecurityFilterChain 필터를 자동으로 등록하고, SecurityConflg를 로드히는 ContextLoaderListener를 추가한다.