# UI 상태 노출
```
UDF를 사용하여 상태 생성을 관리하므로 생성된 상태를 stream으로 간줄 할 수 있다. 
즉, 시간 경과에 따라 여러 버전의 상태가 생성된다.[update된 state datas..]
따라서 [LiveData] 또는 [StateFlow]와 같이 관찰 가능한 [observable] 데이터 홀더에 UI 상태를 노충해야 한다.
이유는 viewModel에서 데이터를 직접 가져오지 않고도[get 하지않아도] UI가 상태 변경사항에 반응할 수 있또록 하기 위해서 이다.
이러한 유형은 항상 최신 버전의 UI 상태를 캐시한다는 이점이 있다.
```

```kotlin
class NewViewModel(args...): ViewModel() {
  val uiState: StateFlow<NewUiState> = ...
}
```

```
UI에 노출되는 데이터가 비교적 간단할 때는 UI 상태 유형으로 데이터를 래핑하는 것이 좋은 경우가 많다. 내보낸 상태 홀더와
관련 화면/UI 요소 간의 관계를 전달하기 때문이다. 또한 UI 요소가 더 복잡해질 때 언제나 간편하게 UI 상태 정의를 추가하여 
UI 요소를 렌더링하는 데 필요한 더 많은 정보를 포함할 수 있다.
UIState 스트림을 만드는 일반적인 방법은 ViewModel에서 지원되는 변경 가능한 스트림을 변경 불가능한 스트림으로 노출하는 것이다.
[변경가능한 스트림은 data layer에서의 update 와같은 데이터 변경 시에 필요하다. 변경 불가능한 스트림은 viewmodel에서 ui에게 주는 데이터이다.]
예를 들어 MutableStateFlow<UiState>를 StateFlow<UiState>로 노출한다. [노출한다는 의미는 public으로 제공한다 이다.]
```

```kotlin
class NewsViewModel(args...): ViewModel() {
  private val _uiState = MutableStateFlow(NewsUiState())
  val uiState: StateFlow<NewsUiState> = _uiState.asStateFlow()
}
```

```kotlin
c;ass NewsModel(private val repository: NewsRepository,
...
): ViewModel() {

  private val _uiState = MutableStateFlow(NewsUiState())
  val uiState: StateFlow<NewsUiState> = _uiState.asStateFlow()
  
  //update 
  private var fetchJob: Job? = null
  
  //update method public
  fun fetchArticles(category: String) {
    fetchJob?.cancel()
    fetchJob = viewModelScope.lauch {
      try {
            val newsItems = repository.newsItemForCategory(category)
            _uiState.update {
              it.copy(newsItem = newsItems)
              }
          } catch (ioe: IOException) {
              _uiStete.update {
                  val message = getMessagesFromThrowable(ioe)
                  it.copy(userMessage = message)
                }
            }
      }           
}
```

### 추가 고려사항
```
UI 상태 객체는 서로 관련성 있는 상태를 처리해야 한다. 
"뉴스 항목 목록"과 "북마크 수"를 서로 다른 두 스트림에 노출하면 한 스트림이 업데이트되고 다른 스트림은   
업데이트되지 않은 상황이 발생할 수 있다. 단일 스트림을 사용하면 두 요소가 모두 최신 상태로 유지된다.[2개를 묶어서]
또한 일부 비즈니스 로직에는 소스 조합이 필요할 수 있다. 예를들어, 로그인한 상태인 동시에 프리미엄 뉴스 서비스 구독자인 사용자  
에게만 북마크 버튼을 표시해야 한다면 [login + premium] 다음과 같이 UI 상태 클래스를 정의하면 된다.
```

```kotlin
data class NewsUiState (
  val isSignedIn: Boolean = false, //login
  val isPremium: Boolean = false // premium
  val newItems: List<NewsItemUiState> = listOf()
)

val NewsUiState.canBookmarkNews: Boolean get() = isSignedIn && isPremium
```

#### UI 상태
```
단일 스트림 인지 여러 스트림인지. 
UI 상태 노출 대상을 단일 스트림과 여러 스트림 중에서 선택할 때 주요 원칙은 이 전 글머리기호에서 설명한 내보낸 항목간의
관계이다. 단일 스트림 노출의 가장 큰 장점은 편의성과 데이터 일관성이다.
즉, 상태 사용자가 언제나 즉시 최신 정보를 확인할 수 있다. 하지만 다음과 같이 viewmodel 상태의 스트림이 별개일 때 적합한 경우가 있다.

- 관련없는 데이터 유형
  UI를 렌더링하는 데 필요한 일부 상태는 서로 완전히 별개일 수 있다. 이때 서로 다른 상태를 함꼐 번들로 묶는 데 드는 비용이  
  이점보다 더 클 수 있으며 이는 상태 중 하나가 다른 상태보다 더 자주 업데이트 되는 경우가 그렇다.
- UiState 비교 (diff): UiState 객체에 필드가 많을수록 필드 중 하나를 업데이트하면 스트림이 내보내질 가능성이 크다. 
  뷰에는 연속적으로 이루어지는 내보내기가 같은지 다른지 파악하는 비교 매커니즘이 없으므로 내보내기를 할 때마다 뷰가   
  업데이트 된다. 따라서 Flow API 또는 LiveData의 idstinctUnitlChanged와 같은 메서드를 사용한 완화작업이 필요할 수 있다.
```

##### 출처:  https://developer.android.com/jetpack/guide/ui-layer?hl=ko#views
