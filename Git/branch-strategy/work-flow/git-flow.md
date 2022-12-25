# git-flow

2010년에 개발자[nvie]에 의해 소개된 work flow이다. 해당 work-flow는 `feature`-`develop`-`release`-`hotfix`-`main[master]`로 브랜치를 구성한다.

![image](https://user-images.githubusercontent.com/62305110/209458539-8e6bbfa9-09c9-41e0-8758-e2b147e94a30.png)

* * *

![image](https://user-images.githubusercontent.com/62305110/209458543-c24ec8f1-5eae-4079-839b-5da7ea26682a.png)

### feature
```
추가기능을 개발할 때 사용하는 브랜치. 
develop브랜치에서 뻗어나와 작업이 끝나면 develop브랜치에 merge 된다.
브랜치명은 feature[/,-][구현기능명] 로 작성하는 것이 일반적이다.
```

![image](https://user-images.githubusercontent.com/62305110/209458556-0c66d3e9-976c-40fa-9b56-022bdf263b58.png)

### develop
```
main브랜치에서 뻗어나온 브랜치. 다음 버전 출시 개발을 위한 브랜치. 
버전 출시 준비가 완료되면 release브랜치로 뻗어나간다.
```

![image](https://user-images.githubusercontent.com/62305110/209458571-c6c694ad-84d1-4bfe-9b93-678b2b4dee13.png)

### release
```
다음 버전 출시를 준비할 때 사용하는 브랜치. 
develop브랜치에서 뻗어나온 브랜치로, release브랜치에서 QA, 테스트를 진행 후 ,
main[master]브랜치와 develop브랜치로 합쳐진다.
브랜치명은 release[/,-](version) 로 작성하는 것이 일반적이다.
```

![image](https://user-images.githubusercontent.com/62305110/209458586-2ed12fb9-c05b-4450-8deb-7828cfb70e03.png)

### hotfix
``` 
현재 운영중인 main[master]브랜치에서 발생한 버그를 긴급으로 수정하는 브랜치.  
main[master]브랜치에서 뻗어나와 main[master]와 develop브랜치에 합쳐진다.
[tag numbering은 이해를 돕기위해  작성되었습니다]
브랜치명은 hotfix[/,-](version) 로 작성하는 것이 일반적이다.
```

![image](https://user-images.githubusercontent.com/62305110/209458593-aa49e619-ed81-49e0-afab-544cc30a5a4a.png)

### main[master]
```
현재 출시되어있는 production 코드가 있는 브랜치. 
제품으로 출시되는 브랜치이다.
```

항시 유지되는 메인 브랜치에는 `main[master]` 와 `develop`가 있다.
메인 브랜치에 merge되면 사라지는 보조 브랜치는 `feature`, `release`, `hofix` 이다.

```warn
Note of reflection (March 5, 2020)

This model was conceived in 2010, now more than 10 years ago, and not very long after Git itself came into being. In those 10 years, git-flow (the branching model laid out in this article) has become hugely popular in many a software team to the point where people have started treating it like a standard of sorts — but unfortunately also as a dogma or panacea.

During those 10 years, Git itself has taken the world by a storm, and the most popular type of software that is being developed with Git is shifting more towards web apps — at least in my filter bubble. Web apps are typically continuously delivered, not rolled back, and you don't have to support multiple versions of the software running in the wild.

This is not the class of software that I had in mind when I wrote the blog post 10 years ago. If your team is doing continuous delivery of software, I would suggest to adopt a much simpler workflow (like GitHub flow) instead of trying to shoehorn git-flow into your team.

If, however, you are building software that is explicitly versioned, or if you need to support multiple versions of your software in the wild, then git-flow may still be as good of a fit to your team as it has been to people in the last 10 years. In that case, please read on.

To conclude, always remember that panaceas don't exist. Consider your own context. Don't be hating. Decide for yourself.
```
출처:[https://nvie.com/posts/a-successful-git-branching-model/]

```html
git-flow는 2010년 소개된 이후로 많은 곳에서  이용이 되면서 work-flow의 표본이 되어오면서, 마치 “work-flow의 기본은 git-flow 다” 라는 생각을 많이 가져왔지만 그렇지 않다. 
처음 git-flow를 소개한 개발자도 “ 팀에서 소프트웨어를 지속적으로 제공하는 경우 팀에 git-flow 보다 간단한 github-flow를 채택하는 것이 좋다 . 그러나  버전이 지정된 소프트웨어를 빌드하거나,
여러 버전의 소프트웨어를 지원해야 하는 경우 git-flow가 적합 할 수 있다.” 라고 말하고 있다. 즉, 버전별로 제공해야하는 앱,
오픈 소스 라이브러리 또는 프레임워크 등과 같은 경우 git-flow 가 적합 할 수 있다. 반대로, 최신 릴리즈 버전만을 제공하는 웹 어플리케이션에는 git-flow가 적합하지 않을 수 있다.
```


##### 출처 및 참고: 

http://blog.hwahae.co.kr/all/tech/tech-tech/9507/ 
https://nvie.com/posts/a-successful-git-branching-model/ 
https://techblog.woowahan.com/2553/ 
https://www.atlassian.com/git/tutorials/what-is-git 
https://academy.realm.io/kr/posts/360andev-savvas-dalkitsis-using-git-like-a-pro/ 
https://www.youtube.com/watch?v=Uszj_k0DGsg 
https://www.youtube.com/watch?v=Z9dvM7qgN9s&t=165s 
https://www.youtube.com/watch?v=LAUCi2eXA9w&list=PLEOnZ6GeucBUvJYvTTnNalSFvYqVObVVH&index=5&t=969s 
https://www.youtube.com/watch?v=ykZbBD-CmP8 
https://www.youtube.com/watch?v=_w6TwnLCFwA&t=752s 
https://app.diagrams.net/
