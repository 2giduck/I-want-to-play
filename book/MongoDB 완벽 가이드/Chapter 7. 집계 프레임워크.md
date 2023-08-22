#### Chapter 7. 집계 프레임워크 

집계 프레임워크

집계 단계

집계 표현식

집계 누산기

7.1 파이프라인, 단계 및 조정 가능 항목
집계 프레임워크: 몽고DB 내 분석 도구 모음

하나 이상의 컬렉션에 있는 도큐먼트에 대한 분석을 수행하게 해줌

집계 프레임워크는 파이프라인 개념을 기반으로 함

집계 파이프라인을 통해 몽고 DB 컬렉션에서 입력을 받고, 컬렉션에서 나온 도큐먼트를 하나 이상의 단계를 거쳐 전달함

단계마다 해당 입력에 다른 작업을 수행함

각 단계는 전 단계에서 생산한 출력이 무엇이든 입력으로 받아들임


집계 파이프라인의 개별 단계는 데이터 처리 단위(data processing unit)

한 번에 입력 도큐먼트 스트림을 하나씩 가져와서, 각 도큐먼트를 하나씩 처리하고, 출력 도큐먼트 스트림을 하나씩 생성함




각 단계는 knobs 또는 tunables 셋을 제공함

이 항목들을 조정해 각 단계를 매개변수로 지정함으로써 원하는 작업을 수행할 수 있음

tunables는 일반적으로 필드 수정, 산술 연산 수행, 도큐먼트 재구성 등 여러 작업을 수행하는 연산자

7.2 단계 시작하기: 익숙한 작업들
일치(match), 선출(projection), 정렬(sort), 건너뛰기(skip), 제한(limit)


> db.companies.aggregate([{$match: {founded_year: 2004}}])
> db.companies.find({founded_year: 2004})
// 2004년에 설립된 회사 모두 찾기 (일치 단계)

> db.companies.aggreagate([
{$match: {founded_year: 2004}},
{$project: {
_id: 0,
name: 1,
founded_year:1
}}
])
// _id필드는 제외, name과 founded_year는 포함 (선출 단계)
aggreagate는 집계 쿼리를 실행할 때 호출하는 메서드

일치 단계는 컬렉션에 대해 필터링하고 결과 도큐먼트를 한 번에 하나씩 선출 단계로 전달함

선출 단계는 작업을 수행하고 도큐먼트 모양을 변경한 후 출력을 파이프라인에서 다시 우리에게 전달

파이프라인을 구축할 때 한 단계에서 다른 단계로 전달해야 하는 도큐먼트의 수를 반드시 제한하자


> db.comapnies.aggregate([
{$match: {founded_year: 2004}},
{$sort: {name: 1}}, // name 기준으로 오름차순 정렬  
{$skip: 10},  // 처음 10개의 도큐먼트 건너 뜀
{$limit: 5}, // 결과 셋을 5개 도큐먼트로 제한함
{$project: {
_id: 0,
name: 1}},
])
7.3 표현식
불리언 표현식

AND, OR, NOT

집합 표현식

배열을 집합으로 사용할 수 있음

ex. 2개 이상의 집합의 교집합이나 합집합을 얻을 수 있음

비교 표현식

다양한 유형의 범위 필터(range filter)를 표현할 수 있음

산술 표현식

곱하기, 나누기, 더하기, 빼기

천장(ceiling), 바닥(floor), 자연 로그, 로그 계산

문자열 표현식

연결(concat),하위 문자열(substring) 검색, 대소문자 및 텍스트 검색

배열 표현식

배열 요소를 필터링, 배열 분할, 특정 배열에서 값의 범위 가져오기

가변적 표현식

리터럴(literal), 날짜 값 구문 분석을 위한 식, 조건식 사용

누산기

합계, 기술 통계 및 기타 여러 유형의 값을 계산하는 기능

7.4 $project
중첩 필드를 승격하기

$ 문자는 선출 단계에서 ipo, valuation, funders에 대한 값을 지정하는 데 사용됨


> db.companies.aggregate([
{$match: {"funding_rounds.investments.financial_org.permalink": "greylock"}},
{$project: {
_id: 0,
name: 1,
ipo: "$ipo.pub_year",
valuation: "$ipo.valuation_amount",
funders: "$funding_rounds.investments.financial_org.permalink"
}}
]).pretty()


// 결과
{
"name": "Facebook",
"ipo": 2012,
"valuation": NumberLong("10400000000"),
"funders": [
[
"accel-partners"
],...
]
}
7.5 $unwind
전개(unwind) 단계를 통해 지정된 배열 필드의 각 요소에 대해 출력 도큐먼트가 하나씩 있는 출력을 생성할 수 있음


즉, 배열에 10개의 요소가 있으면 전개 단계에서는 10개의 출력 도큐먼트가 생성됨

> db.companies.aggregate([
{ $match: {"funding_rounds.investments.financial_org.permalink": "greylock"}},
{ $unwind: "$funding_rounds"},
{ $project: {
_id: 0,
name: 1,
amount: "$funding_rounds.raised_amount",
year: "$funding_rounds.funded_year"
}}
])
7.6 배열 표현식
필터 표현식

필터 기준에 따라 배열에서 요소의 부분집합을 선택함


> db.companies.aggregate([
{ $match: {"funding_rounds.investments.financial_org.permalink": "greylock"}},
{ $project: {
_id: 0,
name: 1,
founded_year: 1,
rounds: { $ filter: {
input: "$funding_rounds", // funding_rounds 배열을
as: "round", // round라고 이름 붙임
cond: { $gte: {"$$round.raised_amount", 100000000}}
// funding_rounds의 raised_amount가 1억 달러 이상인 요소만 선택
}}
}},
{ $match: {"rounds.investments.financial_org.permalink": "greylock"}}
])
// round
$$는 작업 중인 표현식 내에서 정의된 변수를 참조하는 데 사용함

as 절은 필터 표현식 내에서 변수를 정의함

배열 요소 연산자


> db.companies.aggregate([
{$match: { "founded_year": 2010}},
{$project: {
_id: 0,
name: 1,
founded_year: 1,
first_round: { $arrayElemAt: ["$funding_rounds", 0]}, // funding_rounds의 첫 번째 요소
last_round: { $arrayElemAt: [ "$funding_rounds", -1]} // funding_rounds의 마지막 요소
}}
]).pretty()
slice 표현식을 사용하면 여러 항목을 순서대로 반환할 수 있음


> db.companies.aggregate([
{$match: { "founded_year": 2010}},
{$project: {
_id: 0,
name: 1,
founded_year: 1,
early_rounds: { $slice: [ "$funding_rounds", 1, 3]} // 인덱스 1부터 3개의 요소를 가져옴
}}
]).pretty()
$size 표현식은 선출 단계에서 사용될 때 배열 요소 개수의 값을 제공함


> db.companies.aggregate([
{$match: { "founded_year": 2010}},
{$project: {
_id: 0,
name: 1,
founded_year: 1,
total_rounds: {$size: "$funding_rounds"}
}}
]).pretty()
7.7 누산기
누산기를 사용하면 특정 필드의 모든 값 합산($sum), 평균 계산($avg) 등의 작업을 할 수 있음

$mergeObjects를 사용하면 여러 도큐먼트를 하나의 도큐먼트로 결합할 수 있음

$push를 사용하여 배열에 값을 푸쉬할 수 있음

$addToSet은 $push와 매우 유사하지만, 결과 배열에 중복 값이 포함되지 않게 함

몽고DB 3.2 이전에는 누산기를 그룹 단계에서만 사용할 수 있었지만, 이후에는 선출 단계에서 누산기의 서브셋에 접근하는 기능을 도입

7.7.1 선출 단계에서 누산기 사용

> db.comapnies.aggregate([
{$match: { "funding_rounds": {$exists: true, $ne: []}}},
//“funding_rounds” 필드를 포함하고 funding_rounds 배열이 비어있지 않은 도큐먼트
{$project: {
_id: 0,
name: 1,
largest_round: {$max: "$funding_rounds.raised_amount"}
//funding_rounds 배열의 raised_amount 최댓값
}}
])
7.8 그룹화 소개
그룹 단계는 SQL GROUP BY 명령과 유사한 기능을 수행함


> db.companies.aggregate([
{ $group: {
_id: {founded_year: "$founded_year"}, // 설립 연도 기준으로 모든 회사 합침
average_number_of_employees: { $avg: "$number_of_employees"} // 연도마다 평균 직원 수 계산
}},
{ $sort: {average_number_of_employees: -1}} // 평균 직원 수 기준으로 내림차순 정렬
])

> db.companies.aggregate([
{ $match: {"relationships.person": {$ne: null}}}, // person이 null이 아닌 값 필터링
{ $project: {relationships: 1, _id: 0}},
{ $unwind: "$relationships"},
{ $group: {
_id: "$relationships.person", // 동일한 person 값을 갖는 모든 도큐먼트를 그룹화
count: { $sum: 1} // 각 사람이 맺은 관계 수 계산
}},
{ $sort: {count: -1}} // 관계 수 기준으로 내림차순 정렬
]).pretty()
7.9 집계 파이프라인 결과를 컬렉션에 쓰기
집계 파이프라인에서 생성된 도큐먼트를 컬렉션에 쓸 수 있는 두 가지 단계

$out

동일한 데이터베이스에만 쓸 수 있음

기존 컬렉션이 있으면 덮어쓰며, 샤딩된 컬렉션에는 쓸 수 없음

$merge

샤딩 여부에 관계 없이 모든 데이터베이스와 컬렉션에 쓸 수 있음

진정한 장점은 파이프라인 실행 시 출력 컬렉션의 내용이 점진적으로 갱신되는 주문식(on-demand)의 구체화된 뷰를 생성할 수 있다는 점

두 단계 중 하나만 사용할 수 있으며, 이는 집계 파이프라인의 마지막 단계여야함 