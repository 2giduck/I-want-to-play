### Chapter 15. 샤딩 구성
- 구성 서버, 샤드, mongods 프로세스 설정방법
- 클러스터 용량 추가 방법
- 데이터 저장 및 분산 방법

#### 15.1 언제 샤딩해야 하나
- 너무 일찍 샤딩 -> 배포 운영이 복잡해짐 
- 늦게 샤딩 -> 과부화된 시스템을 중단 없이 샤딩하기 어려움

- 샤딩 사용하는 경우
  - 사용 가능한 메모리를 늘릴 때
  - 사용 가능한 디스크 공간을 늘릴 때
  - 서버 부하를 줄일 때
  - 한 개의 mongod가 다룰 수 있는 처리량보다 더 많이 데이터를 읽거나 쓸 때
  
#### 15.2 서버 시작
- monogos와 샤드를 설정함

###### 15.2.1 구성 서버
- 구성 서버는 클러스터의 두뇌부 
- 어떤 서버가 무슨 데이터를 갖고 있는지에 대한 모든 메타 데이터 보유함 

###### 15.2.2 mongos 프로세스
- 세 개의 구성 서버가 실행 중이면 애플리케이션이 접속할 mongos 프로세스 시작
###### 15.2.3 복제 셋으로부터 샤딩 추가
- 애플리케이션에 이미 복제 셋이 있다면 해당 셋이 첫 번째 샤드가 됨 
###### 15.2.4 용량 추가
- 용량을 추가하려면 샤드를 추가해야 함 
- 복제 셋을 생성해 빈 샤드를 새로 추가하고, 기존 샤드들과 다른 이름을 갖게 함 
###### 15.2.5 데이터 샤딩
- 몽고 DB는 데이터를 어떻게 분산할지 알려주기 전에는 자동으로 데이터를 분산하지 않음

#### 15.3 몽고DB는 어떻게 클러스터 데이터를 추적하는가
- 각 mongos는 샤드 키가 주어지면, 도큐먼트를 어디서 찾을지 항상 알아야 함 
- 이론상 몽고DB는 각각의 도큐먼트가 어디 있는지 추적 가능하지만, 도큐먼트가 많은 컬렉션은 다루기 힘듬
- 범위가 겹치는 청크는 가질 수 없음 
- 도큐먼트는 항상 단 하나의 청크에만 속함
###### 15.3.1 청크 범위
- 각 청크는 포함되는 범위에 의해 설명됨
###### 15.3.2 청크 분할
- 각 샤드 프라이머리 mongod는 청크에 얼마나 많은 데이터가 삽입되었는지 추적하고, 청크가 나뉘어야 할지 확인해야 함 
- 분할이 일어나려면 모든 구성 서버가 살아 있어야 하고 접근 가능해야 함 

#### 15.4 밸런서
- 밸런서는 데이터 이동을 책임짐
- 주기적으로 샤드 간의 불균형을 체크하다가, 불균형이면 청크를 이동하기 시작함

#### 15.5 콜레이션
- 몽고 DB의 콜레이션을 이용해 문자열 비교를 위한 언어별 규칙을 지정할 수 있음 

#### 15.6 스트림 변경
- 스트림 변경을 사용하면 애플리케이션이 데이터베이스 내 데이터의 실시간 변경 사항을 추적할 수 있음
- 샤드 클러스터의 변경 사항은 전역 논리 클록을 사용해 순서대로 보관됨 