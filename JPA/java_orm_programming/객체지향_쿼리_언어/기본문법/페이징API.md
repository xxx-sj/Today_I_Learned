# 페이징 api

- setFirstResult(int startPosition): 조회 시작 위치 (0부터 시작) [offset]
- setMaxResults(int maxResults): 조회할 데이터 수 

```java

em.createQuery("select m from member m order by m.age desc", Member.class)
.setFirstResult(0)
.setMaxResults(10)
.getResultList();

```


###### 출처: https://www.inflearn.com/course/lecture?courseSlug=ORM-JPA-Basic&unitId=21721&tab=curriculum
