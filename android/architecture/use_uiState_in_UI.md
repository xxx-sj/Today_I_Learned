# UI 상태 사용

```
UI에서 UiState객체의 스트림을 사용하려면 사용 중인 관찰 가능한(observable) 데이터 유형의 터미널 연산자를 사용한다.
예를들어 LiveDate의 경우 observe() 메소드를 사용하고 kotlin흐름의 경우 collect() 메서드나 이 메소드를 변형하여 사용한다.

UI에서 관찰 가능한 데이터 홀더를 사용할 때는 UI수명 주기를 고려해야 한다. 수명 주기를 고려해야 하는 이유는 사용자에게   
뷰가 표시되지 않을 때 UI가 UI 상태를 관찰해서는 안되기 때문이다.(뷰가 표시되지않음에도 UI에서 UIState를 관찰하고 있으면 안됨)
LiveDate에서는 LifecycleOwner가 수명 주기 문제를 암시적으로 처리한다. 흐름을 사용할 때는 적절한 코루틴 범위와   
repeatOnLifecycle API로 처리하는 것이 가장 좋다
```

```kotlin
class NewsActivity: AppCompatActivity() {
  private val viewModel: NewsViewModel by viewModel()
  
  ~~~ onCreate(args..) {
  
  ...
  
    lifecycleScope.launch {
      repeatOnLifecycle(Lifecycle.State.STARTED) {
        viewmodel.uiState.collect {
        
        }
      
      }
    }
  }

}
```

### 진행중인 작업 표시
```
UIState 클래스의 로드 상태를 나타내는 간단한 방법은 불리언 필드(flag)를 사용하는 것이다.
```

```kotlin
data class NewsUiState(
val isFetchingArticels: Boolean = false,
)


class NewsActivity : AppCompatActivity() {

    private val viewModel: NewsViewModel by viewModels()

    override fun onCreate(savedInstanceState: Bundle?) {
        ...

        lifecycleScope.launch {
            repeatOnLifecycle(Lifecycle.State.STARTED) {
                viewModel.uiState
                    .map { it.isFetchingArticles }
                    .distinctUntilChanged()
                    .collect { progressBar.isVisible = it }
            }
        }
    }
}

```

#### 화면에 오류표시

```
data class Message(val id: Long, val message: String)

data class NewsUiState(
    val userMessages: List<Message> = listOf(),
    ...
)
```

##### 출처: https://developer.android.com/jetpack/guide/ui-layer?hl=ko#animations
