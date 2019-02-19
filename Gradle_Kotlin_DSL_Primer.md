[원문](https://docs.gradle.org/5.0/userguide/kotlin_dsl.html)

# Gradle 코틀린 DSL 입문서
### 목차
- [전제조건](#전제조건)
- [IDE 지원사항](#IDE-지원사항)
- 코틀린 DSL 스크립트
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
