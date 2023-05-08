# OSIV와 성능 최적화

OSIV : Open Session In View -> hibernate
Open EntityManager In View -> JPA

spring에서 설정값으로 [ spring.jpa.open-in-view ]: true 기본 값

기본적으로 database transaction을 시작할 때 영속성 컨텍스트가 database connection을 획득한다.    

이제 connection resource를 언제 반환하느냐에 대한 정보이다.        
보통 service 계층에서 transaction을 선언하는데, OSIV가 true이면 transaction을 선언한 service 계층을 벗어나도     
resource를 반환하지 않는다. api인 경우는 유저에게 반환이 될때까지, view template을 사용할 경우 렌더링이 될 때 까지 사용한 후 [view template에서 사용가능?]    
반환하게 된다. [ response 반환할 때 까지 데이터베이스 커넥션과 영속성 컨텍스트가 살아있다. ]    

이 경우 데이터베이스 커넥션 리소스를 너무 오래사용하기 때문에, 실시간 트래픽이 중요한 어플리케이션에서는 커넥션이 모자를 수 있다.[말라 버린다.] -> 장애로 이어진다.    
controller에서 외부 api를 호출할 경우 외부 api 대기 시간만큰 커넥션 리소스를 반환하지 못하고, 유지해야 한다.


### 동작방식 

사용자의 요청[request]가 들어오면 필터 또는 인터셉터에서 영속성 컨텍스트를 생성한다. 이 후 service에서 Transactional을 만나면 이 전에 생성한 영속성 컨텍스트를     
가져와 트랜잭션을 시작한다. service 계층이 끝나면 커밋하고 영속성을 flush 한다. **OSIV가 true로 되어있다면 트랜잭션은 끝나지만 영속성 컨텍스트는 살아있게 된다.**    
영속성은 response를 반환하기 전 까지 [ 영속성 컨텍스트를 생성한 필터 또는 인터셉터를 거치기 전 까지 ] 살아있게 되며, **controller나 view에서 레이지 로딩이 가능해진다.     
이 시점에서는 transaction이 적용되지 않으므로 조회만 가능하며, 수정 및 삭제는 불가능 하다.**   
수정 및 삭제가 되지 않는 이유는 flush를 호출하지 않고 close로 영속성컨텍스트를 닫아버리기 때문에이다. 또한 flush()를 호출하게 되어도 오류를 뱉으며 commit되지 않는다.      
 
 
### command와 query 분리 [CQS] 
보통 비즈니스 로직은 특정 엔티티 몇 개를 등로그 수정하는 것이므로 성능이 크게 문제가 되지 않는다. 그런데 복잡한 화면을 출력하기 위한 쿼리는    
화면에 맞추어 성능을 최적화 하는 것이 중요하다. 하지만 그 복잡성에 비해 핵심 비즈니스에 큰 영향을 주는 것은 아니다.    
그래서 크고 복잡한 어플리케이션을 개발한다면, 이 둘의 관심사를 분히라는것이 좋다.
```
insert[command]는 id정도 반환 update도 고려ㅑ해볼 것 delete는 void
query[select] 는 결과 반환
```


- OrderService
  - OrderSerivce: 핵심비즈니스 로직 
  - OrderQueryService: 화면이나 API에 맞춘 서비스 (주로 읽기 전용 프랜잭션 사용)





##### 출처: https://www.inflearn.com/course/lecture?courseSlug=%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-JPA-API%EA%B0%9C%EB%B0%9C-%EC%84%B1%EB%8A%A5%EC%B5%9C%EC%A0%81%ED%99%94&unitId=24339&tab=curriculum
