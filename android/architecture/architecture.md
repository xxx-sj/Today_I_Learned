# 아키텍처



```
Activity와 fragment와 같은 UI 기반 클래스는 UI 및 운영체제 상호작용을 처리하는 로직만 포함해야한다. 
이러한 클래스를 최대한 가볍게 유지하여 구성요소 수명 주기와 관련된 많은 문제를 피하고 그러한 클래스의 테스트 가능성을 개선할 수 있다.
```


### 데이터 모델에서 UI 도출하기

```
데이터 모델은 앱의 데이터를 나타내며, 앱의 UI 요소 및 기타 구성요소로부터 독립되어 있다.   
즉, 이들은 UI 및 앱 구성요소 수명 주기와는 관련이 없다.
하지만 OS가 메모리에서 앱의 프로세르를 삭제하기로 결정하면 데이터 모델도 삭제된다.
```

### 권장 앱 아키텍쳐 [광범위한 앱에 적용]
```
화면에 애플리케이션 데이터를 포시하는 UI 레이어 [activity, fragment, viewmodels ...]
앱의 비즈니스 로직을 포함하고 애플리케이션 데이터를 노출하는 데이터 레이어 [dao, repositories..]
UI와 데이터 레이어 간의 상호작용을 간소화하고 재사용하기 위한 도메인 레이어라는 레이어를 추가할 수 있다. [optional]

```

### UI 레이어
```
UI 레이어의 역할은 화면에 어플리케이션 데이터를 표시하는 것이다. 사용자 상호작용(ex 버튼 누르기) 또는 외부 입력(ex 네트워크 응답)으로 인해   
데이터가 변할 때마다 변경사항을 반영하도록 UI가 업데이트 되어야 한다.
화면에 데이터를 렌더링하는 UI 요소. view
데이터를 보유하고 이를 UI에 노출하며 로직을 처리하는 상태 홀더 *viewModel
```

```
      DATA Layer
          |
          |
-----------------------
UI Layer              |
--------------------- |
view Model [UI state] |
   |                  |
   |                  |
UI element            |
-----------------------


상태는 아래로 향하고 이벤트는 위로 향하는 패턴을 단방향 데이터 흐름 이라고 한다.

- viewmodel이 ui에 사용될 UIstate를 보유하고 노출한다. UIstate는 viewmodel에 의해 변환된 어플리케이션 데이터[db data]이다.
- ui가 viewmodel에 사용자 이벤트를 알린다. [onClick, mouseover doubleClick...]
- viewmodel이 사용자 작업을 처리하고 상태를 업데이트 한다. [viewmodel에서 이벤트를 받아 uistate data를 update 한다.]
- 업데이트된 상태가 렌더링할 ui에 다시 제공한다. [update 된 uistate data를 다시 ui에 제공한다.]
```


```
북마크 예시
0. 데이터 레이어[db] 에서 현재 앱 데이터를 가져온다.
1. viewmodel은 data layer에서 받은 데이터를 가지고 ui state를 생성하여 ui를 update 한다.
2. user가 bookmark 요청을 보낸다 [event]
3. viewmodel은 해당 이벤트를 받아 data layer에게 uistate가 변경되었다고 알린다.
4. data layer는 변경된 데이터를 저장하고 update 한 후 update된 data를 UI layer에게 전달한다.
5. UI layer의 viewModel은 update된 데이터를 받아 UIstate를 update 한 후[? data update가 이부분인지 모름]  
   UI에게 새로운 UIstate를 전달한다.
```


### 로직의 유형
```
기사 북마크는 앱에 가치를 부여하므로 [비즈니스 로직] 의 예 이다.

다양한 로직 유형
- 비즈니스 로직
  상태 변경에 따라 진행해야 할 작업이다. 비즈니스 로직은 일반적으로 도메인 또는 데이터 레이어에 배치되지만  
  UI 레이어에는 배치되지 않는다.
- UI 동작 로직 or UI 로직
  상태 변경사항을 표시하는 방법. 예를 들어 android resources를 사용하여 화면에 표시할 올바른
  텍스트를 가져오거나(getString(R.string.~)), 사용자가 버튼을 클릭할 때 특정 화면으로 이동하거나 (fragmentManager.replace()..)
  toast message 또는 snackBar를 사용하여 화면에 사용자 메시지를 표시한다. 

특히 context 같은 UI 유형의 경우 UI 로직은 viewmodel이 아닌 UI에 있어야 한다. UI 로직을 다른 클래스에 위임하고자 하며 UI 가   
점점 복잡해지는 경우 간단한 클래스를 상태 홀더로 만들 수 있다. UI에서 생성된 간단한 클래스는 UI의 수명 주기를 따르기 때문이다.
viewmodel 객체의 수명은 더 길다.

```

##### 출처 : https://developer.android.com/topic/architecture?hl=ko#recommended-app-arch
