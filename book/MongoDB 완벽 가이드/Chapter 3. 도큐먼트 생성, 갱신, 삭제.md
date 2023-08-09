#### Chapter 3. 도큐먼트 생성, 갱신, 삭제
도큐먼트 삽입
insertOne 메소드 사용.


db.movies.insertOne({
"title": "토이 스토리"
})
→ 도큐먼트에 _id 키가 추가되고 (제공하지 않은 경우) 도큐먼트가 몽고디비에 저장됨.

insertMany

여러 도큐먼트를 컬렉션에 삽입할 때 사용.

한번에 대량 삽입 하므로 효율적. (수십, 수백, 수천 … 개를 한번에 전송하여 삽입이 빠름.)

몽고디비 현재 버전은 48MB보다 큰 메시지를 허용하지 않아 일괄 삽입 데이터 크기 제한이 있음. 보다 큰 삽입을 시도하면 일괄 삽입 여러개로 분할함.

삽입 중간에 특정 유형의 오류가 발생하는 경우 정렬, 비정렬 연산에 따라 상황이 달라짐.

insertMany 두 번째 매개변수로 옵션 도큐먼트를 지정할 수 있고 ordered 키를 통해 정렬 여부를 설정할 수 있음. (기본은 false 로 정렬된 삽입.)

false 지정 시 몽고디비가 성능 개선을 위해 삽입을 재배열 함.

만약 삽입 오류가 발생했을 때 배열에서 해당 지점을 벗어난 도큐먼트 면 삽입 하지 않고, 정렬되지 않은 삽입의 경우 일부 삽입 오류 여부와 관계 없이 모든 도큐먼트 삽입을 시도함.


// 처음 두 도큐먼트만 삽입 성공하고 _id 가 동일한 세 번째 도큐먼트 부터 삽입 실패
db.movies.insertMany([
{"id": 0, "title": "탑건"},
{"id": 1, "title": "에일리언"},
{"id": 1, "title": "해리포터"},
{"id": 2, "title": "반지의제왕"},
])

// _id 가 중복된 세 번째 도큐먼트만 삽입 실패
db.movies.insertMany([
{"id": 3, "title": "탑건"},
{"id": 4, "title": "에일리언"},
{"id": 4, "title": "해리포터"},
{"id": 5, "title": "반지의제왕"},
], {"ordered": false})
삽입 유효성 검사

몽고디비는 삽입된 데이터의 최소한의 검사를 수행.

_id 필드 존재여부 체크 및 새로 추가.

도큐먼트 크기 16MB 보다 작음 체크.

→ 나쁜 스키마 설계 예방 및 일관된 성능 보장함.

삽입 시 사용되는 메소드 중 3.0 이전에 지원하던 insert 메소드가 있는데 하위호환성을 위해 여전히 지원되나 insertOne, insertMany 를 사용하자.

도큐먼트 삭제
deleteOne, deleteMany 를 지원.

두 메소드 모두 필터를 첫 번째 매개변수로 사용.


// 특정 아이디로 도큐먼트 삭제
db.movies.deleteOne({"id": 4})

// 필터와 일치하는 모든 도큐먼트 삭제
db.movies.deleteMany({"year": 1984})
db.mailing.list.deleteMany({"opt-out": true})
몽고디비 3.0 이전에 remove 를 사용했음. (호환성을 위해 여전히 지원.) 하지만, deleteOne, deleteMany 를 사용하자.

drop


// 도큐먼트 모두 제거
db.movies.deleteMany({})

// 전체 컬렉션 삭제
db.movies.drop()
delete, drop 을 통해 삭제된 도큐멘트는 복구 불가.(자세한 내용은 백업 및 복원 23장 참조)

도큐먼트 갱신
updateOne, updateMany, replaceOne 갱신 메소드 사용.

updateOne, updateMany 은 필터를 첫 번째, 변경 사항 수정자 도큐먼트를 두 번째 매개변수로 사용.

replaceOne 은 필터를 첫 번째, 두 번째 매개변수는 필터와 일치하는 도큐먼트를 교체할 도큐먼트.

갱신은 원자적으로 이뤄지고, 요청 두 개가 동시에 발생하면 서버에 먼저 도착한 요청이 적용되고 다음 요청이 적용됨. 따라서, 요청이 빠르게 발생해도 마지막 요청이 최후 승리자가 되므로 도큐먼트는 변질 없이 안전하게 처리됨.

기본 동작을 원치 않으면 버저닝 패턴을 고려하자. (9장 스키마 디자인 패턴 참조)

치환

replaceOne

완전히 새 것으로 치환.

마이그레이션에 유용함.


// friends 필드를 서브 도큐먼트로 옮기기
var park = db.users.findOne({"name": "park"});
park.relationships = {"friends": park.friends};
db.users.replaceOne({"name": "park"}, park);

// 결과
{
"_id": ObjectId(“4basdasdalf631733d917a7a"),
"name": "park",
"relationships": {
"friends": 100
}
}
흔히 하는 실수

조건걸로 2개 이상 도큐먼트 일치되게 한 후 두 번째 매개변수로 중복된 _id 값을 갖는 도큐먼트를 생성하는 경우 → 오류 반환 및 변경 반영 안함.


// 고유한 도큐먼트를 갱신 대상으로 지정하는 것이 좋음
db.users.replaceOne({"_id": ObjectId("4b2b9f67a1f631733d917a7c")}, park);
추가로 _id 가 컬렉션의 기본 인덱스의 기초를 형성하므로 필터에 사용 시 효율적임.

갱신 연산자

키 변경, 추가, 제거, 배열과 내장 도큐먼트를 조작하는 복잡한 연산을 지정하는 특수키.

$inc 제한자


// pageViews 값 1 만큼 증가 시키기
db.analytics.updateOne({"url": "www.example.com"},{"$inc": {"pageviews": 1}});
_id 변경은 안되고 그 외 다른 키 값은 모두 변경할 수 있음.

“$set” 제한자

필드 값을 설정함.

필드 미 존재 시 새 필드 생성됨.

스키마를 갱신하거나 사용자 정의 키를 추가할 때 편리.


// 특정 사용자 도큐먼트에 test 키,값 설정하기
db.users.updateOne({"_id": ObjectId("aaaaaaaaaaa11111111111")}, {"$set": {"test": "test"}});

// 값 변경
db.users.updateOne({"_id": ObjectId("aaaaaaaaaaa11111111111")}, {"$set": {"test": "ttttteeeeessssssstttttt"}});

// 값 변경(배열로)
db.users.updateOne({"_id": ObjectId("aaaaaaaaaaa11111111111")}, {"$set": {"test": [1,2,3,'ㅋㅋㅋㅋ']}});

// 값 제거
db.users.updateOne({"_id": ObjectId("aaaaaaaaaaa11111111111")}, {"$unset": {"test": 1}});

// 내장 도큐먼트 데이터 변경
db.users.updateOne({"_id": ObjectId("aaaaaaaaaaa11111111111")}, {"$set": {"test.tt": "tttttttt"}});
실수하는 부분

$ 제한자 미 사용으로 키 값을 다른 값으로 변경

이전 버전에서는 해당 유형의 오류를 포착하지 못해 전체 도큐먼트가 바로 교체되어 버림.

함정을 피하기 위해 새로운 CRUD API 가 만들어졌음.

증가, 감소

$inc

숫자 증감을 위해 설계됨.

int, long, double, decimal 타입 값에만 사용 가능.

숫자가 아닌 값으로 증감을 시도하면 'Modifier $inc” allowed for numbers only'라는 오류 발생

베열 연산자

배열을 갱신함.

$push

배열이 이미 존재하면 끝에 요소를 추가하고, 그렇지 않으면 새로운 배열을 생성함.

몽고디비는 일부 연산자에 제한자를 제공함.

$push 에 $each 제한자를 사용하면 작업 한 번으로 여러 값을 추가할 수 있음.


// 배열에 5개 요소 추가
db.test.updateOne({"_id": "1234"}, {"$push": {"elements": {"$each":[1,2,3,4,5]}}});

// $slice 제한자로 배열이 특정 크기 이상으로 늘어나지 않도록 하기
db.test.updateOne({"name": "park"}, {"$push": {"top10": {"$each": [1,2,3,4,5],"$slice": -10}}});
배열을 집합으로 사용

특정 값이 배열에 존재하지 않을 때 해당 값을 추가하면서, 배열을 집합처럼 처리하려면 “$ne” 를 사용.

“$addToSet” 도 있는데 중복값 저장을 시도하면 저장 안함.

고유한 값 여러 개 추가는 “$addToSet”에 “$each” 를 결합.


// 인용 목록에 저자가 존재하지 않으면 저자 추가
db.papers.updateOne({"authors cited": {"$ne": "park"}}, {"$push": {"authors cited": "park"}});

// 고유한 값 여러개 추가($ne, $push 조합으로는 불가능)
db.users.updateOne({"_id": ObjectId("123123123123123asda")}, {"$addToSet": {"emails": {"$each": ["test@test.com","test@gmail.com"]}}});
요소 제거

배열 요소 제거.

큐, 스택처럼 사용하려면 “$pop” 을 사용.


// 마지막 요소 부터 제거
{"$pop": {"key" : 1}}
// 처음 요소 부터 제거
{"$pop": {"key" : -1}}
$pull 은 주어진 조건에 맞는 배열 요소를 제거. (조건과 일치하는 요소 모두 제거)


// 순서 상관 없는 할일 목록에서 특정 요소 제거
db.lists.updateOne({}, {"$pull": {"todo": "laudry"}});
배열에 만 한정된 연산자라 정수형, 문자형은 “$set”, “$inc” 를 사용해야함.

배열의 위치 기반 변경

배열 내 여러 값을 다루는 방법.

위치 연산자를 사용.


// 첫 번째 댓글의 투표수 증가
db.blog.updateOne({"post": "post_id"},{"$inc": {"comments.0.votes": 1}});
하지만 보통 도큐먼트를 쿼리해서 검사해보지 않고는 배열의 몇 번째 요소를 변경할지 알 수 없음.

위 문제 해결을 위해 쿼리 도큐먼트와 일치하는 배열 요소 및 요소의 위치를 알아내서 갱신하는 위치 연산자 “$”를 제공함.


// park 이라는 사용자가 이름을 parkk로 갱신하려고 할 때 위치 연산자 사용
db.blog.updateOne({"comments.autor": "park"}, {"$set": {"comments.$.autor": "parkk"}});
위 연산자는 첫 번째로 일치하는 요소만 갱신함.

만약 park 이 댓글을 2개 이상 남겼다면 첫번째 작성자면 parkk 로 변경됨.

배열 필터를 이용한 갱신

몽고디비 3.6 에서는 개별 배열 요소를 갱신하는 필터 arrayFilters 를 도입.


// 반대표 5표 이상인 댓글 숨기기
db.blog.updateOne(
{"post": post_id},
{"$set": {"comments.$[elem].hidden": true}},
{arrayFilters: ["elem.votes": {"$lte": -5}]}
);
comments 배열의 각 일치 요소에 대한 식별자로 elem 을 정의함.(elem 말고 아무거나 지정 가능하나 arrayFilters 에 정의한 것과 동일하게 적기)

elem이 식별한 댓글의 투표값이 -5 이하면 comment 도큐먼트에 hidden 필드를 추가하고 값을 true 로 지정.

갱신 입력

특수한 형태를 갖는 갱신

갱신 조건에 맞는 도큐먼트가 존재하지 않을 때 쿼리 도큐먼트와 갱신 도큐먼트를 합쳐서 새로운 도큐먼트를 생성함.

조건에 맞는 도큐먼트가 발견되면 일반적인 갱신을 수행함.

갱신 입력은 컬렉션 내 시드 도큐먼트가 필여 없어 매우 편리, 같은 코드로 도큐먼트를 생성하고 갱신할 수 있음.


// 이 페이지에 대한 항목이 있는지 확인
blog = db.analytics.findOne({url: "/blog"});

// 항목이 있으면 조회 수 1 더하고 저장
if (blog) {
blog.pageviews++;
db.analytics.save(blog);
} else { // 항목 없으면 이 페이지에 대한 새로운 도큐먼트 생성
db.analytics.insertOne({url: "/blog", pageviews: 1});
}

// -----------------------------------------------------------

// 갱신 입력 처리 코드
// rep 값이 25인 도큐먼트를 찾아 3 증가
db.users.updateOne(
{"rep": 25},
{"$inc": {"rep": 3}},
{"upsert": true}
);
도큐먼트 생성 시 필드가 설정돼야 할 때, 이후 갱신에서는 변경되지 않아야 함. 이때, “$setOnInsert” 사용.


// 생성 시 필드 설정
db.users.updateOne({}, {"$setOnInsert": {"createdAt": new Date()}}, {"upsert": true});

// 재갱신 하면 기존 도큐먼트를 찾고 아무것도 입력되지 않으며, createdAt 필드는 변경되지 않음.
다중 도큐먼트 갱신

updateMany 사용.


db.users.updateMany({"birthday": "1993.05.22"}, {"$set": {"gift": "Happy Birthday!!"}});
갱신한 도큐먼트 반환

findOneAndUpdate

한번의 연산으로 항목을 반환하고 갱신함.

도큐멘트 상태를 수정하기 전에 정보를 반환함.

옵션 도큐먼트에 returnNewDocumentment 필드를 true 로 설정하면 갱신된 도큐먼트를 반환.

findOneAndReplace

findOneAndUpdate 와 동일한 매개변수 사용.

returnNewDocument의 값에 따라 교체 전 후 필터와 일치하는 도큐먼트 반환.

findOneAndDelete

삭제된 도큐먼트 반환.

findOneAndUpdate와 유사하나 매개변수로 사용하지 않으며, 두 메서드의 옵션을 부분적으로 가짐.