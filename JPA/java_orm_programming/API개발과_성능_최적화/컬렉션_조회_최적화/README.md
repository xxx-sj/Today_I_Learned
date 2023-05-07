# 권장순서

1. 엔티티 조회 방식으로 우선 접근
  - 페치조인으로 쿼리 수를 최적화 XToOne
  - 컬렉션 최적화
    - 페이징 필요 hibernate.default_batch_fetch_size, @BatchSize로 최적화
    - 페이징이 필요가 없다면 fetch 조인으로 
2. 엔티티 조회 방식으로 해결이 안되면 DTO 조회 방식 사용
3. NativeSQL or 스프링 JDBCTemplate 사용


