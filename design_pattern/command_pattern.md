# command_pattern [커맨드 패턴] 

```
요청 내역을 객체로 캡슐화해서 객체를 서로 다른 요청 내역에 따라 매개변수화 할 수 있다.
이러면 요청을 큐에 저장하거나 로그로 기록하거나 작업 취소 기능을 사용할 수 있다.
```

```
client = main에 해당한다.
  invoker와 command receiver를 생성하여 설정하는 곳
Invoker = controller
Command = command 
Receiver = light 

invoker는 command interface를 필드로 갖고, client는 receiver를 필드로 갖는다.
client는 command를 구현하였고, execute 메소드를 통해 receiver의 메소드를 호출한다.
receiver에서는 execute에서 호출한 메소드에서 receiver가 처리할 로직을 처리한다.
```

```java

//command interface
interface Command {
    void execute();
}

class LightOnCommand implements Command {
    Light light;

    public LightOnCommand(Light light) {
        this.light = light;
    }

    @Override
    public void execute() {
        light.on();
    }
}


//remote controller
class SimpleRemoteControl {
    Command slot;

    public SimpleRemoteControl() {}

    public void setCommand(Command command) {
        this.slot = command;
    }

    public void buttonWasPressed() {
        slot.execute();
    }
}

class Main {
    public static void main(String[] args) {
        SimpleRemoteControl remote = new SimpleRemoteControl();
        Light light = new Light();
        LightOnCommand lightOn = new LightOnCommand(light);

        remote.setCommand(lightOn);
        remote.buttonWasPressed();
    }
}

```




```
RemoteControl = 컨트롤러 invoker
  생성자를 통해 모든 on,off command를 초기화하며, set메소드를 통해 slot과 on,off command를 받아 설정한다.
  on,off버튼 클릭 시 slot 을 매개변수로 받아 해당 slot에 해당하는 command를 실행한다.
  [-]offCommand 해당 receiver의 off 행동에 해당하는 메소드를 실행한다.
  [-]onCommand 해당 receiver의 on 행동에 해당하는 메소드를 실행한다.
  [-]Loader = client 객체들을 생성하여 설정하는 곳
```

```java

class RemoteControl {
    Command[] onCommands;
    Command[] offCommands;

    public RemoteControl() {
        onCommands = new Command[7];
        offCommands = new Command[7];

        Command noCommand = new NoCommand();
        for(int i = 0; i < 7; i++) {
            onCommands[i] = noCommand;
            offCommands[i] = noCommand;
        }
    }

    public void setCommand(int slot, Command onCommand, Command offCommand) {
        onCommands[slot] = onCommand;
        offCommands[slot] = offCommand;
    }

    public void onButtonWasPushed(int slot) {
        onCommands[slot].execute();
    }

    public void offButtonPushed(int slot) {
        offCommands[slot].execute();
    }
}


class LightOffCommand implements Command {
    Light light;

    public LightOffCommand(Light light) {
        this.light = light;
    }

    public void execute() {
        light.off();
    }
}


class StereoOnWithCDCommand implements Command {
    Stereo stereo;

    public StereiIbWutgCDCommand(Stereo stereo) {
        this.stereo = stereo;
    }

    public void execute() {
        stereo.on();
        stereo.setCD();
        stereo.setVolume();
    }
}

class RemoteLoader {

    public static void main(String[] args) {
        RemoteControl remoteControl = new RemoteControl();

        Light livingRoomLight = new Light("Living Room");
        Light kitchenLight = new Light("Kitchen");
        CeilingFan ceilingFan = new CeilingFan("Living Room");
        GarageDoor garageDoor =new GarageDoor("Garage");
        Stereo stereo = new Stereo("Living Room");

        LightOnCommand livingRoomLightOn = new LightOnCommand(livingRoomLight);
        LightOffCommand livingRoomLightOff = new LightOffCommand(livingRoomLight);
        //..

        CeilingFanOnCommand ceilingFanOnCommand = new CeilingFanOnCommand(ceilingFan);
        CeilingFanOffCommand ceilingFanOffCommand = new CeilingFanOffCommand(ceilingFan);
        //..

        remoteControl.setCommand(0, livingRoomLightOn, livingRoomLightOff);
        remoteControl.setCommand(1, ceilingFanOnCommand, ceilingFanOffCommand);

        remoteControl.onButtonWasPushed(0);
        remoteControl.offButtonPushed(0);
    }
}
```


##### Null Object
```
배열 초기화 시 모든 배열에 null object로 채우는데 이유는
잘못된 slot의 버튼을 클릭 시 execute 메소드가 실행되는데 그때 발생되는
nullPointer Exception(오류)을 방지하기 위해, 또는 클라이언트 입장에서 null을 처리하지 않기 위해
```

```java

//Null Object
class NoCommand implements Command {

    @Override
    public void execute() {}
}

```
##### 작업취소 추가

```java
interface Command {
    void execute();
    //execute와 반대의 행동
    void undo();
}

class Light {
  public void on() {
  ...
  }
  
  public void off() {
  ...
  }

}

class LightOnCommand implements Command {
    Light light;
    public LightOnCommand(Light light) {
        this.light = light;
    }

    public void execute() {
        light.on();
    }

    public void undo() {
        light.off();
    }
}

class LightOffCOmmand implements Command {
    Light light;
    public LightOnCommand(Light light) {
        this.light = light;
    }

    public void execute() {
        light.off();
    }

    public void undo() {
        light.on();
    }
}

class RemoteControlWithUndo {
    Command[] onCommands;
    Command[] offCommands;
    Command undoCommand;

    public RemoteControlWithUndo() {
        onCommands = new Command[7];
        offCommands = new Command[7];

        Command noCommand = new NoCommand();
        for(int i = 0; i < 7; i++) {
            onCommands[i] = noCommand;
            offCommands[i] = noCommand;
        }
        undoCommand = noCommand;
    }

    public void setCommand(int slot, Command onCommand, Command offCommand) {
        onCommands[slot] =  onCommand;
        offCommands[slot] = offCommand;
    }

    public void onButtonWasPushed(int slot) {
        onCommands[slot].execute();
        undoCommand = onCommands[slot];
    }

    public void offButtonWasPushed(int slot) {
        offCommands[slot].execute();
        undoCommand = offCommands[slot];
    }

    public void undoButtonWasPushed() {
        undoCommand.undo();
    }
}


class RemoteLoader {
    public static void main(String[] args) {
        RemoteControlWithUndo remoteControlWithUndo = new RemoteControlWithUndo();
        
        Light light = new Light("living room");
        
        LightOnCommand lightOnCommand = new LightOnCommand(light);
        LightOffCommand lightOffCommand = new LightOffCommand(light);
        
        remoteControlWithUndo.setCommand(0, lightOnCommand, lightOffCommand);
        
        remoteControlWithUndo.onButtonWasPushed(0);
        remoteControlWithUndo.offButtonWasPushed(0);
        remoteControlWithUndo.undoButtonWasPushed();
        
        
    }
}
```
###### 여러가지 리시버를 처리하는 커맨드

```java

//여러가지 리시버를 처리하는 커맨드
class MacroCommand implements  Command {
    Command[] commands;

    public MacroCommand(Command[] commands) {
        this.commands = commands;
    }

    @Override
    public void execute() {
        for (Command command : commands) {
            command.execute();
        }
    }
}


//pseudo code + 
/**
 * main
 *  receiver 객체들 만들기
 *  해당 receiver command에 receiver객체 넣기
 *  commad 객체들을 배열로 만들기 []
 *  커맨드 배열 객체를 macroCommand에 넣기
 *  매크로 커맨드 컨트롤러에 넣기 
 *  가능한 이유는 remoteControl 매개변수로 command 객체르 받는데,
 *  매크로 커맨드도 command 객체이기 때문에
 *  다른 커맨드 객체들은 리시버를 매개변수로 받지만 매크로 커맨드는
 *  커맨드배열객체를 매개변수로 받는다.
 */ 
```

##### 여러번의 작업취소는 undoCommand 객체를  


##### 커맨드 패턴 활용하기

```
커맨드 패턴을 스케줄러나, 스레드 풀, 작업 큐와 같은 다양한 작업에서 사용한다.
작업 큐를 예를들면, 큐 한 쪽 긑은 커맨드를 추가할 수 있도록 되어 있고, 다른 쪽 끝에는
커맨드를 처리하는 스레드들이 대기하고있다. 각 스레드는 우선 execute() 메소드를 호출하고
완료되면 커맨드 객체를 버리고 다른 커맨드 객체를 가져온다.
작업 큐 클래스는 계산작업(일련의 행동)을 하는 객체들과 완전히 분리되어 있다.
큐에 커맨드 패턴을 구현하는 객체를 넣으면 그 객체를 처리하는 스레드가 생기고 자동으로
execute 메소드가 호출된다.
```


```
어떤 어플리케이션은 모든 행동을 기록해 두었다가 어플리케이션이 다운되었을 대 그 행동을
다시 호출해서 복구할 수 있어야 한다. 커맨드 패턴을 사용하면 store()와 load() 메소드를 추가해서
이런 기능을 구현할 수 있다.
로그 기록은 어떤 명령을 실행하면서 디스크에 실행 히스토리를 기록하고(store) 어플리케이션이 다운되면
커맨드 객체를 다시 로딩해서 (load) execute메소드를 순서대로 실행한다.
데이터가 변경될 때마다 매번 저장할 수 없는 자료구조를 다루는 어플리케이션에서 로그를 사용해서 마지막 체크
포인트 이후로 진행한 모든 작업을 저장한 다음 시스템이 다운되었을 때 최근 수행된 작업을 다시 수행한다.
```



##### 출처: 헤드퍼스트 디자인패턴: 개정판
