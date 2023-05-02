# 병합 

- 준영속 상태의 엔티티를 영속상태의 엔티티로 변경하는 것

[ 수정 시 ]
entity가 entityId를 갖고있다는 의미는 DB에 데이터가 저장되어있으며, 파라미터로 넘어온 값들은 entityId에 해당하는 값을 대체할 값들이다.

entity를 update할 때는 크게 방법이 두 가지가 있는데 첫 번째로는 entityId로 row를 조회한 후 영속상태의 엔티티에 set을 통해 데이터를 변경하는 것이다.

```java

@Transactional
public void update(UpdateDto dto) {
  Member member = memberService.findOne(memberId);
  member.setName(dto.getName());
  member.setAge(dto.getAge());
}
```

이렇게 하면 tx commit시 jpa가 Flush를 통해 스냅샷과 다른 데이터들을 업데이트하는 업데이트문을 날리게 된다.

두 번째로는 merge를 사용하는 것이다.
```java
@Transactional
public Dto merger(UpdateDto dto) {
  Dto dto = em.merge(dto);
  
  return dto;
}
```
merge동작을 살펴보자면 먼저 영속성 컨텍스트에서 dto의 entityId로 찾는다. 못 찾으면 db를 조회를 통해 데이터를 조회해온 후 dto의 값들로 교체합니다.
이후 영속성 컨텍스트에 속한 entity를 반환합니다. 

### merge 사용 시 주의 점
merge는 인자로 넘어온 dto의 값들을 모두 update하기 때문에 값이 넘어오지 않은 경우 [ null로 설정된 값 ] 또한 모두 update 될 수 있다.     

**되도록 find를 통해 엔티티를 찾은 후 수정해야 할 값들을 변경하는 것이 좋다.**


##### 출처: https://www.inflearn.com/course/lecture?courseSlug=%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-JPA-%ED%99%9C%EC%9A%A9-1&unitId=24309
