# 뷰 결합 

```
모듈에서 사용 설정된 뷰 결합은 모듈에 있는 각 xml 레이아웃 파일의 결합 클래스를 생성한다. 바인딩 클래스의 인스턴스에는 상응하는   
레이아웃에 ID가 있는 모든 뷰의 직접 참조가 포함된다. 대부분의 경우 뷰 결합이 "findViewById"를 대체한다.
```

### 설정안내
= 뷰 결합은 Android Studio 3.6 Canary 11 이상에서 사용 가능하다.
```

뷰 결합은 모듈별로 사용 설정된다. 모듈에서 뷰 결합을 사용 설정하려면 다음과 같이 viewBinding 요소를 build.gradle 파일에 복사한다.

```

```groovy
android {
        ...
        viewBinding {
            enabled = true
        }
    }
    
    
//    
plugins {
  id 'kotlin-android-extensions'
}
//
```
```
결합 클래스를 생성하는 동안 레이아웃을 무시하려면 아래와 같이 속성을 추가한다 [레이웃 파일의 루트 뷰에]
```
```xml
<LinearLayout
            ...
            tools:viewBindingIgnore="true" >
        ...
    </LinearLayout>
    
```

### 사용

```
모듈에 뷰 결합을 사용하도록 설정되면 모듈에 포함된 각 xml 레이아웃 파일의 결합 클래스가 생성된다. 각 결합 클래스에는
루트 뷰 및 id가 있는 모든 뷰의 참조가 포함된다. 결합 클래스의 이름은 xml 파일의 이름을 카멜 표기법으로 변환하고 끝에
Binding을 추가하여 생성된다.
예를 들어 xml파일 이름이 result_profile.xml 인 경우 다음과 같다.
```

```xml
<LinearLayout ... >
        <TextView android:id="@+id/name" />
        <ImageView android:cropToPadding="true" />
        <Button android:id="@+id/button"
            android:background="@drawable/rounded_button" />
    </LinearLayout>
```

```
생성된 결합 클래스 이름은 ResultProfileBinding이 된다. 이 클래스에는 name 이라는 textView와 button 이라는 button 등 두
필드가 있따 레이아웃의 imageView에는 id가 없으므로 결합 클래스에 참조가 없다.
또한 모든 결합 클래스에는 상응하는 레이아웃 파일의 루트 뷰에 관한 직접 참조를 제공하는 getRoot() 메소드가 포함된다. 
이 예에서는 ResultProfileBinding 클래스의 getRoot() 메소드가 LinearLayout 루트 뷰를 반환한다.
```

#### activity에서 뷰 결합 사용
```
액티비티에서 사용할 결합 클래스 인스턴스를 설정하려면 액티비티의 onCreate() 메소드에서 다음과 같이 진행한다.
1. 생성된 결합 클래스에 포함된 정적 inflate() 메소드를 호출한다. 그러면 액티비티에서 사용할 결합 클래스 인스턴스가 생성된다.
2. getRoot() 메소드를 호출하거나 KOtlin 속성 구문을 사용하여 루트 뷰 참조를 가져온다.
3. 루트 뷰를 setContentView()에 전달하여 화면상의 활성 뷰로 만든다.
```

```kotlin
 private lateinit var binding: ResultProfileBinding

    override fun onCreate(savedInstanceState: Bundle) {
        super.onCreate(savedInstanceState)
        binding = ResultProfileBinding.inflate(layoutInflater)
        val view = binding.root
        setContentView(view)
    }
    

  binding.name.text = viewModel.name
  binding.button.setOnClickListener { viewModel.userClicked() }
```


#### Fragment에서 뷰 결합 사용
```
프래그먼트에 사용할 결합 클래스 인스턴스를 설정하려면 프래그먼트의 onCreateView() 메소드에서 다음 단계를 따른다.
1. 생성된 결합 클래스에 포함된 정적 inflate() 메소드를 호출한다. 그러면 프래그먼트에서 사용할 결합 클래스 인스턴스가 생성된다.
2. getRoot() 메소드를 호출하여 루트 뷰 참조를 가져온다.
3. onCreateView()메소드에서 루트 뷰를 반환하여 화면상의 활성 뷰로 만든다.
***
inflate() 메소드를 사용하려면 레이아웃 인플레이터를 전달해야 한다. [layoutInflater] 레이아웃이 이미 확장되어 있다면
결합 클래스의 정적 bind() 메소드를 호출하면 된다.
***
```


```kotlin
private var _binding: ResultProfileBinding? = null
    // This property is only valid between onCreateView and
    // onDestroyView.
    private val binding get() = _binding!!

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        _binding = ResultProfileBinding.inflate(inflater, container, false)
        val view = binding.root
        return view
    }

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
    
 binding.name.text = viewModel.name
binding.button.setOnClickListener { viewModel.userClicked() }
    
```

#### 이미 확장되어있다면

```kotlin
class BindFragment : Fragment(R.layout.fragment_blank) {

    // Scoped to the lifecycle of the fragment's view (between onCreateView and onDestroyView)
    private var fragmentBlankBinding: FragmentBlankBinding? = null

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        val binding = FragmentBlankBinding.bind(view)
        fragmentBlankBinding = binding
        binding.textViewFragment.text = getString(string.hello_from_vb_bindfragment)
    }

    override fun onDestroyView() {
        // Consider not storing the binding instance in a field, if not needed.
        fragmentBlankBinding = null
        super.onDestroyView()
    }
}
```

### findViewById와의 차이점
```
뷰 결합에는 findViewById를 사용하는 것에 비해 다음과 같은 중요한 장점이 있다
Null안전: 뷰 결합은 뷰의 직접 참조를 생성하므로 유효하지 않은 뷰 ID로 인해 null 포인터 예외가 발생할 위험이 없다.
        또한 레이아웃의 일부 구성에만 뷰가 있는 경우 결합 클래스에서 참조를 포함하는 필드가 @nullable로 표시된다.

유형안전: 각 바인딩 클래스에 있는 필드의 유형이 xml 파일에서 참조하는 뷰와 일치한다. 즉 클래스 변환 예외가 
발생할 위험이 없다.
```

##### 출처: https://developer.android.com/topic/libraries/view-binding?hl=ko
