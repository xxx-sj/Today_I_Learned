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



##### 출처: android_developers_document
