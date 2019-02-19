[원문](https://docs.gradle.org/5.0/userguide/kotlin_dsl.html)

# Gradle 코틀린 DSL 입문서
### 목차
- [전제조건](#전제조건)
- [IDE 지원사항](#IDE-지원사항)
- [코틀린 DSL 스크립트](#코틀린-DSL-스크립트)
- Type-safe한 모델 접근자
- Multi-Project에서 빌드하기
- `plugins {}` 를 사용할수 없을때
- container 객체와 사용하기
- 런타임 속성(properties)과 사용하기
- 코틀린 DSL 플러그인
- 내장(embedded) 코틀린 코드
- 그루비와 코틀린 함께 사용하기(상호운용성)
- 한계

Gradle의 코틀린 DSL은 이전에 사용되오던 Groovy DSL을 대체할 수 있는 여러 구문들을 제공합니다. 
이를 통해 여러 IDE들의 내용완성(content assist), 리팩토링, 문서화 등을 제공받을 수 있습니다. 
이 문서에서는 코틀린 DSL의 주요 작성법과 Gradle API의 사용법에 대해 다룹니다.

> Note : 이미 만들어놓은 Gradle를 코틀린 DSL로 마이그레이션하는데 관심이 있는경우 [마이그레이션 가이드](.)를 읽어보세요

### 전제조건
- 내장(embedded) 코틀린 컴파일러는 리눅스, 맥OS, 윈도우, Cygwin, FreeBSD와 x86-64 기반 Solaris 에서 동작합니다.
- 코틀린 문법과 기본 기능들은 본 문서를 이해하는데 많은 도움이 됩니다. [코틀린 레퍼런스 문서](https://kotlinlang.org/docs/reference/) 나 [Kotlin Tutorial](https://kotlinlang.org/docs/tutorials/koans.html) 문서를 참고하세요.
- `plugin {}` 블록을 사용하면 여러 IDE 지원을 받을수 있으니 추천드립니다.

### IDE 지원사항
IntelliJ와 Android Studio는 완전하게 Kotlin DSL을 지원합니다. 다른 IDE들은 아직은 완벽하게 지원하진 않으나, 사용할 수는 있습니다.

|  | Build import | Syntax highlighting | Semantic editor |
|--------:|:--------:|:--------:|:--------:|
| IntelliJ IDEA | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| Android Studio | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| Eclipse | :white_check_mark: | :white_check_mark: | :x: |
| CLion | :white_check_mark: | :white_check_mark: | :x: |
| Apache Netbeans | :white_check_mark: | :white_check_mark: | :x: |
| Visual Studio Code(LSP) | :white_check_mark: | :white_check_mark: | :x: |
| Visual Studio | :white_check_mark: | :x: | :x: |

* Syntax highlighting : Gradle 코틀린 DSL 코드에서 문법별 코드 색상화
* Semantic editor : 코드 자동완성, 코드내에서 이동, 문서화, 리팩토링 등

아래 한계에서 언급되듯이, Gradle 모델에서 프로젝트를 가져와야 위 도구들이 지원됩니다.

추가적으로, IntelliJ 와 Android Studio는 편집을 할때 최대 3개의 Gradle 데몬이 동작될 수 있습니다. (빌드, 설정파일, 초기화 스크립트)
설정이 느리게 되어있다면 IDE 반응성에 영향을 줄 수 있습니다. 그럴경우 [성능향상가이드](https://guides.gradle.org/performance/#configuration) 를  확인해주세요.  

#### 자동 import vs 자동 의존성 추가
IntelliJ와 그에서 파생된 Android Studio 둘 다 빌드 로직의 변경을 감지해 두가지 옵션을 제공합니다.
1. 모든 변경사항 다시 불러오기
![intellij-build-import-popup](https://docs.gradle.org/5.0/userguide/img/intellij-build-import-popup.png)
2. 스크립트를 변경할 때마다 의존성 불러오기
![intellij-script-dependencies-reload](https://docs.gradle.org/5.0/userguide/img/intellij-script-dependencies-reload.png)

1번은 비활성화하고 2번은 활성화하는것을 권장합니다. 그렇게하면 스크립트를 편집하는동안 피드백을 빠르게 받을 수 있고, 전체 빌드가 되는 시점을 제어할 수 있습니다.

> 역자 : 1번 방식은 상대적으로 오래걸리기에, 수동으로 시점을 제어하는걸 권장하는거라고 생각합니다.

#### 문제 해결하기
IDE 지원은 아래 두 모듈을 통해 제공됩니다.
- IntelliJ/Android Studio를 통해 사용되는 kotlin plugin
- Gradle

지원되는 내용은 각 버전에 따라 다릅니다.

문제가 발생했다면, 제일먼저 커맨드에서 `./gradlew tasks` 를 실행해 IDE 문제인지 Gradle 문제인지 부터 확인해 보세요. 
커맨드에서 동일한 문제가 발생한다면 그것은 IDE문제가 아닌 빌드의 문제입니다.

커맨드에선 빌드가 잘 된다면, IDE의 캐시를 지우고 다시 실행해보세요.

위 시도를 해도 동작하지 않아, IDE의 문제가 있다고 생각되면 다음을 시도해 볼 수 있습니다.
- `./gradlew tasks` 에서 더 자세한 정보 살펴보기
- 아래 경로에 존재하는 로그를 확인해보기
  - `$HOME/Library/Logs/gradle-kotlin-dsl` (맥 OS X 기준)
  - `$HOME/.gradle-kotlin-dsl/logs` (리눅스 기준)
  - `$HOME/Application Data/gradle-kotlin-dsl/log` (윈도우 기준)
- [Kotlin Issue Tracker](https://github.com/gradle/kotlin-dsl/issues/)에 이슈남기기

IDE 문제일 경우 해당 이슈 트래커에 문의해주세요
- [JetBrain IDEA](https://docs.gradle.org/5.0/userguide/kotlin_dsl.html)
- [Google Android Studio](https://docs.gradle.org/5.0/userguide/kotlin_dsl.html)

### 코틀린 DSL 스크립트
Groovy 기반의 환경에서 처럼, Kotlin DSL도 Gradle의 Java API를 통해 구현됩니다.
Kotlin DSL의 모든 코틀린 코드들은 Gradle을 통해 컴파일되고 실행됩니다.
객체, 함수, 속성들은 Gradle API 와 추가된 플러그인들을 통해 사용됩니다.

#### 스크립트 파일 네이밍
- Groovy DSL은 `.gradle` 확장자를 사용합니다.
- Kotlin DSL은 `.gradle.kts` 확장자를 사용합니다.

Kotlin DSL을 활성화하려면, `.gradle` 확장자를 `.gradle.kts` 로 바꾸세요. 그리고 설정 및 초기화 파일에도 적용하면 됩니다.
Groovy DSL 스크립트와 Kotlin DSL 스크립트를 섞어서 사용할 수 있다는걸 기억하세요.

IDE의 원활한 지원을 위해 아래 규칙을 따르는걸 권장합니다.
- 설정 스크립트(혹은 `Settings` 객체와 관련된 스크립트)는 `*.settings.gradle.kts` 패턴으로 하세요. 
- 초기화(initialization) 스크립트는 `*.init.gradle.kts` 혹은 `init.gradle.kts` 패턴으로 하세요.
