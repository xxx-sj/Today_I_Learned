# 최대 사용자 프로세스 수 변경

1. sysctl kern.maxproc 시스템 전체의 최대 프로세스를 확인한다.
2. ulimit -u [number] 로 최대 프로세스 이하로 설정한다.


### 부가적인 명령어
```
//시스템 전체의 최대 프로세스 수 늘리기
sudo sysctl -w kern.maxproc=<new_maxproc_value>

//시스템 전체의 최대 사용자 프로세스 수 늘리기
sudo sysctl -w kern.maxprocperuid=<new_value>
sudo sysctl -w kern.maxpocperuid=[number]

명령어 라인이 지속되기 위해서는
 /etc/sysctl.conf 
 파일에 
 kern.maxproc=<new-val>
kern.maxprocperuid=<new-val>
```

```linux
sudo launchctl limit maxuserprocesses [number] [number] 
sudo launchctl limit maxproc <new_maxproc> <new_maxproc_soft>
```


```
ulimit -u [최대 크기 만큼]
```

```linux
ulimit -a // user limit

-t: cpu time (seconds)              unlimited
-f: file size (blocks)              unlimited
-d: data seg size (kbytes)          unlimited
-s: stack size (kbytes)             8192
-c: core file size (blocks)         0
-v: address space (kbytes)          unlimited
-l: locked-in-memory size (kbytes)  unlimited
-u: processes                       2784
-n: file descriptors                256
 
 ----------------------------------------------
 
 ulimit -aH // user limit Hard
 
 -t: cpu time (seconds)              unlimited
-f: file size (blocks)              unlimited
-d: data seg size (kbytes)          unlimited
-s: stack size (kbytes)             65532
-c: core file size (blocks)         unlimited
-v: address space (kbytes)          unlimited
-l: locked-in-memory size (kbytes)  unlimited
-u: processes                       4176
-n: file descriptors                unlimited
```

