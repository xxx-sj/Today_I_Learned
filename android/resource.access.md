# 앱 리소스 액세스

```
모든 리소스 ID는 aapt 도구가 자동으로 생성하는 프로젝트의 R 클래스에 정의된다.
애플리케이션이 컴파일되면 aapt가 R클래스를 생성하며, 이 클래스 안에 res/ 디렉터리에 있는 모든 리소스의 리소스ID가 들어있다.
코드에서 사용할 때는 아래와 같다
R 클래스의 서브 클래스에서 다음과 같이 정적 정수를 사용한다.
R.string.hello

XML: R클래스에서 정의된 리소스 ID에 상응하기도 하는 특수 XML 문법을 사용한다
@string/hello
```

### 시스템 리소스
```
시스템 리소르를 참조하기 위해서는 패키지 이름을 포함해야 한다.
동일 패키지 내에서는 생략가능하다.
```

```xml

android:textColor="@android:color/secondary_text_dark"
```

```xml
@[<package_name>:]<resource_type>/<resource_name>
```

##### 출처: android_developers_documents
