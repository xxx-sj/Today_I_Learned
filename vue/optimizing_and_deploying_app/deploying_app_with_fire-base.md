# deploying vue app in fire-base

```
fire-base를 이용하여 배포를 진행한다. 간단한 app이나 request가 별로 없고 client에만 집중할 수 있는 웹/앱 같은 경우
fire-base가 적합하다.
```

![image](https://user-images.githubusercontent.com/62305110/211186419-3c7dbedc-d60a-4e89-88f7-be4154171564.png)

```
빌드 아래 보면 hosting이라는 tab을 볼 수 있는데 hosting tab에 들어가면, 시작하기 버튼이 있따. 해당 버튼을 클릭하게 되면 
순서대로 app을 배포하는 방법이 설명되어 있다.
```
![image](https://user-images.githubusercontent.com/62305110/211186595-8fb8793e-633a-48d5-a8d4-57a0ed360c45.png)

1. 먼저 global에 firebase-tools를 설치한다.
2. 설치가 완료되면 firebase-login으로 fire-base에 login을 시도한다. 
3. 다음으로는 firebase init을 통해 firebase에 올릴 프로젝트를 생성하게 된다.
4. 현재는 기본적으로 hosing만 할 것이기 때문에 terminal에 보이는 리스트 중 hosting을 선택한다.
5. 다음으로는 현재 존재하는 project를 올리도록 선택한다
6. 선택 이후에는 firebase에 있는 project리스트가 보여지게 되고, 그 중 배포하고 싶은 파일을 선택하면 된다.
7. wat do you want to use your bublic directory에 대한 물음에는 우리가 실제로 uploaded할 폴더를 선택한다 [dist]
8. 다음으로는 configure as a single-page app(rewrite all urls to /index.html )에 대한 물음이 나오는데,
   이 질문은 현재 app을 single-page app으로 설정할 것인가에 대한 질문이다.
   
8-1. 주의해야 하는 점이 있는데, spa는 routing을 통해 page를 전환하게 된다. 만약, 일반적인 웹페이지에서 [host.com 이라 가정한다]
host.com/product 라는 페이지를 요청하게 되면 해당 path가 static server에 전달되고, path를 무시하지 않게 된다면 app application이 배포되어있는
서버에서 request와 일치하는 것을 찾으려 할 것이다. product 폴더라던가 html file 들이 해당된다. 하지만 vue app은 찾는데 실패하게 될 것이다.
이유는 배포하는 앱에는 /css, /js, /favicon.ico, index.html 밖에 없기 때문이다. 따라서 server로 전달되는 path는 무싷고 매번 요청에 index.html만을
반환하게 하는 것이다. 그렇게하면 js 파일이 있는 vue app에서 path를 확인하여 client side에서 처리하게 만든다.
     
9. 마지막으로 dist/indext.html already exists. overwrite라는 질문에는 n을 선택한다.
   이유는 keep our file 하고 싶기 때문이다.
10. [firebase deploy] 를 통해 firebase server에 app을 배포한다.




##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880094#content
