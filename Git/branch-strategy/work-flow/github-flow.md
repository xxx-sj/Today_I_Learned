# gifhub-flow


![image](https://user-images.githubusercontent.com/62305110/209473612-4f280523-177f-4369-8eee-e4eae27eb35a.png)

```
git-flow도 좋지만 적용하기에 복잡할 수 있고 해당 어플리케이션에 적용하기에는 적합하지 않을 수 있다.
Scott Chacon에 의해 만들어진 github-flow는 git-flow에 비해 단순해졌다.
github-flow는 자주 배포되어야 하는 환경에 있는 어플리케이션 개발에 적절하다.
```

![image](https://user-images.githubusercontent.com/62305110/209473627-1b32afc9-6539-481f-9d9f-5b2dae5831f7.png)

### create branch
```html
main[master] 에서 새로 branch를 생성한다.
branch를 생성할 때는 git-flow와는 다르게 브랜치를 구분하지 않기 때문에 어떠한 브랜치인지 상세히 정의해야 한다.
브랜치를 생성할 때는 한개의 티켓 단위로 하는것이 좋다.
```

![image](https://user-images.githubusercontent.com/62305110/209473652-cad40428-51b3-4fe8-9497-db612e17868b.png)

### commit & push
```html
브랜치를 따로 구분하지 않기 때문에 commit 메시지를 명확하게 작성한다.
개발중인 내용은 push 를 통해 원격지에도 저장을 하여 같이 개발하는 다른 개발자들도확인 할 수 있도록 한다.
```

![image](https://user-images.githubusercontent.com/62305110/209473672-73debe82-6caa-440a-b558-8a14f2fddc2e.png)

### open pull request
```
개발이 끝나거나, 피드백 또는 도움이 필요할 때  PR( pull request )를 생성한다.
```

![image](https://user-images.githubusercontent.com/62305110/209473689-26947ccf-3ae1-4175-b018-746b3b34eaf9.png)

### re-view & feedback
```html
PR이 생성되면 다른 개발자들이 참여하여 코드 리뷰, 피드백을 진행한다.
re-view와 discuss and feedback 을 통해 해당 내용을 main에 merge할지 판단한다.
merge하기에 적절하지 않다 판단되면 해당 PR을 close를 통해 취소 시킨다.
반대로 적절하다 판단되면 merge를 진행한다.
```

![image](https://user-images.githubusercontent.com/62305110/209473704-9d67ad22-4405-424b-b920-8b29c4a964c4.png)

### test [before merge]
```
리뷰와 토의가 끝났다면 main에 merge 전, 해당 PR을 라이브 서버 (또는 테스트 서버)에 배포해본다.
배포 시 문제가 발생한다면 이전 main 내용으로 다시 배포를 한다.
```

![image](https://user-images.githubusercontent.com/62305110/209473718-f6215db7-418c-4381-ab7d-5eb6f08f4f7d.png)

### merge
```html
윗 단계에서 배포했을 당시 문제가 발생하지 않는다면, main 브랜치에 push 하고배포를 진행한다. 
main 브랜치는 merge, push 되면  자동으로 배포되어야 한다. [CI / CD]
(자동으로 배포할 수 없는 환경일 경우 수동으로 배포한다.)
```

```
github-flow 또한 git-flow와 마찬가지로 현재 개발중인 어플리케이션에 맞는 work-flow를 선택해야한다.
“만약 장기간 프로젝트가 존재하고, 핫픽스 등 유지보수를 위한 작업을 수행해야 되는 팀은 Git-flow가 타당하다. 상시 배포하는 팀은 간단한 GitHub-flow가 적합하다.”
```


##### 출처 및 참고: 
         https://inpa.tistory.com/entry/GIT-%E2%9A%A1%EF%B8%8F-%EA%B9%83%ED%97%99-PRPull-Request-%EB%B3%B4%EB%82%B4%EB%8A%94-%EB%B0%A9%EB%B2%95-folk-issue?category=890012 

         http://blog.hwahae.co.kr/all/tech/tech-tech/9507/ 

         https://nvie.com/posts/a-successful-git-branching-model/ 

         https://techblog.woowahan.com/2553/ 

         https://www.atlassian.com/git/tutorials/what-is-git 

         https://academy.realm.io/kr/posts/360andev-savvas-dalkitsis-using-git-like-a-pro/ 

         https://app.diagrams.net/ 

         https://subicura.com/git/guide/github-flow.html#github-flow-%E1%84%87%E1%85%A1%E1%86%BC%E1%84%89%E1%85%B5%E1%86%A8 

         https://www.youtube.com/watch?v=Uszj_k0DGsg 

         https://www.youtube.com/watch?v=Z9dvM7qgN9s&t=165s 

         https://www.youtube.com/watch?v=LAUCi2eXA9w&list=PLEOnZ6GeucBUvJYvTTnNalSFvYqVObVVH&index=5&t=969s 

         https://www.youtube.com/watch?v=ykZbBD-CmP8 

         https://www.youtube.com/watch?v=_w6TwnLCFwA&t=752s 

         https://www.youtube.com/watch?v=etnFe2tBD5I
