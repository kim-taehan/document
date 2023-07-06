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





