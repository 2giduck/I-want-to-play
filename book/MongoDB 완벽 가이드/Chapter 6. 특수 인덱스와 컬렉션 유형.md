#### Chapter 6. 특수 인덱스와 컬렉션 유형

큐 같은 데이터를 위한 제한 컬렉션

캐시를 위한 TTL 인덱스

단순 문자열 검색을 위한 전문 인덱스

2D 구현 및 구면 기하학을 위한 공간 정보 인덱스

대용량 파일 저장을 위한 GridFS

6.1 공간 정보 인덱스
몽고DB는 2dsphere와 2d라는 공간 정보 인덱스를 가짐

2dsphere 인덱스: WGS84 좌표계를 기반으로 지표면을 모델링하는 구면 기하학으로 작동

지구의 형태를 고려하므로 2d 인덱스를 사용할 때보다 더 정확한 거리 계산 가능

2d: 평면의 점에 사용

점: [경도, 위도]


{
"name": "New York City",
"loc": {
"type": "Point",
"coordinates": [50, 2]
}
}
선: 점의 배열


{
"name": "Hudson River",
"loc": {
"type": "LineString",
"coordinates": [[0,1], [0,2], [1,2]]
}
}
다각형: 선과 같은 방식(점의 배열), type이 다름


{
"name": "New England",
"loc": {
"type": "Polygon",
"coordinates": [[0,1], [0,2], [1,2]]
}
}
createIndex로 “2dsphere”를 사용하여 공간 정보 인덱스를 만들 수 있음

db.openStreetMap.createIndex({"loc": "2dsphere"})

6.1.1 공간 정보 쿼리 유형
공간 정보 쿼리는 교차, 포함, 근접이라는 세 가지 유형이 있음

찾을 항목을 {"$geometry": geoJsonDesc}과 같은 GeoJSON 형태로 지정함

"$geoIntersects" 연산자를 이용해 쿼리 위치와 교차하는 도큐먼트를 찾을 수 있음


db.openStreetMap.find({“loc”: {“$geoIntersects” : {“$geometry”: eastVillage}}})
// eastVillage 내에 한 점을 갖는 점, 선, 다각형이 포함된 도큐먼트를 모두 찾음
"$geoWithin"을 이용해 특정 지역에 완전히 포함된 항목을 쿼리할 수 있음


db.openStreetMap.find({“loc”: {“$geoWithin” : {“$geometry”: eastVillage}}})
// eastVillage 내에 완전히 포함된 도큐먼트를 반환
// eastVillage를 가로지르거나, 부분적으로 겹치는 다각형은 반환하지 않음
"$near"을 이용해 주변 위치에 쿼리할 수 있음


db.openStreetMap.find({“loc”: {“$near” : {“$geometry”: eastVillage}}})
// 거리가 가까운 곳부터 가장 먼 곳 순으로 반환됨
6.1.2 공간 정보 인덱스 사용
공간 정보 인덱스를 사용해 특정 지역과 관련된 모양과 점이 포함된 컬렉션에서 공간 쿼리를 효율적으로 실행할 수 있음

공간 정보 쿼리는 쿼리와 사용 중인 인덱스 유형에 따라 구면 또는 2D 구조를 사용함


공간 정보 연산자가 사용하는 기하 구조 유형
2d 인덱스는 구(sphere)에서 평면 기하학과 거리 계산을 모두 지원함

하지만 구면 기하학을 사용하는 쿼리는 2dsphere 인덱스를 사용할 때 성능과 정확성이 향상됨

$geoNear 집계 연산자를 사용하려면 컬렉션에 2dsphere 인덱스와 2d 인덱스가 최대 1개만 있어야 함

반면, $near나 $geoWithin과 같은 공간 정보 쿼리 연산자는 컬렉션이 여러 개의 공간 정보 인덱스를 갖도록 허용함

예제
사용자가 뉴욕에서 레스토랑을 찾도록 돕는 모바일 애플리케이션

사용자가 현재 위치한 지역을 찾는다

해당 지역 내 레스토랑 수를 보여준다

지정된 거리 내에 있는 레스토랑을 찾는다

레스토랑 검색

mongoimport 도구를 이용하여 데이터셋(뉴욕 지역, 레스토랑)을 데이터베이스로 가져옴


$ mongoimport <path to neighborhoods.json> -c neighborhoods
$ mongoimport <path to restaurants.json> -c restaurants
mongo 셸에서 createIndex 명령을 사용해 각 컬렉션에 2dsphere 인덱스를 만듦


> db.neighborhoods.createIndex({location: "2dsphere"})
> db.neighborhoods.createIndex({location: "2dsphere"})
현재 지역 찾기


> db.neightborhoods.findOne({geometry: {$geoIntersects:{$geometry:{type:"Point",
... coordinates: [-73.93414657, 40.82302903]}}}})
// 사용자의 경도, 위도가 각각 -73..., 40...이라고 가정
지역 내 모든 레스토랑 찾기


> var neighborhood = // 찾은 현재 지역을 변수에 넣기
> db.restaurants.find({
location: {
$geoWithin: {
// 앞에서 검색한 객체의 기하 구조를 사용
$geometry: neighborhood.geometry
}
}
},
// 일치하는 레스토랑의 이름만 표시
{name:1, _id: 0});
범위 내에서 레스토랑 찾기


> var METERS_PER_MILE = 1609.34;
db.restaurants.find({
location: {
$nearSphere: {
$geometry: {
type: "Point",
coordinates: [-73.93414657, 40.82302903]
},
$maxDistance: 5*METERS_PER_MILE
}
}
})
// 사용자로부터 5마일 내에 있는 모든 레스토랑을 가장 가까운 곳부터 먼 순으로 반환
6.1.3 복합 공간 정보 인덱스
공간 정보 인덱스는 다른 필드와 묶음으로써 더 복잡한 쿼리를 최적화할 수 있음


> db.openStreetMap.createIndex({"tags": 1, "location": "2dsphere"})
6.1.4 2d 인덱스
비구체 지도(비디오 게임 지도, 시계열 데이터 등)에는 “2dsphere” 대신 “2d” 인덱스를 사용함

도큐먼트는 “2d” 인덱스 필드에 요소가 2개인 배열을 사용하며, 배열 요소는 각각 경도와 위도 좌표를 나타냄

기본적으로 2d 인덱스는 값이 -180과 180 사이에 있다고 가정함

범위를 넓히거나 좁히려면 createIndex를 사용해 최솟값과 최댓값을 지정함

“$geoWithin”은 평평한 표면에 정의된 영역 내 점을 쿼리하는 데 사용함


> db.hyrule.find({
title: {
$geoWithin: {
$box: [[10, 10], [100, 100]]
}
}
}) // 왼쪽 하단 모서리 [10, 10]과 오른쪽 상단 모서리[100, 100]으로 정의된 사각형 다큐먼트에 대한 쿼리
몽고DB는 레거시 지원을 위해 2d 인덱스에 대한 기초적인 구형 쿼리도 지원함

6.2 전문 검색을 위한 인덱스
몽고DB의 text 인덱스는 전문 검색의 요구 사항을 지원함

아틀라스 전문 검색 인덱스와 다름. → 아파치 루씬을 활용해 추가적인 검색 기능을 구현함

제목, 설명 등 컬렉션 내에 있는 필드의 텍스트와 일치시키는 키워드 쿼리를 하게 하려면 text 인덱스 사용

text 인덱스는 토큰화, 정지 단어, 형태소 분석 등 일반적인 검색 엔진 요구 사항을 지원함

text 인덱스를 만들면 시스템 리소스가 많이 소비될 수 있어, 인덱스 생성이 애플리케이션 성능에 부정적인 영향을 미치지 않을 때 생성해야 함

text로 인덱싱된 컬렉션에서는 쓰기 성능이 다른 컬렉션보다 떨어지는 경향이 있음

6.2.1 텍스트 인덱스 생성
키에 순서가 있는 복합 인덱스와 달리, 각 필드는 text 인덱스에서 동등하게 고려됨

가중치를 지정하면 각 필드에 지정하는 상대적 중요도를 제어할 수 있음

인덱스를 삭제하고 다시 생성하지 않는 한 필드 가중치를 변경할 수 없음


> db.articles.createIndex({ "title": "text", "body": "text"});
> db.articles.createIndex({ "title": "text", "body": "text"}, {"weights": {"title": 3, "body": 2}});
“$ **”에 인덱스를 만들면 도큐먼트의 모든 문자열 필드에 전문 인덱스를 생성할 수 있음

모든 문자열 필드를 인덱싱하고, 내장 도큐먼트와 배열에서 문자열 필드를 검색함


> db.articles.createIndex({"$**": "text"})
6.2.2 텍스트 검색
“$text” 쿼리 연산자를 사용해 text 인덱스가 있는 컬렉션에 테스트 검색을 수행 가능함

공백과 구두점을 구분 기호로 사용해 검색 문자열을 토큰화함

검색 문자열에서 모든 토큰의 논리적 OR를 수행함

AND를 수행하려면 각 용어를 큰따옴표로 묶어 구문으로 처리


> db.articles.find({"$text": {"$search": "impact crater lunar"}}, {title: 1}).limit(10)
// "impact"나 "crater"또는 "lunar"라는 용어가 포함된 기사를 모두 찾음
> db.articles.find({"$text": {"$search": "\"impact crater\" lunar"}}, {title: 1}).limit(10)
// impact crater AND lunar
> db.articles.find({"$text": {"$search": "\"impact crater\" lunar meteor"}}, {title: 1}).limit(10)
// impact crater AND (lunar OR meter)
관련성 스코어는 “textScore”라는 메타 데이터 필드에 저장됨


> db.articles.find({"$text": {"$search": "\"impact crater\" lunar"}},
{title: 1, score: {$meta: "textScore"}}).limit(10)
// 결과마다 제목과 예상되는 관련성 점수를 볼 수 있음, 단 결과는 정렬되지 않음
6.2.3 전문 검색 최적화
복합 인덱스를 생성할 때 다른 기준을 첫 번째로 두고 전문 필드를 그 다음으로 둠


> db.blog.createIndex({"date": 1, "post": "text"})
// date에 따라 몇 개의 작은 트리 구조로 쪼갬 -> 파티셔닝(partitioning)
혹은, 다른 기준을 뒤쪽에 두어 사용할 수도 있음.


> db.blog.createIndex({"post": "text", "author": 1})
접두사 방식과 접미사 방식을 합칠 수도 있음


> db.blog.createIndex({"date": 1, "post": "text", "author": 1})
6.2.4 다른 언어로 검색하기
도큐먼트가 입력되면(혹은 인덱스가 처음 생성되면) 몽고 DB는 인덱스 필드를 살펴보고 기본 구성 단위로 줄여가며 각 단어의 형태소를 분석함

언어에 따라 형태소 분석 방법이 다르므로 어떤 언어로 쓰였는지 명시해야 함


> db.users.createIndex({"profile": "text", "interets": "text"}, {"default_language": "french"})
// 별도로 언어를 지정하지 않는 한 프랑스어가 형태소 분석에 사용됨
도큐먼트의 언어를 language 필드에 명시해 도큐먼트별로 형태소 분석 언어를 다르게 지정할 수 있음


> db.users.insert({"username": "swedishChef", ...
"profile": "Bork de bork", language: "swedish"})
6.3 제한 컬렉션
몽고DB의 “일반적인” 컬렉션은 동적으로 생성되고 추가적인 데이터에 맞춰 크기가 자동으로 늘어남

“제한 컬렉션”이라는 컬렉션은, 미리 생성되어 크기가 고정됨



이미 가득 찬 제한 컬렉션에 입력을 시도하면 무슨 일이 생길까?


그러나 일반적으로 몽고DB TTL 인덱스가 와이어드타이거 스토리지 엔진에서 더 나은 성능을 발휘하므로 제한 컬렉션보다 더 권장됨

TTL 인덱스는 날짜 유형 필드 값과 인덱스의 TTL 값을 기반으로 일반 컬렉션에서 데이터가 만료되고 제거됨

제한 컬렉션은 유연성이 부족하지만 로깅에는 나름 유용함

6.3.1 제한 컬렉션 생성
일반 컬렉션과 달리 사용되기 전에 명시적으로 생성해야 됨


> db.createCollection("my_collection", {"capped": true, "size": 100000})
// 10만 바이트 고정 크기로 제한 컬렉션 생성
제한 컬렉션은 일단 생성되면 변경할 수 없음

기존의 일반 컬렉션을 제한 컬렉션으로 변환할 수 있음

그러나 제한 컬렉션을 일반 컬렉션으로 변환하는 방법은 없음


> db.runCommand({"convertToCapped": "test", "size": 10000});
// text 컬렉션을 1만 바이트 크기 제한 컬렉션으로 변환
6.3.2 꼬리를 무는 커서
꼬리를 무는 커서: 결과를 모두 꺼낸 후에도 종료되지 않는 특수한 형태의 커서

tail -f 명령어에서 영감을 받아 만들어짐

컬렉션에 데이터가 추가되면 새로운 결과를 바로 꺼낼 수 있음

일반 컬렉션에서는 입력 순서가 추적되지 않기 때문에, 꼬리를 무는 커서는 제한 컬렉션에만 사용

15장에서 다룰 스트림 변경(Change Stream)은 꼬리를 무는 커서보다 훨씬 많은 제어와 구성을 제공하며 일반 컬렉션에서도 작동하므로 대부분에 경우에 더 권장됨

아무런 결과가 없으면 10분 후에 종료됨

mongo 셸에서는 사용할 수 없지만, PHP에서는 가능함

6.4 TTL 인덱스
각 도큐먼트에 유효 시간을 설정하여, 설정한 시간에 도달하면 자동으로 지워짐

세션 스토리지와 같은 문제를 캐싱하는 데 유용함


> db.sessions.createIndex({"lastUpdated": 1}, {"expireAfterSeconds": 60*60*24})
// 24시간 뒤 타임아웃
lastUpdated 필드에 TTL 인덱스를 생성함

도큐먼트에 lastUpdated 필드가 존재하고 날짜형이라면, 서버 시간이 도큐먼트 시간의 expreAfterSeconds 초를 지나면 삭제됨

활동 중인 세션이 삭제되는 것을 방지하려면 활동이 있을 때마다 lastUpdate 필드를 현재 시간으로 갱신함

몽고DB는 TTL 인덱스를 매분마다 청소하므로 초 단위로 신경 쓸 필요 없음

collMod 명령어를 이용해 expireAfterSeconds를 변경할 수 있음


> db.runCommand({"collMod": "someapp.cache", "index": {"keyPattern": ...
{"lastUpdated": 1, "expireAfterSeconds": 3600}}})
하나의 컬렉션에 TTL 인덱스를 여러 개 가질 수 있음

6.5 GridFS로 파일 저장하기
GridFS: 몽고DB에 대용량 이진 파일을 저장하는 메커니즘

장점

아키텍처 스택을 단순화 → 파일 스토리지를 위한 별도 도구 없어도 됨

몽고DB를 위해 설정한 기존의 복제나 자동 샤딩을 이용할 수 있음

사용자가 올린 파일을 저장할 때 특정 파일시스템이 갖는 문제를 피할 수 있음

단점

성능이 느림

도큐먼트를 수정하려면 도큐먼트 전체를 삭제하고 다시 저장하는 방법밖에 없음

큰 변화가 없고 순차적인 방식으로 접근하려는 대용량 파일을 저장할 때 좋음

6.5.1 GridFS 시작하기: mongofiles
mongofiles 유틸리티로 쉽게 GridFS를 설치하고 운영함


$ echo "Hello, world" > foo.tx
$ mongofiles put foo.txt // 파일시스템에서 GridFS로 파일을 올림
$ mongofiles list // GridFS에 저장된 파일 목록을 보여줌
$ rm foo.txt
$ mongofiles get foo.txt // GridFS에서 올린 파일을 받아 파일시스템에 저장
$ cat foo.txt
6.5.2 몽고DB 드라이버로 GridFS 시작하기
모든 클라이언트 라이브러리는 GridFS API를 가짐

6.5.3 내부 살펴보기
GridFS는 파일 저장을 위한 간단한 명세이며 일반 몽고DB 도큐먼트를 기반으로 만들어짐

GridFS의 기본 개념은 대용량 파일을 청크로 나눈 후, 각 청크를 도큐먼트로 저장하는 것

GridFS의 청크는 자체 컬렉션에 저장됨

기본적으로 fs.chunks 컬렉션에 저장되지만, 바꿀 수 있음


{
"_id": ObjectId("..."),
"n": 0, // 다른 청크를 기준으로 하는 파일 내 청크의 위치
"data": BinData("..."), // 파일 내 청크의 크기
"files_id": ObjectId("...") // 청크에 대한 메타데이터를 포함하는 파일 도큐먼트의 "_id"
}
각 파일의 메타데이터는 기본적으로 별도의 컬렉션인 fs.files에 저장됨


GridFS 명세에서 강제하는 키