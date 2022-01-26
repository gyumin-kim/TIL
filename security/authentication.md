# Authentication

## JWT

- 취약점 및 유의사항
    - payload에 포함된 정보는 base64로 인코딩 된 값일 뿐 암호화된 것이 아니므로, 민감한 정보를 포함시킬 수 없다.
    - JWT의 알고리즘 타입 중 none 알고리즘은 서명 값 검증을 하지 않으므로, none 알고리즘은 서버에서 허용하지 않도록 해야 한다.
    - 해커가 어떠한 경로로 비밀키를 획득하면, payload의 데이터를 위조해 유효한 토큰을 생성할 수 있다. 이를 방지하기 위해 복잡도가 높고 안전한 비밀키를 사용해야 한다.
    - 발급된 이후 탈취된 토큰이 악의적으로 사용되는 것을 막기 위해, access 토큰과 refresh 토큰을 활용해야 한다.

### 참고 자료
- [https://blog.lgcns.com/2687](https://blog.lgcns.com/2687)
- [https://gngsn.tistory.com/31](https://gngsn.tistory.com/31)
- [https://www.devbridge.com/articles/five-risks-and-tips-when-securing-user-authentication-tokens/](https://www.devbridge.com/articles/five-risks-and-tips-when-securing-user-authentication-tokens/)