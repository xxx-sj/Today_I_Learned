# onClick [클릭 이벤트]


#### activity_main

```xml
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="button"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        android:onClick="sendMessage" />
```


```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
    }

    fun sendMessage(view: View) {
        //do something

    }
}
```

``` kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)


        val mainButton: Button = this.findViewById<Button>(R.id.main_button)

        mainButton.setOnClickListener(object: View.OnClickListener {
            override fun onClick(view: View?) {
                //do something
            }
        })
    }
```

```
onClick 메소드에는 속성과 호환으로 인식하기 위해 몇가지 조건이 있다.
1. public 메소드 이여야 한다.
2. return 이 void 혹은 unit 이여야 한다.
3. 매개변수로 view를 받아야 한다. 여기서 view는 마지막으로 클릭한, view 객체이다 [button]

```



##### 출처: android_developers_document
