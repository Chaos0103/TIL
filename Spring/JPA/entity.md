## 주의 사항

- `EntityManagerFactory`: 하나만 생성해서 애플리케이션 전체에서 공유
- `EntityManager`: 고객의 요청이 올때마다 생성(사용하고 버려야 함), 쓰레드간에 공유 X
- JPA의 모든 데이터 변경은 `Transaction` 안에서 실행
- `EntityManager`는 데이터 변경시 `Transaction`을 시작해야 함

## JPQL

- JPA를 사용하면 Entity 객체를 중심으로 개발
- JPA는 SQL을 추상화한 JPQL이라는 객체 지향 쿼리 언어 제공
- SQL과 문법 유사: SELECT, FROM, WHERE, GROUP BY, HAVING, JOIN 지원
- JPQL은 **Entity 객체**를 대상으로 쿼리
- SQL은 **데이버베이스 테이블**을 대상으로 쿼리
- 테이블이 아닌 **객체를 대상으로 검색하는 객체 지향 쿼리**

## 영속성 컨텍스트

### Entity의 생명주기

- 비영속(new/trasient): 영속성 컨텍스트와 전혀 관계가 없는 **새로운** 상태
- 영속(manage): 영속성 컨텍스트에 **관리**되는 상태
- 준영속(detached): 영속성 컨텍스트에 저장되었다가 **분리**된 상태
- 삭제(removed): **삭제**된 상태