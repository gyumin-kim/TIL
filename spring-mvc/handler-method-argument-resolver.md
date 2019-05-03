# Spring MVC — HandlerMethodArgumentResolver


Spring MVC의 Controller에서 parameter를 받을 때 흔히 다음과 같이 한다.

```java
@GetMapping(path = "user/{userId}/post/list")
public ApiResult<List<Post>> posts(
		...
        @RequestParam("offset") int offset,
        @RequestParam("limit") int limit) {
        
    ...
    
    return new ApiResult<>(...);
}
```

`@RequestParam`을 통해 URL 상의 매개변수를 직접 받아 사용하는 모습이다.

위와 동일한 방법으로, 더 많은 수의 값들을 받을 수도 있다.

```java
@GetMapping(path = "user/{userId}/post/list")
public ApiResult<List<Post>> posts(
		...
        @RequestHeader(name = "customerName", required = false) String customerNameHeader,
        @RequestParam(name = "customerName", required = false) String customerNameParam,
        @RequestHeader(name = "department", required = false) String departmentHeader,
        @RequestParam(name = "department", required = false) String departmentParam,
        ...) {
        
    ...
    
    return new ApiResult<>(...);
}
```

위의 코드는 실질적으로 URL 매개변수로는 2개만 받지만, 그것을 활용해서 더 많은 수의 값을 만들어 사용하고 있다.

동작하는 데는 문제가 없으나 코드가 보기 싫게 길어지는 문제가 있고, 비슷한 로직을 여러 클래스에서 공통적으로 사용할 경우 코드의 중복이 발생한다.


혹은 여러 값들을 하나의 객체로 묶고, `@ModelAttribute` 애노테이션을 붙여서 객체 형태로 받을 수도 있다. 코드가 훨씬 간결해진다.

```java
@GetMapping(path = "user/{userId}/post/list")
public ApiResult<List<Post>> posts(
		...
        @ModelAttribute CustomDto customDto) {
        
    ...
    
    return new ApiResult<>(...);
}
```

단 `@ModelAttribute`의 경우 고려해야 할 것이 있는데, 일단 `@ModelAttribute`로 받는 객체에는 setter가 반드시 존재해야 한다. setter 선언을 안하면 결과적으로 property 세팅이 안된다. 예를 들어 parameter로 받는 객체의 필드들이 immutable(final)하고 해당 필드에 대한 setter가 없다면, `@ModelAttribute`을 통해 Controller에서 받아도 값이 주입되지 않는다.



## HandlerMethodArgumentResolver 소개

위와 같은 상황에서 사용할 수 있는, Spring 3.1부터 제공되는 `HandlerMethodArgumentResolver`라는 인터페이스가 있다. Spring 공식문서에는 다음과 같이 설명되어 있다.

> Strategy interface for resolving method parameters into argument values in the context of a given request.  
주어진 요청을 처리할 때, 메소드 파라미터를 인자값들에 주입 해주는 전략 인터페이스.


위에서 언급한 것처럼,

- parameter로 받는 값이 여러 개가 존재하고(혹은 객체의 필드들이 여러 개가 존재), 그것을 처리하는 코드들의 중복이 발생할 때
- Controller에 공통으로 입력되는 parameter들을 추가하거나 수정하는 등의 여러 공통적인 작업들을 한 번에 처리하고 싶을 때

대략 이러한 경우에 HandlerMethodArgumentResolver를 사용한다.



## HandlerMethodArgumentResolver 사용하기
1. 먼저, parameter로 받을 객체를 만들고, 이름은 `FooBar`라고 하자.


```java
public class FooBar {
 
    private final String bar;
    private final String foo;
 
    FooBar(String bar, String foo) {
        this.bar = bar;
        this.foo = foo;
    }
 
    public String getBar() {
        return bar;
    }
 
    public String getFoo() {
        return foo;
    }
}
```



2. 다음으로는 custom `HandlerMethodArgumentResolver`를 구현해야 한다. 

이 인터페이스는 `supportsParameter(MethodParameter parameter)`,  
`resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, NativeWebRequest webRequest, WebDataBinderFactory binderFactory)` 2가지 메소드를 갖고 있다.

`supportsParameter`는 핸들러(컨트롤러 메소드)의 특정 파라미터를 지원하는지 여부를 판단하기 위한 메소드다. 쉽게 말해 어떤 파라미터에 대해 작업을 수행할 것인지를 정의하는 곳이다.

`resolveArgument`에서는 해당 parameter에 대한 실질적인 로직을 처리하는 곳이다. parameter에 전달할 객체에 대한 조작을 자유롭게 진행한 뒤 해당 객체를 리턴한다.


```java
import org.springframework.core.MethodParameter;
import org.springframework.web.bind.support.WebDataBinderFactory;
import org.springframework.web.context.request.NativeWebRequest;
import org.springframework.web.method.support.HandlerMethodArgumentResolver;
import org.springframework.web.method.support.ModelAndViewContainer;
 
public final class FooBarHandlerMethodArgumentResolver 
	implements HandlerMethodArgumentResolver {
 
    @Override
    public boolean supportsParameter(MethodParameter methodParameter) {
        return methodParameter.getParameterType().equals(FooBar.class);
    }
 
    @Override
    public Object resolveArgument(MethodParameter methodParameter,
                                  ModelAndViewContainer modelAndViewContainer,
                                  NativeWebRequest nativeWebRequest,
                                  WebDataBinderFactory webDataBinderFactory) throws Exception {
        String bar = nativeWebRequest.getParameter("bar");
        String foo = nativeWebRequest.getParameter("foo");
 
        if (isNotSet(bar)) {
            bar = "defaultBar";
        }
 
        if (isNotSet(foo)) {
            foo = "defaultFoo";
        }
 
        return new FooBar(bar, foo);
    }
 
    private boolean isNotSet(String value) {
        return value == null;
    }
}
```

URL 매개변수로 들어온 값들을 변수에 할당하고, 값이 없으면 기본값을 할당하는 간단한 코드다. 더 복잡한 설정을 자유롭게 할 수 있다. 


3. 위에서 구현한 `HandlerMethodArgumentResolver`를 `addArgumentResolvers`를 통해 등록해주어야 한다.


```java
@Configuration
public class WebMvcConfigure implements WebMvcConfigurer {
    
    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
        resolvers.add(fooBarHandlerMethodArgumentResolver());
    }

    @Bean
    public FooBarHandlerMethodArgumentResolver fooBarHandlerMethodArgumentResolver() {
        return new FooBarHandlerMethodArgumentResolver();
    }
}
```



4. 이제 처음으로 돌아가서, Controller에서 `FooBar` 객체를 받는 모습을 보자.

```java
@RestController
final class FooBarController {
 
    @RequestMapping(value = "/test", method = RequestMethod.GET)
    public void processFooBar(FooBar fooBar) {
        //Do stuff
    }
}
```


Controller에서 입력값에 대한 처리를 신경쓸 필요가 없어 간편하다.

---


이 때 Controller에서 받는 객체는 직접 만든 custom한 객체가 아니어도 된다. 예를 들어 org.springframework.data.domain의 `Pageable`을 parameter로 받는다고 가정하자.


```java
@GetMapping(path = "user/{userId}/post/list")
public ApiResult<List<Post>> posts(
        ...
        Pageable pageable) {
    
    ...
    
    return new ApiResult<>(..., pageable));
}
```


페이징 처리를 위해 Controller에서 parameter로 `Pageable` 객체를 받고 있다.

이제 앞에서 본 것과 동일하게, `HandlerMethodArgumentResolver를` 구현한다.


```java
@Override
public boolean supportsParameter(MethodParameter parameter) {
	// 파라미터가 Pageable 타입이면 지원한다
	return Pageable.class.equals(parameter.getParameterType());
}

@Override
public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {
	int offsetString = Integer.parseInt(webRequest.getParameter("offset"));
	int limitString = Integer.parseInt(webRequest.getParameter("limit"));

	long offset = parseAndApplyBoundaries(offsetString, Integer.MAX_VALUE);
	int limit = parseAndApplyBoundaries(limitSizeString, DEFAULT_MAX_LIMIT_SIZE);

	limit = limit < 1 ? fallbackPageable.getPageSize() : limit;
	limit = limit > DEFAULT_MAX_LIMIT_SIZE ? DEFAULT_MAX_LIMIT_SIZE : limit;

	return new PageRequest(offset / limit, limit, new Sort(Sort.Direction.DESC, "seq"));
}
```

...


요청 URL에는 'offset'과 'limit'이라는 값이 포함되어 있다.

위 코드를 풀어서 해석하면,

- controller에서 parameter가 `Pageable` 타입이면 해당 작업을 수행할 것이며, 
- 요청 URL의 parameter로 들어온 offset과 limit의 값을 적절하게 조작하고, `PageRequest` 객체를 리턴한다. (`resolveArgument` 내 로직은 천차만별일 수 있다)


쉽게 생각하면 `HandlerMethodArgumentResolver`는, 인자가 요구되는 상황에서 이 **인자를 생성하고 조립하기 위한 팩토리** 정도로 생각할 수 있다. 앞서 Spring Docs의 설명에서 **전략 인터페이스**라는 용어가 등장했었는데, **`HandlerMethodArgumentResolver`의 구현체에 개발자가 전략을 정의하고 DispatcherServlet의 앞단에서 해당 전략을 주입한다**는 의미로 받아들이면 될 듯 하다.



~~(AOP는 아니고, 정확하게는 Filter도 아니고....  
모든 컨트롤러에 해당되는 건 아니기 때문에 Interceptor도 아니라는데 이건 좀 더 알아봐야 될 것 같다.)~~


---

## 참고 자료
- [Spring From the Trenches: Creating a Custom HandlerMethodArgumentResolver](https://www.petrikainulainen.net/programming/spring-framework/spring-from-the-trenches-creating-a-custom-handlermethodargumentresolver/)
- [스프링 MVC 커스텀 HandlerMethodArgumentResolver 사용하기](https://javacan.tistory.com/entry/how-use-spring-mvc-custom-HandlerMethodArgumentResolver)
- [Spring Docs — HandlerMethodArgumentResolver](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/method/support/HandlerMethodArgumentResolver.html)
