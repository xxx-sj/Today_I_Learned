# git 최초 설정

- /etc/gitconfig : 시스템의 모든 사용자와 모든 저장소에 적용되는 설정이다. [ git config --system ] 으로 이 파일을 읽고 쓸 수 있다.
-  ~/.gitconfig : 특정 사용자에게만 적용되는 설정이다. [ git config --global ] 옵션으로 이 파일을 읽고 쓸 수 있다.
-  .git/config: 이 파일은 git directory 에 있고 특정 저장소(혹은 작업 중인 프로젝트)에만 적용된다. 각 설정은 역순으로 우선 시 된다.    
    .git/config 가 /etc/gitconfig 보다 우선 시 된다.

윈도우 Git은 $HOME directory(C:\Documents and Settings\$USER) 에 있는 .gitconfig 파일을 찾는다. msysGit에도 /etc/gitconfig 파일이 있다     
경로는 MSys 루트에 다른 상대 경로이다. 인스톨러로 msysGit을 설치할 때 설치 경로를 선택할 수 있다.


### 사용자 정보
Git을 설치하고 나서 가장 먼저 해야 하는 것은 사용자 이름과 이메일 주소를 설정하는 것이다. Git은 커밋할 때마다 이 정보를 사용하며, 한 번 커밋한 후에는 정보 변경이 불가하다.

```git
$ git config --global user.name "xxx-sj"
$ git config --global user.email xxx-sj@github.com
```
  --global로 설정하는 값은 설치 후 한번만 설정해주면 된다. 만약, 프로젝트마다 사용한다면 --global 옵션을 빼고 명령을 실행한다.    
  
  
### 편집기
Git에서 사용할 텍스트 편집기를 고른다. 기본적으로 시스템의 기본 편집기를 사용하고, 보통 편집기는 vi, vim이다.    
```git
$ git config --global core.editor sublineText
```

### Diff 도구
Merge 충돌을 해결하기 위해 사용하는 도구를 설정할 수 있다. 
```git
$ git config --global merge.tool vimdiff
```

### 설정확인
```git
$ git config --list
```

Git은 같은 키를 여러 파일(/etc/gitconfig와 ~/.gitconfig같은)에서 읽기 때문에 같은 키가 여러개 있을 수 있다. 이런 경우 git은 나중 값을 사용한다.     
```git
$ git config user.name
```

### 도움말
config명령에 대한 도움말을 볼 수 있다.
```git
$ git help config
```
  
##### 출처: Pro git
