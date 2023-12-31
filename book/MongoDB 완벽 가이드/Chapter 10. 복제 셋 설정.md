### Chapter 10. 복제 셋 설정
- 복제 셋의 정의
- 복제 셋을 설정하는 방법
- 복제 셋 멤버 구성 옵션
#### 10.1 복제 소개
- 복제: 데이터의 동일한 복사본을 여러 서버상에서 보관하는 방법 
- 복제셋: 클라이언트 요청을 처리하는 프라이머리 서버 1 + 복사본을 갖는 세컨더리 여러개

#### 10.2 복제 셋 설정 - 1장
(생략)

#### 10.3 네트워크 고려 사항 
- 복제 셋의 모든 멤버는 같은 셋 내 다른 멤버와 연결할 수 있어야 함 
- 복제 셋의 각 멤버가 다른 멤버와 통신하려면 다른 멤버가 연결할 수 있는 IP 주소에도 바인딩 해야함

#### 10.4 보안 고려 사항
- localhost 이외의 IP 주소에 바인딩하기 전 복제 셋을 구성할 때, 권한 제어를 활성화하고 인증 메커니즘을 지정
- 디스크의 데이터를 암호화하고, 복제 셋 멤버간 통신 및 셋과 클라이언트 간 통신 암호화 

#### 10.5 복제 셋 설정 - 2장
- 각 멤버를 나열하는 구성(configuration)을 만들어 mongod 프로세스 중 하나로 보내면 멤버들은 서로의 존재를 알게 됨

#### 10.6 복제 관찰
- 복제 셋이 다른 노드를 프라이머리로 선출했다면, 셸을 종료하고 명령행에서 올바른 포트 번호를 지정해 프라이머리 노드에 연결함
- 클라이언트는 독립 실행형 서버에 보낼 수 있는 모든 작업을 프라이머리 서버에 보낼 수 있다
- 클라이언트는 세컨더리에 쓰기를 할 수 없다
- 기본적으로 클라이언트는 세컨더리로부터 읽을 수 없다

#### 10.7 복제 셋 구성 변경
- 멤버 추가, 삭제, 변경 가능 

#### 10.8 복제 셋 설계 방법
- 프라이머리를 선출하려면 멤버의 과반수 이상이 필요함 
- 이용 가능한 복제 셋 멤버가 과반수에 못 미치면 모든 멤버는 세컨더리가 됨
##### 10.8.1 어떻게 선출하는가
- 프라이머리 검사
  - 요청받은 멤버가 프라이머리에 도달할 수 있는가
  - 선출되고자 하는 멤버의 복제 데이터가 최신인가
  - 대신 선출돼야 하는 우선순위가 더 높은 멤버는 없는가

#### 10.9 멤버 구성 옵션
##### 10.9.1 우선순위
- 우선순위: 특정 멤버가 얼마나 프라이머리가 되기를 원하는지 나타내는 지표 
  - 0 ~ 100, 기본값은 1 
  - priority가 0이면 절대 프라이머리가 될 수 없음, 수동적 멤버

##### 10.9.2 숨겨진 멤버
- 클라이언트는 숨겨진 멤버에 요청을 라우팅하지 않음
- 숨겨진 멤버는 복제 소스로서 바람직하지 않음 

##### 10.9.3 아비터 선출
- 아비터: 프라이머리 선출에 참여하는 용도로만 쓰임 
  - 데이터를 가지지 않으며 클라이언트에 의해 사용되지 않음 
- 아비터에서 아비터가 아닌 것으로 재구성, 아비터가 아닌것을 아비터로 재구성 하는 것은 불가능함
- 아비터는 큰 클러스터상에서 동점 상황을 없앨 수 있음
- 아비터는 최대 하나까지만 사용해야 함 
- 데이터 노드와 아비터 중 하나 골라야 하면, 데이터 노드 선택 

##### 10.9.4 인덱스 구축 
- 때때로 세컨더리는 프라이머리에 존재하는 것과 동일한 인덱스를 갖지 않아도 됨 
