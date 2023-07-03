### 1. URL 마지막에 ?name=com.ngs.framework.broker.NCRMAdapter 붙이기 
> 모든 URL에 마지막에 name queryParam으로 위의 클래스경로를 적어줘야 한다. 이거 진짜 38년도 방식인데.. 규칙이니깐 처리함
> 기존의 HttpUtils.mergeUrls 을 customizig 내부로 이동하는 방식으로 함

```
// CustomizingLogic.class
@Override
public String mergeUrls(HttpServer server, Layout layout) {
    return HttpUtils.mergeUrls(server.getUrl(), layout.getUrlInfo().getUrlPath());
}

// CustomizingLogic을 상속받아 ABL만 재구현
@Override
public String mergeUrls(HttpServer server, Layout layout) {
    return String.format("%s%s", super.mergeUrls(server, layout), "?name=com.ngs.framework.broker.NCRMAdapter");
}
```


### 2. 스프링 시큐리티 설정 변경 
> aTworks jar 자체를 수행시 http:~~~~~:9790 으로 호출하게 되는데 이경우 스프링 시큐리티에서 401 로 처리됨   
> "/api/**" 에 맵핑되는 URL만 로그인 체크를 하도록 수정   
> permitAllUrls() 로 버전관리, 다운로드, 로그인 API는 별도로 권한 설정함  

```
.authorizeHttpRequests(authorizeHttpRequests -> authorizeHttpRequests
        .requestMatchers(permitAllUrls()).permitAll()
        .requestMatchers("/api/**").authenticated()
        .anyRequest().permitAll()
)
```

### 3. ssotoken, ngstoken, NGSSESSION_S 를 단순 http header 가 아닌 Cookie 데이터로 취급 필요함 
> 실제로 데이터 담을때에 처리할 수도 있지만 처음부터 key : cookie로 담아두는 방법을 택함 (커스터마이징 로그인 방식이므로)
> 그리고 login_result에서도 body가 아닌 cookie 데이터로 데이터 내려옴   
```
// CustomizingLogicAbl.java
public SessionInfo getSessionInfo(Response response, SessionInfo sessionInfo) {

    List<Headers> headers = new LinkedList<>();

    String setCookie = "Set-Cookie";
    StringBuilder sb = new StringBuilder();
    response.headers().forEach(header -> {
        if (setCookie.equals(header.getFirst())) {
            String cookieString = header.getSecond().split(";")[0];
            String[] split = cookieString.split("=");
            if (SSOTOKEN.equals(split[0])) {
                sb.append(String.format("%s=%s;", SSOTOKEN, split[1]));
                sb.append(String.format("%s=%s;", NGSTOKEN, split[1]));
            }
            else{
                sb.append(String.format("%s=%s;", split[0], split[1]));
            }
        }
    });
    headers.add(new Headers("Cookie", sb.toString()));
    return new SessionInfo(headers, sessionInfo.loginInfo());
}
```


### 4. [bug] 소스분석시 Dto 하위 Dto 를 연결시에 상위 Dto가 아닌 Controller 에서 맵핑하고 있음
> 리펙토링 하면서 오류가 발생한 내용인데 ServerController -> reqeustDto -> requestSubDto 이렇게 되어 있을떄 requestSubDto 클래스를 찾는데
> ServerController의 import와 패지로 찾고 있음

