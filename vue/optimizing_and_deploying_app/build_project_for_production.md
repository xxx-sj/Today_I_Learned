# building the project for production

```
.vue 파일같은 경우 브라우저가 이해할 수 없는 확장자 파일이기 때문에 해당 파일들을 js파일로 변환해줘야 한다.
이러한 작업을 cli로 생성한 프로젝트는 스크립트가 내장되어있어 작업하기 수월하다.
```

```
package.json에 가보면 scripts list가 나오고 이 중 사용할 script는 build이다.
npm run build를하게되면 root에 dist라는 디렉토리가 생성되고 그 안에는 index.html 파일과 .vue파일을 브라우저가 
이해할 수 있도록 만든 js 파일 및 css 파일들이 있다. 이제 이 디렉토리를 정적 호스트 서버에 배포하면 된다.
```
![image](https://user-images.githubusercontent.com/62305110/211184640-565d2869-e06b-4eea-b63e-fc85d786e0c7.png)


##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21880092#content
