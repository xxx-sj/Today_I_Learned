# 데이터 결합 라이브러리


### 기존코드
```kotlin

findViewById<TextView>(R.id.text_view).apply {
  text = viewModel.userName
}
```

```
findViewById를 통해 view를 찾아 text를 변경 
```

```
아래는 데이터 결합 라이브러리를 통해 레이아웃 파일에서 직접 위젯에 텍스트를 할당한다.
```
```xml
<TextView
        android:text="@{viewmodel.userName}" />
```

### 데이터 결합 라이브러리 사용

```
<layout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:app="http://schemas.android.com/apk/res-auto">
        <data>
            <variable
                name="viewmodel"
                type="com.myapp.data.ViewModel" />
        </data>
        <ConstraintLayout... /> <!-- UI layout's root element -->
    </layout>
```

##### 출처 : https://developer.android.com/topic/libraries/data-binding?hl=ko
