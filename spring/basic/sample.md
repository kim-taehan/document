### 1. Header 오타 수정 및 Constants 로 정리 
> reqVoNm -> reqVONm, transtId -> trnstId 오타 수정 작업
> AblConstants 에 Text 정리하여 공통으로 사용 (CustomizingLogicAbl, AblResolve)
> abl에서만 사용하는 Text 데이터로 static 이 아닌 주입받아서 사용하도록 함
```
// AblCustomizingConfig.java
@Configuration
@Profile("abl")
public class AblCustomizingConfig {

    @Bean
    FrameWorkResolve frameWorkResolve(){
        return new AblResolve(ablConstants());
    }

    @Bean
    CustomizingLogic customizingLogic() {
        return new CustomizingLogicAbl(ablConstants());
    }

    @Bean
    AblConstants ablConstants(){
        return new AblConstants();
    }

}
```

### 2. 소스 분석, 화면 녹화시 신규 API 등록시 Header 데이터 입력 방식
> 소스 분석: AblResolve.getServiceInfo()   
> 화면 녹화: CustomizingLogicAbl.getServiceInfo()

| Key| 설명| 화면녹화| 소스분석| 주의사항
| --| --| --| --| --| 
| srnId| 스크린 아이디| "aTworks"| "aTworks"| 필드만 존재하면 됨|
| trnstId| 트랜잭션 아이디| "aTworks"| "aTworks"| 필드만 존재하면 됨| 
| maskReqYn| 마스킹 정보| "N"| "N"| | 
| RRHTMLID| 트랜잭션 아이디| "aTworks"| "aTworks"| NCRM 에서 사용되는 데이터| 
| RRAPPLID| APP ID| "ngs"| "ngs"| NCRM 에서 사용되는 데이터| 
| reqSrvcNm| Service 이름| 녹화된 이력 그대로 사용| @Service value 데이터 조회 없는 경우 서비스명 사용| 정확하게 일치해야됨|
| reqMthdNm| 메서드 이름| 녹화된 이력 그대로 사용| 소스분석에서 사용되는 메서드명(첫 글자 소문자)| 정확하게 일치해야됨| 
| reqVONm| Requet VO 이름| 녹화된 이력 그대로 사용| 소스분석에서 사용되는 Request VO(패키지명까지 포함)| 정확하게 일치해야됨| 
| RRRECORD| Request VO 정보| 녹화된 이력 그대로 사용| "RR" + Request 메인 VO 명| NCRM 에서 사용되는 데이터| 
| RSRECORD| Response VO 정보| 녹화된 이력 그대로 사용| "RS" + Response 메인 VO 명| NCRM 에서 사용되는 데이터| 
| RRSUBAPPL| sub APP ID| 녹화된 이력 그대로 사용| Service Class의 처음 2글자| NCRM 에서 사용되는 데이터| 


### 3. 소스분석, 화면녹화시 URL Mapping 기능 정리 
> URL : /svc/ui/atworks.json, URL-PATTERN : /svc/ui/%.json 으로 입력되는 방식 업무 코드에 따라 (atworks.json, %.json) 을 제외하고는 달라질 수 있음   
> 이후 화면녹화시 layout을 찾을떄 /svc/ui/{화면ID}.json 으로 와도 URL-PATTERN과 비교하기 떄문에 layout 검색에 문제가 없음 ( ServiceId + URL pattern + Http method 로 일치 여부 확인하고 있음)


### 4. 시스템 코드 
naf-> RSRETURN 가 0000은 서비스 호출여부이며, 실제 응답 코드는 header -> svrTrtRsltList -> svrTrtRsltCd, svrTrtRsltCntnt 로 지정합니다.    
svrTrtRsltList 가 있는 경우 svrTrtRsltCd->SystemCode, svrTrtRsltCntnt->SystemMessage 가 되는 방식


