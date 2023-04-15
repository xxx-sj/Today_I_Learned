# 조건식 case 등등

- 기본 CASE 식
```java

select
  case when m.age <= 10 then '학생요금'
       when m.age >= 60 then '경로요금'
       else '일반요금'
   end
 from Member m
```

- 단순 CASE 식
```java

select
  case t.name
      when '팀A' then '인센티브110%'
      when '팁B' then '인센티브120$'
      else '인센티브105%'
    end
fro Team t
```

- COALESCE: 하나씩 조회해서 null이 아니면 반환
- NULLIF: 두 값이 같으면 null 반환, 다르면 첫번째 값 반환

사용자 이름이 없으면 이름 없는 회원을 반환
```
select coalesce(m.username, '이름 없는 회원') from Member m
```

사용자 이름이 '관리자'면 null을 반환하고 나머지는 본인의 이름을 반환
```
select NULLIF(m.username, '관리자') from Member m
```


##### 출처: https://www.inflearn.com/course/lecture?courseSlug=ORM-JPA-Basic&unitId=21725&tab=curriculum
