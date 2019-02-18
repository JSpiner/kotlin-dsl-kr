[원문](https://docs.gradle.org/5.0/userguide/kotlin_dsl.html)

# Gradle 코틀린 DSL 입문서
### 목차
- 전제조건
- IDE 지원사항
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

Gradle의 Kotlin DSL은 이전에 사용되오던 Groovy DSL을 대체할 수 있는 여러 구문들을 제공합니다. 
이를 통해 여러 IDE들의 내용완성(content assist), 리팩토링, 문서화 등을 제공받을 수 있습니다. 
이 문서에서는 
