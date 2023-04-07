# Annotation
* * *

### @Entity

#### property
- name JPA에서 사용할 엔티티 이름을 지정한다. 다른 패키지에 같은 엔티티 클래스가 있다면 이름을 충돌하지 않도록 주의할 것. [ defualt - 클래스 이름을 그대로 사용한다. ]
기본생성자는 필수이다.
final class, enum, interface, inner class 에는 해당 어노테이션을 사용할 수 없다.
저장할 필드에 final을 사용하면 안 된다.
* * *

### @Table
엔티티와 매핑할 테이블을 지정한다. 생략하면 매핑한 엔티티 이름을 테이블 이름으로 사용한다.
property
- name: 매핑할 테이블 이름
- catalog: catalog 기능이 있는 데이터베이스에서 catalog를 매핑한다.
- schema: 스키마 기능이 있는 데이터베이스에서 스키마를 매핑한다.
- uniqueConstraints: DDL 생성 시에 유니크 제약조건을 만든다. 2개 이상의 복합 유니크 제약조건도 만들 수 있다.
                     참고로 이 기능은 스키마 자동 생성 기능을 사용해서 DDL을 만들 때만 사용된다.         
                     
* * *
### @Enumerated([EnumType.STRING])
자바의 enum 을 사용하려면 어노테이션으로 매핑해야한다. 

* * *
### @Lob
CLOB, BLOB 타입을 매핑할 때 사용한다.

* * *



##### 출처: 자바 ORM 표준 JPA 프로그래밍
