# OSIV와 성능 최적화

OSIV : Open Session In View -> hibernate
Open EntityManager In View -> JPA

spring에서 설정값으로 [ spring.jpa.open-in-view ]: true 기본 값

기본적으로 database transaction을 시작할 때 영속성 컨텍스트가 database connection을 획득한다.




##### 출처: https://www.inflearn.com/course/lecture?courseSlug=%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-JPA-API%EA%B0%9C%EB%B0%9C-%EC%84%B1%EB%8A%A5%EC%B5%9C%EC%A0%81%ED%99%94&unitId=24339&tab=curriculum
