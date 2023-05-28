# 스프링 데이터 JPA 구현체 분석

JpaRepository구현체로 SimpleRepository가 있는데, 이것이 spring-data-jpa 구현체이다.      
simpleRepository에 가보면 @Transactional이 있기 때문에 service에서 따로 transactional를 사용하지 않아도 됐던 것      
service에서 있었다면 서비스에서 전파받아 사용하는 것이고 아니라면 repo에서 시작.       


@Transactional(readOnly = true)
- 옵션을 사용하면 플러시를 생락해서 약간의 성능 향상을 얻을 수 있다.     


### 중요
save() method
- 새로운 엔티티면 저장 (persist)
- 새로운 엔티티가 아니면 병합(merge)

가급적 merge를 사용하면 안된다.     
merge는 영속성상태를 벗어났을 때 사용할 것     


##### 출처: https://www.inflearn.com/course/lecture?courseSlug=%EC%8A%A4%ED%94%84%EB%A7%81-%EB%8D%B0%EC%9D%B4%ED%84%B0-JPA-%EC%8B%A4%EC%A0%84&unitId=28027&category=questionDetail&tab=curriculum
