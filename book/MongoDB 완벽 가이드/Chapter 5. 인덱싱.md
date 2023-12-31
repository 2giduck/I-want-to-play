#### Chapter 5. 인덱싱

5.1 인덱싱 소개
데이터베이스는 전체 내용을 살펴보는 대신 특정 내용을 가리키는 정렬된 리스트를 확인함.

따라서 엄청난 양의 명령을 더 빠르게 쿼리할 수 있다.

인덱스를 사용하지 않는 쿼리를 컬렉션 스캔(collection scan)이라 함.

전체 내용을 살펴봐야 함을 의미.

큰 컬렉션을 스캔할 때는 매우 느려지니 이런 방식은 피하자.

컬렉션을 쿼리할 때 explain 함수를 이용해 쿼리가 실행될 때 몽고DB가 무엇을 하는지 확인할 수 있는데, 자세한 것은 뒤에서 알아보자.

몽고DB가 쿼리에 효율적으로 응답하게 하려면 애플리케이션의 모든 쿼리 패턴에 인덱스를 사용해야 한다.



5.1.1 인덱스 생성

db.users.createIndex({"username":1)
createIndex 컬렉션 메서드를 사용.

인덱스는 쿼리 시간에 놀라운 차이를 만든다.

다만, 인덱싱된 필드를 변경하는 쓰기 작업은 더 오래 걸림.

데이터가 변경될 때마다 모든 인덱스를 갱신해야 하기 때문.

몽고DB에서 인덱스는 전형적인 관계형 데이터베이스의 인덱스와 거의 동일하게 작동함.

인덱스 생성 대상 필드를 선택하려면, 자주 쓰는 쿼리와 빨리 수행하는 쿼리를 조사해 공통적인 키 셋을 찾아보는게 중요함.



5.1.2 복합 인덱스 소개
상당수 쿼리 패턴은 두 개 이상의 키를 기반으로 인덱스를 작성해야 함.

인덱스는 모든 값을 정렬된 순서로 보관하니 인덱스 키로 도큐먼트를 정렬하는 작업이 훨씬 빨라지게 함.

단, 인덱스가 앞 부분에 놓일 때만 정렬에 도움이 됨.

ex) username 인덱스만 존재하는데 db.user.find().sort({”age” : 1, “username” : 1}) 은 도움이 되질 않음.

정렬을 최적화하려면 “age”, “username”에 인덱스를 만들어야 하는데 이를 복합 인덱스 라고 함

쿼리에서 정렬 방향이 여러 개이거나 검색 조건에 여러 개의 키가 있을 때 유용함.

컬렉션에 {”age” : 1, “username” : 1} 로 인덱스를 만들면 다음과 같은 형태로 표현됨.


각 인덱스 항목은 나이, 사용자명을 포함하고 레코드 식별자를 가리킴.

레코드 식별자는 내부에서 스토리지 엔진에 의해 사용되며 도큐먼트 데이터를 찾음.

쿼리의 종류에 따라 인덱스를 사용하는 방법이 다름.



db.users.find({”age” : 21}).sort({”username” : -1})

단일 값을 찾는 동등 쿼리.

인덱스의 두 번째 필드로 인해 결과는 이미 적절한 순서로 정렬됨.

몽고DB는 {”age” : 21}과 일치하는 마지막 항목부터 순서대로 인덱스를 탐색함.

이 쿼리는 매우 효율적임

곧바로 정확한 나이로 건너뛸 수 있고 데이터를 올바른 순서로 반환하므로 결과를 정렬할 필요가 없음.

몽고DB는 인덱스를 어느 방향으로도 쉽게 탐색하므로 정렬 방향은 문제가 되지 않음을 알아두자!



db.users.find({”age” : {”$gte” : 21, “$lte” : 30}})

범위 쿼리이며 여러 값이 일치하는 도큐먼트를 찾아냄.

인덱스에 첫 번째 키인 “age”를 사용해 일치하는 도큐먼트를 반환받음.

일반적으로 인덱스 순서에 따라 도큐먼트 결과를 받환함.



db.users.find({”age” : {”$gte” : 21, “$lte” : 30}}).sort({”username” : 1})

다중값 쿼리이지만 정렬이 포함됨.

검색 조건에 맞는 인덱스를 사용함.

다만, 인덱스는 사용자명을 정렬된 순서로 반환하지 않으며, 쿼리는 사용자명에 따라 정렬된 결과를 요청함.

결과를 반환하기 전에 메모리에서 정렬해야 함을 의미.

일반적으로 이전 쿼리보다 비효율적임.

결과가 32메가바이트 이상이면 몽고DB는 데이터가 너무 많아 정렬을 거부한다는 오류를 내보냄.

같은 키를 역순으로 한 {”username” : 1, “age” : 1} 인덱스 또한 사용할 수 있음.

모든 인덱스 항목을 탐색하지만 원하는 순서로 되돌림.

“age” 부분을 이용해서 일치하는 도큐먼트를 가져옴.

거대한 인메모리 정렬이 필요하지 않다는 장점이 있음.

다만, 일치하는 값을 모두 찾으려면 전체 인덱스를 훑어야 함.

따라서 복합 인덱스를 구성할 때는 정렬 키를 첫 번째에 놓으면 좋다!



5.1.3 몽고DB가 인덱스를 선택하는 방법
5개의 인덱스가 있다고 가정.

쿼리가 들어오면 몽고DB는 쿼리 모양을 확인함.

모양은 검색할 필드와 정렬 여부 등 추가 정보와 관련 있음.

이 정보를 기반으로 쿼리를 충족하는 데 사용할 인덱스 후보 집합을 식별함.

3개가 쿼리 후보로 식별되었으면 각 인덱스 후보에 하나씩 총 3개의 쿼리 플랜을 만듦.

각각 다른 인덱스를 사용하는 3개의 병렬 스레드에서 쿼리를 실행함

어떤 스레드에서 가장 빨리 반환하는지 확인하기 위함임.

가장 먼저 목표 상태에 도달하는 쿼리 플랜이 승자가 됨.

앞으로 동일한 모양을 가진 쿼리에 인덱스를 사용함.

쿼리 플랜 캐시를 유지함.

시간이 지나 컬렉션과 인덱스가 변경되면 쿼리 플랜이 캐시에서 제거되고, 다시 가능한 쿼리 플랜을 실험해 해당 컬렉션 및 인덱스 집합에 가장 적합한 플랜을 찾음.




5.1.4 복합 인덱스 사용
읽기와 쓰기를 가능한한 효율적으로 수행하도록 인덱스를 설계하자.

먼저 인덱스의 선택성을 고려한다.

특정 쿼리 패턴에서 스캔할 레코드 개수를 인덱스가 얼마나 최소화하는지에 관심 있음.

약 백만 개의 레코드가 포함된 학생 데이터셋을 사용하는 예제가 있음.

인덱스 두 개로 시작해서 이러한 인덱스를 어떻게 사용하는지 (또는 사용하지 않는지) 살펴본다.


db.students.createIndex({class_id:1})
db.students.createIndex({student_id:1, class_id:1})

db.students.find({student_id:{$gt:500000}, class_id:54})
.sort({student_id:1})
.explain("executionStats")
explain 출력에서 “executionStats” 필드는 선정된 쿼리 플랜에 대해 완료된 쿼리 실행을 설명하는 통계를 포함함.

“totalKeysExamined”, “nReturned”와 비교하면 몽고DB가 쿼리와 일치하는 도큐먼트를 찾으려고 얼마나 많은 인덱스를 통과했는지 알 수 있음.

도큐먼트 9903개를 찾으려고 인덱스 키 85만 477개를 검사함.

“executionTimeMills”로 봤을 때 쿼리 실행하는데 4.3초가 넘게 걸림.

선택성은 인덱스 설계 시 핵심 목표이므로 예제 쿼리의 인덱스에서 잘못된 부분을 파악해보자.

explain 출력 맨 위에는 선정된 쿼리 플랜이 있음 (”winningPlan” 필드 참조)



우리는 사용한 인덱스 종류와 몽고DB가 인메모리 정렬을 수행해야 하는지 여부에 특히 관심이 있음.

student_id, class_id 복합 인덱스 사용.

쿼리 플랜을 단계 트리로 표시함.

하위 단계 개수에 따라 하나 이상의 입력 단계가 있을 수 있고, 입력 단계는 도큐먼트나 인덱스 키를 상위 단계에 제공함.

입력 단계에 인덱스 스캔이 하나 있었고, 해당 스캔은 쿼리와 일치하는 도큐먼트에 대한 레코드 ID를 상위 단계인 “FETCH” 단계에 제공함.

“FETCH” 단계는 도큐먼트를 검색하고 클라이언트가 요청하면 일괄적으로 반환함.

선정된 플랜 아래에는 거부된 플랜이 있음.



실패한 쿼리 플랜은 class_id를 기반으로 하는 인덱스 사용.

인메모리 정렬을 수행.

쿼리 플랜에 “SORT” 단계가 표시된다면, 이는 몽고DB가 데이터베이스에서 결과 셋을 정렬할 때 인덱스를 사용할 수 없었으며 대신 인메모리 정렬을 했다는 의미.

쿼리에서 선정된 인덱스는 정렬된 출력을 반환할 수 있는 인덱스이며, 선정되려면 정렬된 결과 도큐먼트의 시험 수에 도달해야 했음.

다른 플랜이 선정되려면 해당 쿼리 스레드가 전체 결과 셋(거의 1만 개 도큐먼트)을 먼저 반환해야 했음.

여기서 문제는 선택성임.

“student_id”가 50만보다 큰 레코드를 요청하므로 광범위한 “student_id” 값을 지정함.

이는 컬렉션 레코드의 약 절반.


db.students.find({student_id:{$gt:500000}, class_id:54})
.sort({student_id:1})
.explain("executionStats")
이 쿼리에는 다중값 부분과 동등 부분이 모두 포함되어 있지만 “class_id”가 쿼리를 실행하는 데 훨씬 더 선택적인 기준으로 사용됨.

따라서 실패한 쿼리 플랜에 있는 “class_id”만을 기반으로 인덱스를 사용하는 것이 좋음.

몽고DB는 데이터베이스가 특정 인덱스를 사용하도록 강제하는 두 가지 방법을 제공하지만, 쿼리 플래너의 결과를 재정의하는 방법은 매우 신중히 사용해야 함. 운영환경 배포 시 사용해서는 안됨.

커서 hint 메서드를 사용하면 사용할 인덱스를 지정할 수 있는데 아래와 같이 사용하면 출력이 상당히 달라짐.


db.students.find({student_id:{$gt:500000}, class_id:54})
.sort({student_id:1})
.hint({class_id:1})
.explain("executionStats")
약 85만 개의 인덱스 키를 스캔한 것에서 약 2만개로 줄어들어 1만 개 미만의 인덱스 키를 얻었음.

실행 시간 또한 4.3초가 아닌 272밀리초밖에 되지 않음.

일반적으로 동등 필터를 사용할 필드가 다중값 필터를 사용할 필드보다 앞에 오도록 복합 인덱스를 설계해야 한다.


db.students.createIndex({class_id:1, student_id:1})
hint가 필요하지 않고 “nReturned”, “TotalKeyExamined”가 동일한 빠른 쿼리(37밀리초)를 확인할 수 있음.

복합 인덱스를 설계할 때는 인덱스를 사용할 공통 쿼리 패턴의 동등 필터, 다중값 필터, 정렬 구성 요소를 처리하는 방법을 알아야 함.

이는 모든 복합 인덱스 설계 시 고려해야 하며, 인덱스를 올바르게 설계하면 최상의 쿼리 성능을 얻을 수 있음.


만약 이러한 정렬이 추가된다면..?


db.students.find({student_id:{$gt:500000}, class_id:54})
.sort({final_grade:1})
.explain("executionStats")
쿼리 플랜에 “SORT” 단계가 포함되며, 인메모리 정렬을 수행함을 알 수 있음.

인덱스를 더 잘 설계하면 인메모리 정렬을 피할 수 있으나 복합 인덱스 설계에는 트레이드오프가 있음.

인메모리 정렬을 피하려면 반환하는 도큐먼트 개수보다 더 많은 키를 검사해야 하고, 인덱스를 사용해 정렬하려면 몽고DB가 인덱스 키를 순서대로 살펴볼 수 있어야 함.

즉, 복합 인덱스 키 사이에 정렬 필드를 포함해야 함.


db.students.createIndex({class_id:1, final_grade:1, student_id:1})
이 복합 인덱스는 몽고DB가 결과 셋에 포함될 도큐먼트보다 더 많은 도큐먼트의 키를 검사하게 하지만, 인덱스를 사용해 도큐먼트를 정렬함으로써 실행 시간을 절약할 수 있음.


요약하자면 복합 인덱스를 설계할 때

동등 필터에 대한 키를 맨 앞에 표시해야 함.

정렬에 사용되는 키는 다중값 필드 앞에 표시해야 함.

다중값 필터에 대한 키는 마지막에 표시해야 함.



키 방향 선택하기
두 개(혹은 그 이상)의 검색 조건으로 정렬할 때는 인덱스 키의 방향이 서로 달라야 함.

복합 정렬을 서로 다른 방향으로 최적화하려면 방향이 맞는 인덱스를 사용해야 함.

역방향 인덱스(각 방향에 -1을 곱한다)는 서로 동등함을 알아두자.

{”age” : 1, “username” : -1} 과 {”age” : -1, “username” : 1} 은 동일한 쿼리를 충족함.

인덱스 방향은 다중 조건에 따라 정렬할 때만 문제가 됨.

단일 키로 정렬하면 몽고DB는 인덱스를 쉽게 역순으로 읽을 수 있다.

{”age” : -1}로 정렬해야 하는데 {”age” : 1} 인덱스나 {”age” : 1} 인덱스나 동일하게 정렬을 최적화할 수 있음. (따라서 둘 다 생성 x)

방향은 다중키로 정렬할 때만 문제!



커버드 쿼리 사용하기
쿼리가 단지 인덱스에 포함된 필드를 찾는 중이라면 도큐먼트를 가져올 필요가 없음.

인덱스가 쿼리가 요구하는 값을 모두 포함하면, 쿼리가 커버드된다고 함.

실무에서는 항상 커버드 쿼리를 사용하자!

커버드 쿼리에 explain을 실행하면 결과에 “FETCH” 단계의 하위 단계가 아닌 “IXSCAN” 단계가 있으며, “executionStats”에서는 “totalDocsExamined”의 값이 0이 됨.



암시적 인덱스
복합 인덱스는 ‘이중 임무’를 수행할 수 있으며 쿼리마다 다른 인덱스처럼 동작할 수 있음.

{”age” : 1, “username” : -1} 는 자체적으로 {”age” : 1} 인덱스를 가질 때처럼 사용됨.

인덱스의 접두사를 이용하는 쿼리에만 적용할 수 있음.

{”a” : 1, “b” : 1, … , “z” : 1} 복합 인덱스가 있을 때 {”b” : 1} 이나 {”a” : 1, “c” : 1}과 같은 인덱스를 사용하는 쿼리는 최적화되지 않음.



5.1.5 $ 연산자의 인덱스 사용법
비효율적인 연산자
일반적으로 부정 조건은 비효율적임.

“$ne”라는 쿼리는 인덱스를 사용하긴 하지만 잘 활용하지 못함.

지정된 항목을 제외한 모든 인덱스 항목을 살펴봐야 하므로 전체 인덱스를 살펴봐야 함.

“$not”은 종종 인덱스를 사용하는데, 어떻게 사용해야 하는지 모를 때가 많음.

쿼리 대부분은 테이블 스캔을 수행함.

“$nin”은 항상 테이블 수캔을 수행함.

이런 종류의 쿼리를 신속하게 수행하려면, 결과 셋이 적은 수의 도큐먼트를 반환하게끔 필터링하는 인덱스를 사용하도록 쿼리에 추가할 만함 절이 있는지 파악하자.



범위
완전 일치가 사용될 필드(예를 들면 “x” : 1)를 첫 번째에, 범위가 사용될 필드(예를 들면 “y” : {”$gt” : 3, “$lt” : 5})를 마지막에 놓자.

예시

특정 나이, 사용자명의 범위에 {”age” : 1, “username” : 1} 인덱스를 사용해 쿼리한다고 가정한다.

{”age” : 47, “username” : {”$gt”~}} → age 47로 건너뛰고 곧이어 user5, user8 사이의 사용자명 범위 내에서 검색함.

반대로 {”username” : 1, “age” : 1}로 인덱스를 사용한다고 가정하자.

{”age” : 47, “username” : {”$gt”~}} → user5, user8 사이의 사용자를 모두 살펴보고 age 47인 사용자를 뽑아내야 하므로 쿼리 플랜을 변경함.

두 번째는 첫 번째 경우보다 인덱스 사용할 때의 100배가 되는 인덱스 항목을 살펴보도록 강제한다.

하나의 쿼리에 두 개의 범위를 사용하면 비효율적인 쿼리 플랜이 된다.



OR 쿼리
현재 몽고DB는 쿼리당 하나의 인덱스만 사용할 수 있음.

“$or”는 두 개의 쿼리를 수행하고 결과를 합치므로 “$or” 절마다 하나씩 인덱스를 사용할 수 있음.

일반적으로 두 번 쿼리해서 결과를 병합하면 한 번 쿼리할 때보다 훨씬 비효율적임.

가능하면 “$or” 보다는 “$in”을 사용하자.

“$or”을 사용해야 한다면 몽고DB가 두 쿼리 결과를 조사하고 중복(2개 이상의 “$or” 절에 일치하는 도큐먼트)을 모두 제거해야 함을 명심하자.

“$in” 쿼리를 실행할 때, 정렬을 제외하면 반환되는 도큐먼트의 순서를 제어하는 방법은 없음.



5.1.6 객체 및 배열 인덱싱
몽고DB는 도큐먼트 내부에 도달해서 내장 필드와 배열에 인덱스를 생성하도록 허용함.

내장 객체와 배열 필드는 복합 인덱스에서 최상위 필드와 결합될 수 있으며, 다소 특수한 경우를 제외하면 대부분 ‘일반적인’ 인덱스 필드와 같은 방식으로 동작함.



내장 도큐먼트 인덱싱하기
인덱스는 내장 도큐먼트 키에 생성될 수 있음.


db.users.createIndex({"loc.city" : 1})
내장 도큐먼트 자체(”loc”)를 인덱싱하면 내장 도큐먼트의 필드(”loc.city”)를 인덱싱할 때와는 매우 다르게 동작한다는 점을 알아두자.

서브 도큐먼트 전체를 인덱싱하면, 서브 도큐먼트 전체에 쿼리할 때만 도움이 된다.

쿼리 옵티마이저는 도큐먼트 전체가 올바른 필드 순서로 기술된 쿼리에만 “loc” 인덱스를 사용할 수 있다.



배열 인덱싱하기
배열에도 인덱스를 생성할 수 있음.

인덱스를 사용하면 배열의 특정 요소를 효율적으로 찾을 수 있음.


db.blog.createIndex({"comments.date" : 1})
입력, 갱신, 제거 작업을 하려면 모든 배열 요소(잠재적으로 수천 개의 인덱스 항목)가 갱신돼야 하므로 배열 인덱스를 단일값 인덱스보다 더 부담스럽게 만든다.

배열 필드 인덱싱은 배열 자체가 아니라 배열의 각 요소를 인덱싱하기 때문에 배열 전체를 단일 개체처럼 인덱싱할 수는 없다.

근데 배열 자체를 인덱싱할 수 없다고 하는데 p167 예제에서 어떻게 가능하지..?

배열 자체로 인덱스를 만들 수는 있어도 각 요소를 인덱싱 한다는거지 배열 전체를 단일 개체처럼 인덱싱하지 않는다는 의미인 것 같다.

특정 배열 요소를 찾는 쿼리에는 인덱싱을 사용할 수는 없고, 특정 항목에 인덱스를 생성할 수는 있음.

인덱스 항목의 한 필드만 배열로 가져올 수 있음.

여러 다중키 인덱스에 의해 인덱스 항목이 폭발적으로 늘어나는 것을 피하기 위함임.



다중키 인덱스가 미치는 영향
배열 필드를 인덱스 키로 가지면 인덱스는 즉시 다중키 인덱스로 표시됨.

explain()에서 isMultiKey 필드가 true

다중키로 표시되면 필드 내 배열을 포함하는 도큐먼트가 모두 제거되더라도 비다중키가 될 수 없음.

비다중키가 되게 하려면 인덱스를 삭제하고 다시 생성해야만 함.

다중키 인덱스는 비다중키 인덱스보다 약간 느릴 수 있음.

하나의 도큐먼트를 여러 개의 인덱스 항목이 가리킬 수 있으므로 몽고DB는 결과를 반환하기 전에 중복을 제거해야 함.



5.1.7 인덱스 카디널리티
카디널리티는 컬렉션의 한 필드에 대해 고윳값이 얼마나 많은지 나타냄.

필드의 카디널리티가 높을수록 인덱싱이 더욱 도움이 됨.

검색 범위를 훨씬 작은 결과 셋으로 빠르게 좁힐 수 있음.

낮은 커디널리티 필드에서 인덱스는 높은 카디널리티 필드에서만큼 일치하는 항목을 많이 제거할 수 없음.

일반적으로 높은 카디널리티 키를 생성하면 좋음.

적어도 복합 인덱스에서 높은 카디널리티 키를 낮은 카디널리티 키보다 앞에 놓자!



5.2 explain 출력
쿼리에 대한 많은 정보를 제공하며, 느린 쿼리를 위한 중요한 진단 도구.

인덱스를 사용하지 않는 쿼리의 explain이 가장 일반적임.

“COLLSCAN”을 사용하면 인덱스를 사용하지 않음을 알 수 있음.

인덱스를 사용하는 쿼리의 explain 출력은 다양함.

필드 종류

“isMultiKey” : false

다중키 인덱스 사용 여부

“nReturned” : 8449

쿼리에 의해 반환된 도큐먼트 개수

“totalDocsExamined” : 8449

몽고DB가 디스크 내 실제 도큐먼트를 가리키는 인덱스 포인터를 따라간 횟수.

인덱스에 포함되지 않는 필드를 반환하도록 요청한다면 각 인덱스 항목이 가리키는 도큐먼트를 살펴봐야 함.

“totalKeysExamine” : 8449

인덱스가 사용됐다면 살펴본 인덱스 항목 개수, 테이블 스캔을 했다면 조사한 도큐먼트 개수

“stage” : “IXSCAN”

몽고DB가 인덱스를 사용해 쿼리할 수 있었는지 여부.

“COLSCAN”은 인덱스로 쿼리할 수 없어 컬렉션 스캔을 수행했음을 뜻함.

“needYields” : 0

쓰기 요청을 처리하도록 쿼리가 양보(일시 중지)한 횟수.

“executionTimeMills” : 15

데이터베이스가 쿼리하는 데 걸린 시간. 밀리초 단위이며 낮을수록 좋음.

“indexBounds” : {…}

인덱스가 어떻게 사용됐는지 설명하며 탐색한 인덱스의 범위를 제공함.

쿼리에 사용하려 했던 인덱스와 다른 인덱스를 몽고DB가 사용한다면 hint를 사용해 특정 인덱스를 사용하도록 강제할 수 있음.



5.3 인덱스를 생성하지 않는 경우
인덱스는 컬렉션에서 가져와야 하는 부분이 많을수록 비효율적임.

인덱스를 하나 사용하려면 두 번의 조회를 해야 하기 때문.

한번은 인덱스 항목, 또 한번은 도큐먼트를 가리키는 인덱스 포인터를 따라감.

반면에 컬렉션 스캔할 때는 도큐먼트만 살펴보면 됨.

최악의 경우(컬렉션의 모든 도큐먼트를 반환해야 할 때), 인덱스를 사용하면 두 배나 많은 조회를 수행하며 이는 대체로 컬렉션 스캔보다 훨씬 느림.

아쉽게도 인덱스가 도움 될지 방해 될지를 알 수 있는 공식은 없음.

대체로 쿼리가 컬렉션의 30% 이상을 반환하는 경우 인덱스는 종종 쿼리 속도를 높임.




5.4 인덱스 종류
인덱스를 구축할 때 인덱스 옵션을 지정해 동작 방식을 바꿀 수 있음.



5.4.1 고유 인덱스
고유 인덱스는 각 값이 인덱스에 최대 한 번 나타나도록 보장함.


db.users.createIndex({"firstname" : 1},
... {"unique" : true, "partialFilterExpression" : {
"firstname" : {$exists: true}}}
)
중복 키 예외를 발생시키면 매우 비효율적이므로, 수많은 중복을 필터링하기보다는 가끔씩 발생하는 중복에 고유 제약 조건을 사용한다.

이미 익숙한 고유 인덱스인 “_id” 인덱스는 컬렉션을 생성하면 항상 자동으로 생성됨.

다른 고유 인덱스와 달리 삭제할 수 없다는 점을 제외하면 일반적인 고유 인덱스.

고유 인덱스를 생성한 후 인덱싱된 필드가 없는 도큐먼트 2개 이상 삽입하려고 시도하면, 이미 null 값을 갖는 도큐먼트가 존재하기 때문에 실패함.

인덱스 버킷은 크기 제한이 있으며, 제한을 넘는 인덱싱 항목은 인덱스에 포함되지 않음.

이는 인덱스를 사용하는 쿼리에서 도큐먼트를 볼 수 없게 만들어 혼동을 일으킬 수 있다.

몽고DB 4.2 이전 버전에서 필드는 인덱스에 포함되려면 1024 바이트보다 작아야 했음.

몽고DB 4.2 이후 버전에서는 이러한 제약이 사라졌음.



복합 고유 인덱스
복합 고유 인덱스를 만들 수도 있음.

개별 키는 같은 값을 가질 수 있지만 인덱스 항목의 모든 키에 걸친 값의 조합은 인덱스에서 최대 한 번만 나타남.



중복 제거하기
기존 컬렉션에 고유 인덱스를 구축할 때 중복된 값이 있으면 실패함.



5.4.2 부분 인덱스
오직 키가 존재할 때만 고유 인덱스가 적용되도록 할 때가 많음.

고유한 필드가 존재하거나 필드가 아예 존재하지 않으면 “unique”와 “partial”을 결합할 수 있음.

부분 인덱스를 만들려면 “partialFilterExpression” 옵션을 포함시킴.

생성하려는 필터 표현식을 나타내는 도큐먼트와 함께 희소 인덱스가 제공하는 기능의 슈퍼셋을 나타냄.

부분 인덱스는 반드시 고유할 필요는 없음.



5.5 인덱스 관리
인덱스는 컬렉션 당 한 번만 만들어야 함.

동일한 인덱스를 다시 생성하면 아무 일도 일어나지 않음.

인덱스 정보는 system.indexes 컬렉션에 저장됨.

예약된 컬렉션으로 안에 있는 도큐먼트 수정, 제거할 수 없음.

createIndex, dropIndexes 등 명령어로만 조작 가능.

특정 컬렉션의 모든 인덱스 정보를 확인하려면 db.컬렉션명.getIndexes()를 실행한다.

인덱스명은 dropIndexes와 같은 관리적인 인덱스 작업에서 식별자로 사용됨.

인덱스의 다중키 여부는 명세에 나타나지 않음.

“v” 필드는 내부적으로 인덱스 버저닝에 사용됨.



5.5.1 인덱스 식별
인덱스명은 서버에서 인덱스를 삭제하거나 조작하는 데 사용됨.

인덱스명은 기본적으로 키명1_방향1_키명2_방향2_…_키명N_방향N

createIndex의 옵션으로 원하는 이름 지정 가능.

글자 수 제한이 있어 사용자 지정 이름이 필요할 수도 있음.



5.5.2 인덱스 변경
dropIndex 명령을 사용해 불필요한 인덱스를 제거하자.

name 필드를 사용해서 삭제할 인덱스를 지정.

몽고DB 4.2 이후 버전에서는 인덱스를 최대한 빨리 구축하기 위해, 구축이 완료될 때까지 데이터베이스의 모든 읽기와 쓰기를 중단함.

읽기, 쓰기에 어느 정도 응답하게 하려면 “background” 옵션을 사용하자.

몽고DB 4.2는 하이브리드 인덱스 구축이라는 새로운 접근 방식 도입

인덱스 구축 프로세스의 시작, 끝에만 락을 가지며, 따라서 프로세스의 나머지 부분은 읽기 및 쓰기 작업을 인터리빙함.

포그라운드, 백그라운드 인덱싱을 모두 대체.

(가능하다면) 기존 도큐먼트에 인덱스를 생성하면 인덱스를 먼저 생성한 후 모든 도큐먼트를 끼워 넣을 때보다 조금 빠르다.