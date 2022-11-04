# 색상 상태 목록 리소스


####  파일 위치 : res/color/ finame.xml

#### 컴파일된 리소스 데이터 유형: ColorStateList

```
코드에서 color를 참조 시에는 R.color.filename
리소스에서 참조시 @[package:]color/filename [동일 패키지 시 패키지명 생략가능]
```
```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_pressed="true"
          android:color="#ffff0000"/> <!-- pressed -->
    <item android:state_focused="true"
          android:color="#ff0000ff"/> <!-- focused -->
    <item android:color="#ff000000"/> <!-- default -->
</selector>
```



##### 출처: [android_developers_document](https://developer.android.com/guide/topics/resources/color-list-resource?hl=ko)
