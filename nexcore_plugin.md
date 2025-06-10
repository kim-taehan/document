# Nexcore 차세대 개발 tool 

## Web 백엔드 개발자 입장에서 VS Code vs IntelliJ를 비교
### VS Code
#### 장점 
- 가볍고 빠름
- JavaScript/TypeScript, Node.js, Express 개발에 최적
- 수많은 확장 기능 (Prettier, ESLint, Docker, Git, REST Client 등)
- 프론트엔드 연동도 자연스러움
- 무료, 오픈소스
#### 단점
- Spring Boot 자동화는 IntelliJ만큼 강력하지 않음
- 대규모 Java 리팩토링에 약함
- 디버깅/런타임 연동은 세팅이 더 필요함

### IntelliJ IDEA
#### 장점
- Spring Boot 통합이 끝내줌 (자동완성, 컨트롤러 ↔ 뷰 ↔ 서비스 추적)
- 복잡한 Java 프로젝트 구조 인식 잘함
- Gradle/Maven 완벽 지원
- 리팩토링, 디버깅, 테스트 작성 강력함
- DB 연동 도구 탑재됨

#### 단점
- 리소스 많이 사용
- Ultimate 버전은 유료임 (Spring 지원은 유료 버전에 있음)


### IntelliJ IDEA 단점 
#### 리소스 많이 사용 
- VS Code 보다 리소스를 많이 사용하는 것이지 eclipce 에 비하면 매우 적고 현재 개발자 PC 사양에서 전혀 문제가 되지 않음

#### Ultimate 버전은 유료임 
- IntelliJ IDEA Community Edition 
  - Apache 2.0 라이선스로 제공됨
  - 누구나, 상업용 프로젝트 포함하여 자유롭게 사용 가능
  - https://www.jetbrains.com/idea/download/#section=windows


## VS Code vs IntelliJ 플러그인 개발 

##  IDE 플러그인 개발 비교

| 항목 | Eclipse | IntelliJ IDEA | VS Code |
|------|---------|----------------|---------|
| **주 언어** | Java | Kotlin / Java | TypeScript / JavaScript |
| **개발 도구** | PDE (Plugin Development Environment) | Gradle + IntelliJ SDK | Node.js + `yo code` (Yeoman) |
| **진입 난이도** | 높음 (구조 복잡) | 중간~높음 (SDK 이해 필요) | 낮음 (가볍고 빠름) |
| **문서/커뮤니티** | 적고 구식 | 공식 문서 풍부, GitHub 템플릿 많음 | 활발하고 예제 많음 |
| **디버깅 방식** | Eclipse 내 런처 | Gradle `runIde` | VS Code에서 F5 실행 |
| **확장 대상** | Java 중심 기능 | JetBrains IDE 전반 | 편집기 UI 및 명령어 |
| **API 유연성** | 제한적, XML 위주 | 매우 유연, 구조화된 SDK | 자유도 높음 (간단한 UI 포함) |
| **학습 곡선** | 급경사 | 다소 복잡 | 비교적 쉬움 |
| **템플릿/예제** | 부족, 오래된 것 많음 | JetBrains 공식 템플릿 다수 | `yo code`로 자동 생성 |
| **배포 방식** | jar/zip 수동 또는 Eclipse Marketplace | JetBrains Plugin Portal | Visual Studio Marketplace |

>  참고:
> - Eclipse 플러그인은 구조가 복잡하고 XML 위주 설정이 많아 진입장벽이 높습니다.
> - IntelliJ 플러그인은 JetBrains 플랫폼 전체에 적용되며, 복잡하지만 확장성이 강력합니다.
> - VS Code는 가장 간단하게 시작할 수 있으며, TypeScript를 쓰면 정적 분석도 가능하고, 배포도 간단합니다.

## IDE별 플러그인 개발 특징 요약

###  Eclipse 플러그인
- PDE (Plugin Development Environment) 사용
- XML 중심의 `plugin.xml`, `MANIFEST.MF` 등 설정 많음
- 매우 구조화되어 있지만 복잡하고, 느림
- 플러그인 API 문서가 부족하거나 옛날 스타일

###  IntelliJ 플러그인
- Kotlin 또는 Java + Gradle 기반
- `plugin.xml`로 구조 정의하되, 코드로 로직 구성
- JetBrains가 공식 템플릿 제공: `intellij-platform-plugin-template`
- `./gradlew runIde`로 테스트 IDE 실행 가능
- 복잡하지만 IDE 내부 구조를 깊게 확장 가능

###  VS Code 확장 (플러그인)
- TypeScript or JavaScript 기반
- `yo code` → 자동 템플릿 생성
- `package.json`으로 명령어, UI, 명령 정의
- 브라우저 기반 구조라 가볍고 배우기 쉬움
- 마켓 등록도 쉽고, 배포 빠름  


## Eclipse 플러그인 기능을 IntelliJ로 옮길 때 고려사항

### 1. 기능 차이와 제약 요약

| 구분                      | Eclipse 플러그인 기능                                  | IntelliJ 플러그인 대응 가능 여부 및 특이점                      |
|--------------------------|------------------------------------------------------|--------------------------------------------------------------|
| **UI 확장 (뷰, 뷰포인트)**   | Eclipse는 복잡한 뷰 패널, 뷰포인트 API 제공               | IntelliJ는 Tool Window, Editor Tab, Popup 등으로 대체 가능하지만 API 구조 다름 |
| **작업 스케줄링 / Job API** | Eclipse Job API가 강력하며 백그라운드 작업에 최적화        | IntelliJ는 Background Task API 제공, 호출 방식과 콜백 구조 다름         |
| **프로젝트 탐색기 커스터마이징** | 프로젝트 뷰 커스터마이징 가능                              | IntelliJ 프로젝트 뷰 확장 가능하나 구현 방법, 트리 모델이 다름           |
| **플러그인 매니페스트 설정 (plugin.xml)** | 엄격한 XML 기반, 액션, 확장점 선언                          | plugin.xml 존재하나 구조 다르며 Java/Kotlin 코드와 연계 중요             |
| **에디터 내 커스텀 편집기 (Editor Part)** | EditorPart API로 커스텀 편집기 구현 가능                     | IntelliJ Editor API 이용 가능하지만 일부 기능 제한적                    |
| **리스너, 이벤트 처리**      | 다양한 Eclipse 이벤트, 리스너 API 존재                     | IntelliJ 이벤트 시스템 다르며 대응 이벤트 별도 구현 필요                 |
| **디버깅 관련 통합**         | Eclipse Debug Framework 사용                             | IntelliJ Debug API 사용, 접근 방식 다름 및 일부 직접 구현 필요           |
| **빌드 및 런 설정**          | Eclipse 빌드 스크립트, 런 컨피그 정의                     | IntelliJ Run Configuration API 존재하나 설정 방식, 확장성 다름          |
| **플러그인 내 다른 플러그인과 통합** | 확장점(Extension Point) 기반 강력한 통합                    | IntelliJ도 Extension Point 존재하지만 구현법과 확장 포인트 다름          |

### 2. 특히 조심해야 할 부분

- **Eclipse 뷰(View) → IntelliJ Tool Window 전환**  
  UI 역할 분리가 달라 UI 재설계 필요

- **Job API와 스레딩 모델 차이**  
  API 호출 방식과 스레드 관리 방식 다름

- **Extension Point 구조 차이**  
  Eclipse는 XML 선언 중심, IntelliJ는 코드와 XML 혼합 구조

- **에디터 커스텀 기능 차이**  
  IntelliJ는 일부 기능 제한과 플랫폼 기반 제약 존재

### 3. 실제로는 “직접 코드 수정 + 아키텍처 재설계” 필수

- 1:1 변환 불가
- 기능별 API 사용법 완전히 다름
- 동일 기능이라도 구현 방법 자체가 달라짐
- 기존 Eclipse 코드 참고하며 IntelliJ SDK 문서, 샘플 참고해 다시 개발 필요

### 4. 추천하는 접근법

1. IntelliJ 플러그인 SDK 문서 꼼꼼히 읽기
2. 기존 Eclipse 플러그인 기능 리스트 작성 후 IntelliJ API 매핑 시도
3. UI 요소는 IntelliJ Tool Window 또는 Editor Tab으로 재설계
4. 점진적 포팅, 한 기능씩 옮기며 테스트
5. JetBrains 커뮤니티, GitHub 샘플 적극 활용



## Eclipse 플러그인을 VS Code 플러그인으로 옮길 때 문제점

### 1. 주요 기능 차이 및 제약

| 구분                      | Eclipse 플러그인 기능                                  | VS Code 플러그인 대응 가능 여부 및 제약                         |
|--------------------------|------------------------------------------------------|--------------------------------------------------------------|
| **UI 확장 (뷰, 뷰포인트)**   | 복잡한 뷰 패널, 뷰포인트 API 제공                      | VS Code는 WebView, 사이드바, 패널 등 제공하나 Eclipse만큼 복잡한 뷰 구조 지원은 제한적 |
| **프로젝트 탐색기 커스터마이징** | 프로젝트 탐색기 완전 커스터마이징 가능                   | VS Code는 제한적. TreeView API는 있으나 Eclipse처럼 깊은 통합은 어려움       |
| **작업 스케줄링 / Job API** | Eclipse Job API로 강력한 백그라운드 작업 지원             | VS Code는 Node.js 이벤트 루프 기반, 별도의 Job API 없고 비동기 콜백 중심        |
| **플러그인 매니페스트 (plugin.xml)** | XML 기반 선언적 설정                                   | VS Code는 JSON 기반 `package.json` 사용, 선언 방식 단순하지만 XML만큼 복잡한 설정 불가 |
| **에디터 내 커스텀 편집기 (Editor Part)** | EditorPart API로 커스텀 에디터 구현 가능                 | VS Code는 CustomEditor API 존재하나 Eclipse 만큼 기능적 깊이는 제한적         |
| **리스너, 이벤트 처리**      | Eclipse 이벤트, 리스너 API 풍부                           | VS Code는 제한된 이벤트 모델, Node.js 이벤트 중심으로 동작                   |
| **디버깅 관련 통합**         | Eclipse Debug Framework 사용                             | VS Code는 Debug Adapter Protocol 기반, 자체 디버거 작성은 비교적 복잡함       |
| **빌드 및 런 설정**          | Eclipse 빌드 스크립트, 런 컨피그 정의                     | VS Code는 자체 빌드 시스템 없고, 외부 빌드 도구 연동 위주                     |
| **다른 플러그인과 통합**     | Extension Point 통한 강력한 통합                          | VS Code는 API 호출 및 이벤트 기반, Eclipse만큼 강력한 통합은 어려움            |

### 2. 주의할 점

- **UI 및 뷰어 구조 차이**  
  Eclipse처럼 복잡한 뷰 계층 구조 구현 불가  
  대신 WebView로 HTML/CSS UI 구현 가능하지만 별도 개발 부담

- **비동기 작업 처리 방식**  
  Eclipse Job API와 달리 VS Code는 Node.js 이벤트 루프, async/await, Promise 기반  
  기존 동기/스레드 방식 코드 변환 필요

- **플러그인 설정 및 명령어 등록**  
  XML → JSON 전환, 선언 방식 단순화되어 일부 세밀한 설정 어려움

- **디버깅 통합 방식 차이**  
  VS Code는 Debug Adapter Protocol 사용, Eclipse 디버그 프레임워크 직접 이식 불가

- **빌드 및 런 타임 환경 의존성**  
  VS Code 자체 빌드 시스템 없음, 외부 빌드 도구에 의존

### 3. 결론

- **Eclipse 플러그인 → VS Code 확장**은 단순 1:1 이식이 거의 불가능
- UI, 작업 스케줄링, 디버깅, 프로젝트 탐색기 등 주요 기능 재설계 필요
- TypeScript/JavaScript 환경에 맞게 아키텍처 변경과 코드 리팩토링 필수
- Web 기술 기반 UI 개발 능력 요구됨
