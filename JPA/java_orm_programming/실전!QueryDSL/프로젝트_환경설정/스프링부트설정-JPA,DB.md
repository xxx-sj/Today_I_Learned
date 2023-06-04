# 스프링 부트 설정 - JPA, DB

hibernate.format_sql 과 hibernate.show_sql은 system.out.println으로 출력되고, logging.level.org.hibernate.SQL=debug는 log로 찍히게 된다.

```java
spring.datasource.url=jdbc:h2:tcp://localhost/D:\\H2\\db\\querydsl
spring.datasource.username=sa
spring.datasource.password=
spring.datasource.driver-class-name=org.h2.Driver

spring.jpa.hibernate.ddl-auto=create
spring.jpa.properties.hibernate.format_sql=true
#spring.jpa.properties.hibernate.show_sql=true

logging.level.org.hibernate.SQL=debug
logging.level.org.hibernate.type= trace
```
logingg.level.org.hibernate.type= trace 는 console에 binding 된 값을 확인할 수 있다.

##### 출처: https://www.inflearn.com/course/lecture?courseSlug=querydsl-%EC%8B%A4%EC%A0%84&unitId=30120&tab=curriculum&category=questionDetail
