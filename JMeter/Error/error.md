# ERROR 


### "unable to create new native thread"

- JMeter JVM의 메모리 제한 늘리기
bin 폴더 아래의 jmeter.sh 파일 열기 [메모장, sublineText ...]

JVM_ARGS="-Xms1g -XmX2g" 추가하기. 

또는 jmeter를 실행할 때
```
exprot JVM_ARGS="-Xms1g -Xmx2g" && sh [jmeber 설취 위치]/bin/jmeter.sh

ex)
export JVM_ARGS="-Xms1g -Xmx2g" && sh /Applications/apache-jmeter-5.4.1/bin/jmeter.sh
```

