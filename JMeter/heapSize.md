# 힙 크기 늘리는 법

### MacOS 기준

JMeter가 설치된 폴더 /bin 으로 이동하여 아래 명령어 입력
```
export HEAP="-Xms2g[최소] -Xmx2g [최대] -XX:MaxMetaspaceSize=256m"

-XX:MaxMetaspaceSizePermGen 공간에 대한 설정과 JMeter 5.5는 나중에 메타스페이스 영역으로 복제되었습니다. 따라서 이 옵션은 Metaspace 크기에 대한 설정입니다.


echo $HEAP //확인하는 방법

./jmeter 실행 후

ps -ef | grep jmeter // 실행되고 있는 프로세스로 확인하기

```


https://superuser.com/questions/302754/increase-the-maximum-number-of-open-file-descriptors-in-snow-leopard
https://sqa.stackexchange.com/questions/17732/maximum-number-of-threads-in-jmeter
