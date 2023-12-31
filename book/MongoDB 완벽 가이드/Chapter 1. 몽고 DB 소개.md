### Chapter 1. 몽고DB 소개

#### 1.1 손쉬운 사용

- 몽고DB

  - RDBMS X, 도큐먼트 지향 데이터베이스
    
  - 행 개념 대신에 보다 유연한 모델인 도큐먼트를 사용함 
  
  - 복잡한 계층 관계를 하나의 레코드로 표현함 
  
  - 도큐먼트의 키와 값을 미리 정의하지 않아 고정된 스키마가 없음 
  
  - 스키마가 없기 때문에 개발 속도가 향상되고 모델을 실험하기 쉬움

#### 1.2 확장 가능한 설계 

- 몽고 DB는 분산확장을 염두에 두고 설계됨 

- 도큐먼트 지향 데이터 모델은 데이터를 여러 서버에 더 쉽게 분산하게 해줌

- 도큐먼트를 자동으로 재분배하고 사용자 요청을 올바른 장비에 라우팅

  - 클러스터 내 데이터 양과 부하를 조절



#### 1.3 다양한 기능

- 데이터 CRUD 외에도 다음과 같은 기능을 제공함

##### 인덱싱

- 일반적인 보조 인덱스를 지원함 

- 고유(unique), 복합(compound), 공간 정보, 전문(full-text) 인덱싱 기능 제공

- 중첩된 도큐먼트(nested document) 및 배열과 같은 계층 구조의 보조 인덱스 지원

##### 집계

- 데이터 처리 파이프라인 개념을 기반으로 한 집계 프레임워크 제공

##### 특수한 컬렉션 유형

- 특정 시간에 만료해야 하는 데이터에 대해 유효 시간(TTL) 컬렉션 지원

- 기준 필터와 일치하는 도큐먼트에 부분 인덱스(partial-index) 지원

##### 파일 스토리지 

- 큰 파일과 파일 메타데이터를 편리하게 저장하는 프로토콜 지원

#### 1.4 고성능

- 몽고 DB는 모든 측면에서 고성능을 유지하기 위해 설계됨

- 강력한 성능을 제공하면서도 관계형 시스템의 많은 기능을 포함

  - 그러나 관계형 데이터베이스의 작업을 전부 하지는 않음

  - 일부 기능의 경우 처리와 로직을 클라이언트 측에 넘김

#### 1.5 몽고 DB의 철학 

- 각 장에서 몽고DB의 개발 과정에서 내린 결정의 동기와 이유를 살펴보자! 

- 몽고DB의 주 관심사는 확장성이 높으며 유연하고 빠른, 완전한 기능을 갖춘 데이터 스토리지를 만드는 일
