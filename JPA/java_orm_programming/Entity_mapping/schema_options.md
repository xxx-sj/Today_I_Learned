# 스키마 자동생성

- hibernate.hbm2ddl.auto

- create: 기존 테이블을 삭제하고 새로 생성한다. DROP + CREATE
- create-drop: create 속성에 추가로 애플리케이션을 종료할 때 생성한 DDL을 제거한다. DROP + CREATE + DROP
- update: 데이터베이스 테이블과 엔티티 매핑정보를 비교해서 변경 사항만 수정한다.
- validate: 데이터베이스 테이블과 엔티티 매핑정보를 비교해서 차이가 있으면 경고를 남기고 RUN 하지 않는다. DDL을 수정하지 않는다.
- none: 자동 생성 기능을 사용하지 않으려면  hibernate.hbm2ddl.auto option을 없애거나 , none을 주면 된다.


* 운영 서버에서 create, create-drop, update 처럼 DLL을 절대 사용하면 언된다. 테이블이나 컬럼을 삭제할 수 있기 때문이다.

- 개발 초기 단계는 create or update
- 초기화 상태로 자동화된 테스트를 진행하는 개발자 환경과 CI 서버는 create or create-drop
- 테스트 서버는 update or validate
- 스테이징과 운영 서버는 validate or none

JPA 2.1 지원옵션 : none, create, drop-and-create, drop



##### 출처: 자바 ORM 표준 jpa 프로그래밍
