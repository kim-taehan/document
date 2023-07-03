# 1. URL 마지막에 ?name=com.ngs.framework.broker.NCRMAdapter 붙이기 
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
