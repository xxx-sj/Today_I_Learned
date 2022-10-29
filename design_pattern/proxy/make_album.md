# 앨범 커버 뷰어 만들기


```
스윙을 이용해 만든다고 가정했을 때, 아이콘 객체를 만들고 그 객체로 하여금 네트워크로 이미지를 불러 오도록 할 수 있다.   
이런 방법을 사용할 때 네트워크의 상태와 인터넷 연결 속도에 따라서 앨범 커버 이미지를 가져오는 데 시간이 걸릴 수 있으므로    
이미지를 불러오는 동안 화면에 뭔가 보여 주면 좋다. 그리고 이미지를 기다리는 동안 어플리케이션 전체가 작동을 멈춰서도 안된다.    
가상프록시를 사용하면 이 2가지 조건을 간단하게 만족할 수 있다.    

가상 프록시가 아이콘 대신 백그라운드에서 이미지를 불러오는 작업을 처리하고, 이미지를 완전히 가져오기 전까지는 "불러오는 중입니다" 와 같은    
메시지를 보여주면 된다. 이미지 로딩이 끝나면 프록시는 아이콘 객체에게 모든 작업을 넘기면 된다.
```

### 앨범 커버 가상 프록시 설계하기

```java
public interface Icon {

    void getIconWidth();
    void getIconHeight();
    void paintIcon();


public class ImageIcon implements Icon{

    @Override
    public void getIconWidth() {}

    @Override
    public void getIconHeight() {}


    @Override
    public void paintIcon() {}
}



// 처음에는 화면에 간단한 메시지를 표시해 주다가, 이미지 로딩이 끝나면 디스플레이 작업을 imageIcon에 위임하는 프록시
public class ImageProxy implements Icon {

    private Icon imageIcon;

    @Override
    public void getIconWidth() {}

    @Override
    public void getIconHeight() {}

    @Override
    public void paintIcon() {}
}

```

1. ImageProxy는 ImageIcon을 생성하고 네트워크 URL로 부터 이미지를 불러온다.
2. 이미지를 가져오는 동안 "기다려주세요" 라는 메시지를 화면에 표시한다
3. 이미지 로딩이 끝나면 paintIcon(), getWidth(), getHeight()를 비롯한 메소드 호출을 이미지 아이콘 객체에게 넘긴다.
4. 새로운 이미지 요청이 들어오면 프록시를 새로 만들고 위의 과정을 처음부터 다시 반복한다.


##### imageProxy
```java
public class ImageProxy implements Icon {

    private Icon imageIcon; //실제 아이콘 객체
    final URL imageURL;
    Thread retrievalThread;
    boolean retrieving = false;

    public ImageProxy(URl url) { this.imageIcon = url};

    @Override
    public void getIconWidth() {
        if (imageIcon != null) {
            return imageIcon.getIconWidth();
        } else {
            return 800;
        }
    }

    @Override
    public void getIconHeight() {
        if (imageIcon != null) {
            return imageIcon.getIconHeight();
        } else {
            return 600;
        }
    }

// imageicon은 2개의 서로 다른 스레드에서 사용한다.
// 따라서(읽기를 보호하는)변수를 volatile로 선언하면서 (쓰기를 보호하는)동기화된 세터 메소드를 시용한다.
    synchronized void setImageIcon(ImageIcon icon) {
        this.imageIcon = icon;
    }
    
    @Override
    public void paintIcon(final Component c, Graphics g, int x, int y) {
        if (imageIcon != null) {
            imageIcon.paintIcon(c, g, x, y);
        } else {
            g.drawString("불러오는 중입니다", x + 300, y + 190);
            if(!retrieving) {
            //이미지를 가져오는 중이 아니라면
                retrieving = true;
                
                // 이미지 로딩 작업을 시작한다. reaint 메소드는 하나의 스레드
                // 에서만 호출하므로 스레드 안전성은 확보되었다.
                  /**
                 * 진짜 아이콘 이미지를 로딩하는 부분 iconImage를 사용해서
                 * 이미지를 로딩하는 과정은 동기화되어 진핸된다.
                 * 이미지 로딩이 끝나기 전까지 imageIcon 생성자에서 아무것도
                 * 리턴된지 않는다. 따라서 화면을 갱싱할 수 없고, 아무 메시지도
                 * 출력할 수 없으므로 비동기 방식으로 작업을 처리해야 한다.
                 */
                 
                  //사용자 인터페이스가 죽지 않도록 별도의 스레드에서 이미지를 가져온다.
                retrievalThread = new Thread(new Runnable() {
                    @Override
                    public void run() {
                        try{
                             //스레드 내에서 icon 객체의 인스턴스를 생성하였다.
                            // 이미지가 완전히 로딩되어야 생성자가 객체를 리턴한다.
                            setImageIcon(new ImageIcon(imageURL, "Album Corver"));
                            //이미지가 확보되면 reaint()메소드를 호출해서 화면을 갱신한다.
                            c.repaint();
                        } catch (Exception e) {
                            e.printStackTrace();
                        }
                    }
                });
                retrievalThread.start();
            }
        }
    }
}

```


##### 보호 프록시 만들기
```
java.lang.reflect 패키지 안에 프록시 기능이 내장되어 있다. 이 패키지를 사용하면 즉석에서 하나 이상의    
인터페이스를 구현하고, 지정한 클래스에 메소드 호출을 전달하는 프록시 클래스를 만들 수 있다.    
진짜 프록시 클래스는 실행 중에 생성되므로 이러한 자바 기술을 "동적 프록시(dynamic proxy)"라고 부른다.
```


```java

public interface ProxySubjectInterface {
    void request();
}


public class ProxySubject implements ProxySubjectInterface{

    private Subject realSubject;

    @Override
    public void request() {}
}

public class RealSubject implements ProxySubjectInterface {

    @Override
    public void request() {}
}

public interface InvocationHandler {
    void invoke();
}

```

```
자바에서 proxy 클래스가 subject 인터페이스 전체를 구현한다.   
이번에는 프록시가 2개의 클래스로 구성된다.
proxy 객체의 모든 메소드 호출을 전달받는 invocationHandler를 제공해야 한다. invocationHandler에서 Realsubject객체에 있는   
메소드로의 접근을 제어한다.
```


```
자바에서 proxy 클래스를 생성해주므로 proxy 클래스에게 무슨 일을 해야 하는지 알려줘야 한다. 
이전처럼 프록시 클래스에 코드를 넣을 수 없기 때문에 InvocationHandler 클래스에 넣어야 한다.    
InvocationHandler는 프록시에 호출되는 모든 메소드에 응답한다.   
proxy에서 메소드 호출을 받으면 항상 InvocationHandler에 진짜 작업을 부탁한다고 생각하면 된다.
```

##### 동적 프록시를 활용해서 보호 프록시 만들기



##### 출처: 헤드퍼스트 디자인 패턴: 개정판
