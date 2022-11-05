# 드로어블 리소스 가져오기




```xml
<ImageView
android:src="@drawable/image" />
```

```kotlin
import androidx.core.content.res.ResourcesCompat;
                                                          activity                          [id]    
va drawable = Drawable? = ResourcesCompat.getDrawable([AppCompatActivity().]resources, R.drawable.image, null)
```




##### 출처 : https://developer.android.com/guide/topics/resources/drawable-resource?hl=ko#kotlin
