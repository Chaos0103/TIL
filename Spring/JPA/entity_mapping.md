## 객체와 테이블 매핑

### @Entity

- `@Entity`가 붙은 클래스는 JPA가 관리
- JPA를 사용해서 테이블과 매핑할 클래스는 `@Entity` 필수
- 사용시 주의 사항
  - 기본 생성자 필수(파라미터가 없는 public 또는 protected 생성자)
  - final 클래스, enum, interface, inner 클래스 사용 X
  - 저장할 필드에 final 사용 X

#### @Entity 속성 정리

- `name`
  - JPA에서 사용할 Entity 이름을 지정
  - default: 클래스 이름을 그대로 사용
  - 같은 클래스 이름이 없으면 가급적 기본값을 사용

### @Table

- `@Table`은 Entity와 매핑할 테이블 지정

#### @Table 속성 정리

- `name`
  - 매핑할 테이블 이름
  - default: Entity 이름을 사용
- `catalog`: 데이터베이스 catalog 매핑
- `schema`: 데이터베이스 schema 매핑
- `uniqueConstraints`: DDL 생성 시에 유니크 제약 조건 생성

## 데이터베이스 스키마 자동 생성

- DDL은 애플리케이션 실행 시점에 자동 생성
- 테이블 중심 -> 객체 중심
- 데이터베이스 방언을 활용해서 데이터베이스에 맞는 적절한 DDL 생성
- **생성된 DDL은 개발 장비에서만 사용**
- 생성된 DDL은 운영서버에서는 사용하지 않거나, 적절히 다듬은 후 사용

### 속성 정리

- `create`: 기존테이블 삭제 후 다시 생성(DROP + CREATE)
- `create-drop`: create와 같으나 종료시점에 테이블 DROP
- `update`: 변경분만 반영(운영DB에는 사용하면 안됨)
- `validate`: Entity와 테이블이 정상 매핑되었는지만 확인
- `none`: 사용하지 않음

### 주의 사항

- **운영 장비에는 절대 create, create-drop, update 사용하면 안됨**
- 개발 초기 단계: create, update
- 테스트 서버: update, validate
- 스테이징과 운영 서버: validate, none

## 필드와 컬럼 매핑

### Mapping Annotation 정리

- `@Column`: 컬럼 매핑
- `@Temporal`: 날짜 타입 매핑
- `@Enumerated`: enum 타입 매핑
- `@Lob`: BLOB, CLOB 매핑
- `@Transient`: 특정 필드를 컬럼에 매핑하지 않음(매핑 무시)

### @Column

- `name`
  - 필드와 매핑할 테이블 컬럼 이름
  - default: 객체의 필드 이름
- `insertable`, `updatable`
  - 등록, 변경 가능 여부
  - default: TRUE
- `nullable`(DDL): null 값의 허용 여부를 설정, false로 설정하면 DDL 생성 시에 not null 제약조건이 붙음
- `unique`(DDL): `@Table`의 uniqueConstraints와 같지만 한 컬럼에 간단히 유니크 제약조건을 걸 때 사용
- `columnDefinition`: 데이터베이스 컬럼 정보를 직접 줄 수 있음
- `length`(DDL)
  - 문자 길이 제약조건, String 타입에만 사용
  - default: 255
- `precision`, `scale`(DDL): 아주 큰 숫자나 정밀한 소수를 다루어야 할 때만 사용

### @Enumerated

- `value`
  - EnumType.ORDINAL: enum 순서를 데이터베이스에 저장
  - EnumType.STRING: enum 이름을 데이터베이스에 저장
  - default: EnumType.ORDINAL
- ORDINAL 사용 X: 순서를 받아오는 거라 중간 값이 바뀌면 전체에 영향

### @Temporal

- LocalData, LocalDataTime을 사용(최신 하이버네이트 지원)

### @Lob

- `@Lob`에는 지정할 수 있는 속성이 없음
- 매핑하는 필드 타입이 문자면 CLOB 매핑, 나머지는 BLOB 매핑
  - CLOB: String, char[], java.sql.CLOB
  - BLOB: byte[], java.sql.BLOB

### @Transient

- 필드 매핑 X
- 데이터베이스에 저장 X, 조회 X
- 주로 메모리상에서만 임시로 어떤 값을 보관하고 싶을 때 사용

## 기본 키 매핑

### Primary key Mapping Annotation

- `@Id`, `@GeneratedValue`

### 기본 키 매핑 방법

- 직접 할당: `@Id`만 사용
- 자동 생성: `@GeneratedValue`
  - `IDENTITY`: 데이터베이스에 위임, MYSQL
  - `SEQUENCE`: 데이터베이스 시퀀스 오브젝트 사용, ORACLE
    - `@SequenceGenerator` 필요
  - `TABLE`: 키 생성용 테이블 사용, 모든 DB에서 사용
    - `@TableGenerator` 필요
  - `AUTO`: 방언에 따라 자동 지정, 기본값

### IDENTITY 특징

- 기본 키 생성을 데이터베이스에 위임
- 주로 MySQL, PostgreSQL, SQL Server, DB2에서 사용
  - MySQL의 `AUTO_INCREMENT`
- JPA는 보통 `Transaction commit`시점에 `INSERT SQL` 실행
- `AUTO_INCREMENT`는 데이터베이스에 `INSERT SQL`을 실행 한 이후에 ID 값을 알 수 있음
- `IDENTITY`는 `em.persist()` 시점에 즉시 `INSERT SQL` 실행하고 DB에서 식별자를 조회

```java
@Entity
public class Member {

    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
}
```

### SEQUENCE 특징

- 데이터베이스 시퀀스는 유일한 값을 순서대로 생성하는 특별한 데이터베이스 오브젝트
  - 오라클 시퀀스
- ORACLE, PostgreSQL, DB2, H2 데이터베이스에서 사용

```java
@Entity
@SequenceGenerator (name = "MEMBER_SEQ_GENERATOR", sequenceName = "MEMBER_SEQ", initialValue = 1, allocationSize = 1)
public class Member {
    
    @Id @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "MEMBER_SEQ_GENERATOR")
    private Long id;
}
```

#### @SequenceGenerator 속성

- `name`
  - 식별자 생성기 이름
  - default: 필수
- `sequenceName`
  - 데이터베이스에 등록되어 있는 시퀀스 이름
  - default: hibernate_sequence
- `initialValue`
  - DDL 생성 시에만 사용됨, 시퀀스 DDL을 생성할 때 처음 1 시작하는 수를 지정
  - default: 1
- `allocationSize`
  - 시퀀스 한 번 호출에 증가하는 수(성능 최적화에 사용)
    - 한 번 호출할 때 마다 DB와 통신
    - 50이면 한 번 호출시 50개를 끌고와서 50개를 다 사용하기 전까지 통신 X
  - 데이터베이스 시퀀스 값이 하나씩 증가하도록 설정되어 있으면 이 값을 반드이 1로 설정해야 함
  - default: 50
- `catalog`, `schema`: 데이터베이스의 catalog, schema 이름

### TABLE

- 키 생성 전용 테이블을 하나 만들어서 데이터베이스 시퀀스를 흉내내는 전략
- 장점: 모든 데이터베이스에 적용 가능
- 단점: 성능
- 실무 사용 X

### 권장하는 식별자

- 기본 키 제약 조건: null 아님, 유일, 변하면 안됨
- 미래까지 이 조건을 만족하는 자연키를 찾기 어려움. 대리키(대체키)를 사용
- 주민등록번호도 기본 키로 적절하지 않음
- 권장: Long형 + 대체키 + 키 생성전략 사용
