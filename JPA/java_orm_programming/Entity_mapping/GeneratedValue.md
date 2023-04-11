# GeneratedValue

### IDENTITY

persist하는 시점에 insert query를 날려서 저장하게 된다. [commit 시점이 아닌.]

### SEQUENCE
먼저 시퀀스 값을 DB에서 조회하여 얻은 후, 얻은 entityid 값으로 영속성 컨텍스트에 저장한다.     
commit 시점에 flush를 통해 insert query가 날라가게 된다.


### auto

데이터베이스에 따라 자동으로 설정해준다.
strategy의 기본값이기도 하다.
```
@GeneratedValue(strategy = GenerationType.AUTO)
private Long id;
```


