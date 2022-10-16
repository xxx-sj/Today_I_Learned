# facade_pattern [퍼사드 패턴] 

```
서브시스템에 있는 일련의 인터페이스를 통합 인터페이스로 묶어준다. 또한 고수준 인터페이스도 정의하므로 서브시스템을
더 편리하게 사용할 수 있다.
```


```
퍼사드객체에 생성자를 통해 행동을 할 객체를 넘기고 넘겨받은 퍼사드 객체에서
한 행동을 할 때 메소드내에서 모두 처리한다.

```

```java
package main.designPattern.adapter;

public class FacadePattern {

    public static void main(String[] args) {

        // 구성요소 초기화
        //보통은 클라이언트에 퍼사드가 주어지므로 직접 구성 요소를 생성하지 않아도 된다.
        HomeTheaterFacade homeTheaterFacade =
                new HomeTheaterFacade((amp,  tuner, player, projector, screen
                , lights, popper);

        homeTheaterFacade.watchMovie("");
    }
}


class HomeTheaterFacade {

    //사용하고자 하는 서브시스템의 모든 구성 요소가 인스턴스 변수로 저장됨
    Amplifier amp;
    Tuner tuner;
    StreamingPlayer player;
    Projector projector;
    TheaterLights lights;
    Screen screen;
    PopcornPopper popper;

    //서브시스템 구성 요소의 레퍼런스가 전달된다.
    public HomeTheaterFacade(
            Amplifier amp,
            Tuner tuner,
            StreamingPlayer player,
            Projector projector,
            Screen screen,
            TheaterLights lights,
            PopcornPopper popper) {
        this.amp =amp;
        this.tuner = tuner;
        this.player = player;
        this.projector = projector;
        this.screen = screen;
        this.lights = lights;
        this.popper = popper;
    }

    public void watchMovie(String movie) {
        popper.on();
        popper.pop();
        lights.dim(10);
        screen.down();
        projector.on();
        projector.wideScreenMode();
        amp.on();
        amp.setStreamingPlayer(player);
        amp.setSurroundSound();
        amp.setVolume();
        player.on();
        player.play(movie);
    }


    public void endMovie() {
        popper.off();
        lights.on();
        screen.up();
        projector.off();
        amp.off();
        player.stop();
        player.off();
    }
}

```


* facade: 겉모양이나 외관

##### 출처: 헤드퍼스트 디자인패턴: 개정판
