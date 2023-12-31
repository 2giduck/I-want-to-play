### Chapter 8. 트랜잭션
- 트랜잭션이 무엇인지
- 트랜잭션을 사용하는 방법
- 애플리케이션을 위한 트랜잭션 제한 조정

#### 8.1 트랜잭션 소개
- 트랜잭션: 읽기나 쓰기 작업이 가능한 데이터베이스 작업을 하나 이상 포함하는 데이터베이스의 논리적 처리 단위

###### 8.1.1 ACID의 정의
- ACID: 원자성, 일관성, 고립성, 영속성
  - 원자성: 트랜잭션 내 모든 작업이 적용되거나 아무 작업도 적용되지 않도록 함
  - 일관성: 트랜잭션이 성공하면 데이터베이스가 하나의 일관성 있는 상태에서 다음 일관성 있는 상태로 이동함
  - 고립성: 여러 트랜잭션이 데이터베이스에서 동시에 실행되도록 허용하는 속성
  - 영속성: 트랜잭션이 커밋될 때 시스템 오류가 발생하더라도 모든 데이터가 유지되도록 함

#### 8.2 트랜잭션 사용법
- 코어 API: 관계형 데이터베이스와 유사한 구문
  - 대부분의 오류에 재시도 로직을 제공하지 않음
  - 개발자가 작업에 대한 로직, 재시도 및 오류 로직을 모두 작성해야 함

- 콜백 API: 트랜잭션 사용에 권장되는 접근 방식
  - 많은 기능을 래핑하는 단일 함수 제공 


#### 8.3 애플리케이션을 위한 트랜잭션 제한 조정
###### 8.3.1 타이밍과 Oplog 크기 제한
- 트랜잭션의 최대 실행 시간: 1분 이하 
  - 제한을 수정해 증가할 수 있음

