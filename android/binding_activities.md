# Intent build [두 액티비티 간 바인딩]



```kotlin
const val EXTRA_MESSAGE = "com.example.android_developer.MESSAGE"

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
//        val mainButton: Button = this.findViewById<Button>(R.id.main_button)
//
//        mainButton.setOnClickListener(object: View.OnClickListener {
//            override fun onClick(view: View?) {
//                //do something
//            }
//        })
    }



    fun sendMessage(view: View) {
        val editText = view.findViewById<TextView>(R.id.main_textView)
        val message = editText.text.toString()
        val intent = Intent(this, DisplayMessageActivity::class.java).apply {
            putExtra(EXTRA_MESSAGE, message)
        }

        startActivity(intent)
    }
}
```

```
sendMessage()에서 이루어지는 작업순서
1. Intent 생성자는 두 매개변수, Context[this(mainActivity)] 와 class[DisplayMessageActivity::class.java(연결할 activity)] 를 사용한다.
    Activity 클래스는 context의 서브 클래스(child class)이므로 context 매개변수를 먼저 사용한다.
    이 경우 시스템에서 Intent, 를 전송하는 앱 구성요소의 class 매개변수는 연결할 activity 이다.
2. putExtra() 메서드는 EditText 값을 인텐트에 추가한다. Intent는 extras라는 키-값 쌍으로 데이터 유형을 전달할 수 있다.
    activity에서 키를 사용하여 텍스트값을 가져오므로 키는 공개 상수 EXTRA_MESSAGE 이다. 앱의 패키지 이름을 접두사로 사용해 인테트 extra 키를 정의하는게 좋다.
    [com.example.android_developer] 그러면 앱이 다른 앱과 상호작용하는 경우 키가 고유하게 유지된다.
3. startActivity() 메서드는 Intent에서 지정하는 DisplayMessageActivity의 인스턴스를 시작한다.

```

#### 절달받은 데이터 표시하기

#### DisplayMessageActivity
```kotlin
class DisplayMessageActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_display_message)

        val message = intent.getStringExtra(EXTRA_MESSAGE)
        
        val textView = findViewById<TextView>(R.id.textView).apply {
            text = message
        }
    }
}

```


##### 출처: android_developers_document
