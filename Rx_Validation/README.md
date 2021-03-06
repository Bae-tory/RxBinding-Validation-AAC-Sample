## Features
- **로그인** 기능
- **회원가입** 기능
- **로그인 및 회원가입 결과** 보여주기
- **다크 모드** 지원

## App Flow
### 로그인 화면
- 앱 실행 시 로그인 화면이 나타남
- 이미 등록된 `계정이 있으면 로그인 기능 수행`
- 이미 등록된 계정이 있으나 비밀번호가 틀릴 시 에러
- 이미 등록된 계정이 없다면 로그인 실패
- `성공 시 유저정보 결과 화면`으로 이동
- 화면에서 하단 회원가입 버튼 클릭 시 회원가입 화면으로 이동
- 백버튼 1.5초 이내로 입력하면 수행

### 회원가입 화면
- 회원 가입 정보에 대한 `입력 및 검증 수행`
- 이메일, 비밀번호, 비밀번호 확인, 닉네임, 성별, 생일, 약관 동의 여부, 마케팅 동의 여부
- 만 14세 미만 가입 불가(400)
- 이미 등록된 계정 중복 가입 불가(401)
- 회원가입 `성공 시 유저정보 결과 화면`으로 이동
- 백버튼 1.5초 이내로 입력하면 수행

## Architecture, Design Pattern
- Presentation Module, Local Module
- Presentation 레이어는 View, ViewModel로 구성(Repository를 구현하지 않았기 때문에 완벽한 MVVM은 아닙니다.)
- Presentation, Local 데이터 간의 맵핑 적용
- DI를 이용하여 외부에서 의존성 주입

## 위의 Design Pattern 및 Architecture를 적용하려고 한 이유🛠
- 궁극적으로는 관심사의 분리이고 변화에 적게 고쳐지는 코드를 짜기 위함입니다.

- Presentation Module
  - 유저에게 보여지는 View와 View와 직접적으로 연관된 ViewModel 클래스들의 집합입니다.
  - 데이터를 어떻게 운용하고 가져오는지는 몰라도 되고 유저에게 어떻게 보여주는지 또는 뷰 컨트롤 관련된 로직을 수정할 때는 이 모듈을 보면 됩니다.
  
- Local Module
  - 회원가입, 로그인에 해당하는 회원 정보 캐시 데이터를 저장하는 역할을 합니다.
  - Presentation 레이어는 데이터가 어떠한 방식으로 오는지는 알 수 없고, 로컬 데이터를 관장하는 모듈을 따로 만들어 로컬 데이터 관련 로직이 바뀔 시에 이 부분에 대해서만 코드를 고치는 것에 집중하기 위하여 모듈을 분리하였습니다. 
  
- Mapping
  - 각 모듈간의 데이터들의 의존성을 낮추기 위하여 매핑을 하는 메서드를 구현하였습니다.
  
- View와 ViewModel로 이루어진 Presentation 레이어
  - MVP와 MVVM의 가장 큰 차이점은 MVVM에서의 View의 역할은 MVP보다 조금 더 능동적이라는 것입니다.
  - MVVM에서 이러한 역할을 View가 수행하는 것을 돕기 위해 구글에서는 여러 AAC들을 지원합니다.</br>(과제에서는 LiveData를 사용하지 않고 RxBinding,RxJava로 대체하였습니다.)
  - 데이터바인딩을 MVP에서도 사용이 가능하지만 MVVM구조에서 사용하는 것이 더 자연스러워 MVP 대신 MVVM을 택했습니다.
  - Hilt에서 ViewModelInject annotation을 지원합니다.
  - AAC Viewmodel를 통해 화면이 회전되어도 간단한 데이터를 보존할 수 있으며, Activity나 Fragment가 메모리에서 해제될 때 onCleared함수를 호출 하여 데이터 스트림을 dispose하는데 용이합니다.
  
## Library 에 대한 간략한 설명과 이유🛠
- [Hilt-Dagger](https://developer.android.com/training/dependency-injection/hilt-android?hl=ko)</br>
  - 생성자나 메서드를 통해 외부로부터 의존성 주입을 받으면 다음과 같은 이점이 있습니다.
  - 클래스간의 결합도를 느슨하게 할 수 있고 코드를 유연하게 짤 수 있습니다.
  - Stub 또는 Mock 객첼르 사용해서 단위테스트를 하기 더 쉬워집니다.
  - 개발자의 입장에서 어디서 객체를 만들어야 되는지 신경쓰지 않아도 되고 고치고 싶은 코드에 집중할 수 있습니다.
  - 의존성 주입 라이브러리에서 Koin, Dagger가 있는데 Hilt를 쓴 이유는 다음과 같습니다.
  - 먼저 Koin을 살펴보자면 매우 쓰기 쉽고 DI의 개념을 익히는데에는 좋습니다. 하지만 런타임 때 오버헤드가 발생할 수 있고 불안정합니다. 그 다음으로 Dagger를 살펴보자면 Koin보다 진입장벽이 높고 컴파일 때, annotation process를 통해 의존성 주입에 관련된 모든 코드들을 생성하기 때문에 안정적이고 명확한 디버깅이 가능합니다. 하지만 Dagger 자체는 보일러플레이트 코드의 양도 상당하고 진입장벽이 높습니다. Hilt는 이러한 Dagger의 사용을 단순화 시켜주고 쉬운 방법으로 다양한 빌드 타입에 대해 다른 바인딩을 제공합니다.
    
 
- [Hilt-ViewModel-KTX](https://developer.android.com/kotlin/ktx)

- [BundleOf](https://developer.android.com/kotlin/ktx/extensions-list?hl=ko)

- [RxJava](https://kotlinlang.org/docs/reference/coroutines-overview.html)

- [RxBinding](https://github.com/JakeWharton/RxBinding)  

- [Room](https://developer.android.com/topic/libraries/architecture/room)

- [Gradle Kotlin DSL](https://docs.gradle.org/current/userguide/kotlin_dsl.html) - For writing Gradle build scripts using Kotlin.

- [Dark Theme](https://developer.android.com/guide/topics/ui/look-and-feel/darktheme)
