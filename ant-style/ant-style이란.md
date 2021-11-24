# ant-style이란

> [Apache Ant](https://ant.apache.org) 프로젝트에서 정의한 path pattern

- [Apache Ant pattern 문서](http://ant.apache.org/manual/dirtasks.html#patterns)
- [Spring Framework - AntPathMatcher](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/util/AntPathMatcher.html)
- [Fisheye - Pattern matching guide](https://confluence.atlassian.com/fisheye/pattern-matching-guide-960155410.html) (참고용)

## Rules
- `?` matches one character
- `*` matches zero or more characters
- `**` matches zero or more directories in a path
- `{spring:[a-z]+}` matches the regexp [a-z]+ as a path variable named "spring"

## Examples
- `com/t?st.jsp` — matches com/test.jsp but also com/tast.jsp or com/txst.jsp
- `com/*.jsp` — matches all .jsp files in the com directory
- `com/**/test.jsp` — matches all test.jsp files underneath the com path
- `org/springframework/**/*.jsp` — matches all .jsp files underneath the org/springframework path
- `org/**/servlet/bla.jsp` — matches org/springframework/servlet/bla.jsp but also org/springframework/testing/servlet/bla.jsp and org/servlet/bla.jsp
- `com/{filename:\\w+}.jsp` will match com/test.jsp and assign the value test to the filename variable