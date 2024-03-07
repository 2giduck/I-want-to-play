# Ch8. 애그리거트 트랜잭션 관리

## 8.1 애그리거트와 트랜잭션

---

![20240306200512.png](Ch8%20%E1%84%8B%E1%85%A2%E1%84%80%E1%85%B3%E1%84%85%E1%85%B5%E1%84%80%E1%85%A5%E1%84%90%E1%85%B3%20%E1%84%90%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8C%E1%85%A2%E1%86%A8%E1%84%89%E1%85%A7%E1%86%AB%20%E1%84%80%E1%85%AA%E1%86%AB%E1%84%85%E1%85%B5%207594db4849ed46d0b69b62f2bb226487/20240306200512.png)

- 주문 애그리거트에 대해 다음과 같이 변경한다면 어떻게 될까
    - 운영자 → 배송 상태로 변경
    - 고객 → 배송지 변경
- 운영자 스레드와 고객 스레드는 개념적으로는 동일한 애그리거트지만 물리적으로 서로 다른 애그리거트 객체 사용
    
    ![20240306200845.png](Ch8%20%E1%84%8B%E1%85%A2%E1%84%80%E1%85%B3%E1%84%85%E1%85%B5%E1%84%80%E1%85%A5%E1%84%90%E1%85%B3%20%E1%84%90%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8C%E1%85%A2%E1%86%A8%E1%84%89%E1%85%A7%E1%86%AB%20%E1%84%80%E1%85%AA%E1%86%AB%E1%84%85%E1%85%B5%207594db4849ed46d0b69b62f2bb226487/20240306200845.png)
    
- 애그리거트를 위한 추가적인 트랜잭션 처리 기법이 필요함
    - 선점 잠금(비관적 잠금)
    - 비선점 잠금(낙관적 잠금)

## 8.2 선점 잠금

---

- 선점 잠금: 애그리거트를 구한 스레드가 애그리거트 사용이 끝날 때까지 다른 스레드가 해당 애그리거트를 수정하지 못하게 막는 방식
    
    ![20240306201154.png](Ch8%20%E1%84%8B%E1%85%A2%E1%84%80%E1%85%B3%E1%84%85%E1%85%B5%E1%84%80%E1%85%A5%E1%84%90%E1%85%B3%20%E1%84%90%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8C%E1%85%A2%E1%86%A8%E1%84%89%E1%85%A7%E1%86%AB%20%E1%84%80%E1%85%AA%E1%86%AB%E1%84%85%E1%85%B5%207594db4849ed46d0b69b62f2bb226487/20240306201154.png)
    
- 선점 잠금은 보통 DBMS가 제공하는 행단위 잠금을 사용해서 구현함
    - 다수의 DBMS가 for update와 같은 쿼리를 사용해 특정 레코드에 한 커넥션만 접근할 수 있는 잠금장치를 제공

```json
Order order = entityManager.find(
		Order.class, orderNo, LockModeType.PESSIMISTIC_WRITE)
//JPA EntityManager는 해당 엔티티와 매핑된 테이블을 이용해 선점 잠금 방식 적용
```

```json
public interface MemberRepository extends Repository<Member, MemberId> {
	
	@Lock(LockModeType.PESSIMISTIC_WRITE)
	@Query("select m from Member m where m:id = :id")
	Optional<Member> findByIdForUpdate(@Param("id") MemberId memberId);
	//스프링 데이터 JPA는 @Lock 애너테이션을 사용해 잠금 모드를 지정 
}
```

### 8.2.1 선점 잠금과 교착 상태

- 잠금 순서에 따른 교착 상태(deadlock)가 발생하지 않도록 주의해야 함
    
    ![20240306201734.png](Ch8%20%E1%84%8B%E1%85%A2%E1%84%80%E1%85%B3%E1%84%85%E1%85%B5%E1%84%80%E1%85%A5%E1%84%90%E1%85%B3%20%E1%84%90%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8C%E1%85%A2%E1%86%A8%E1%84%89%E1%85%A7%E1%86%AB%20%E1%84%80%E1%85%AA%E1%86%AB%E1%84%85%E1%85%B5%207594db4849ed46d0b69b62f2bb226487/20240306201734.png)
    
- 이런 문제가 발생하지 않도록 하려면 잠금을ㅇㅈ 구할 때 최대 대기 시간을 지정해야 함
    - ‘javax.persistence.lock.timeout’ 힌트는 잠금을 구하는 대기 시간을 밀리초 단위로 지정
    - 이 시간 이내에 잠금을 구하지 못하면 exception 발생
    - DBMS에 따라 힌트가 적용되지 않을 수 있으니 관련 기능을 지원하는지 확인해야 함

```json
public interface MemberRepository extends Repository<Member, MemberId> {
	
	@Lock(LockModeType.PESSIMISTIC_WRITE)
	@QueryHints({
			@QueryHint(name = "javax.persistence.lock.timeout", value = "2000")
	})
	@Query("select m from Member m where m:id = :id")
	Optional<Member> findByIdForUpdate(@Param("id") MemberId memberId);
}
```

## 8.3 비선점 잠금

---

![20240307200215.png](Ch8%20%E1%84%8B%E1%85%A2%E1%84%80%E1%85%B3%E1%84%85%E1%85%B5%E1%84%80%E1%85%A5%E1%84%90%E1%85%B3%20%E1%84%90%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8C%E1%85%A2%E1%86%A8%E1%84%89%E1%85%A7%E1%86%AB%20%E1%84%80%E1%85%AA%E1%86%AB%E1%84%85%E1%85%B5%207594db4849ed46d0b69b62f2bb226487/20240307200215.png)

- 비선점 잠금 : 동시에 접근하는 것을 막는 대신 변경한 데이터를 실제 DBMS에 반영하는 시점에 변경 가능 여부를 확인하는 방식
    - 애그리거트에 버전으로 사용할 숫자 타입 프로퍼티를 추가해야 함
    
    ```json
    UPDATE aggtable SET version = version + 1, colx = ?, coly = ?
    WHERE aggid = ? and version = 현재버전
    // 수정할 애그리거트와 매핑되는 테이블의 버전 값이 
    // 현재 애그리거트의 버전과 동일한 경우에만 데이터 수정 
    ```
    
    ![20240307200404.png](Ch8%20%E1%84%8B%E1%85%A2%E1%84%80%E1%85%B3%E1%84%85%E1%85%B5%E1%84%80%E1%85%A5%E1%84%90%E1%85%B3%20%E1%84%90%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8C%E1%85%A2%E1%86%A8%E1%84%89%E1%85%A7%E1%86%AB%20%E1%84%80%E1%85%AA%E1%86%AB%E1%84%85%E1%85%B5%207594db4849ed46d0b69b62f2bb226487/20240307200404.png)
    
- JPA는 버전을 이용한 비선점 잠금 기능을 지원함

```json
@Entity
public class Order {
	@EmbeddedId
	private OrderNo number;

	@Version
	private long version;
}
```

```json
public class ChangeShippingService {
	
	@Transactional
	public void changeShipping(ChangeShippingRequest changeReq) {
		Order order = orderRepository.findById(new OrderNo(changeReq.getNumber()));
		checkNoOrder(order);
		order.changeShippingInfo(changeReq.getShippingInfo()); // OptimisticLockingFailureException 발생!
	}
}
```

![20240307210500.png](Ch8%20%E1%84%8B%E1%85%A2%E1%84%80%E1%85%B3%E1%84%85%E1%85%B5%E1%84%80%E1%85%A5%E1%84%90%E1%85%B3%20%E1%84%90%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8C%E1%85%A2%E1%86%A8%E1%84%89%E1%85%A7%E1%86%AB%20%E1%84%80%E1%85%AA%E1%86%AB%E1%84%85%E1%85%B5%207594db4849ed46d0b69b62f2bb226487/20240307210500.png)

- HTML 폼에 버전값 hidden 타입 태그를 생성해서 폼 전송 시 버전 값이 서버에 함께 전달되도록 함

```json
public class ChangeShippingService {
	
	@Transactional
	public void startShipping(StartShippingRequest req) {
		Order order = orderRepository.findById(new OrderNo(req.getOrderNumber()));
		checkNoOrder(order);
		if(!order.matchVersion(req.getVersion)) { 
			throw new VersionConflictException();
		}
		order.startShipping();
	}
}
```

- 표현 계층은 버전 충돌 익셉션이 발생하면 버전 충돌을 사용자에게 알려 알맞은 후속 처리를 할 수 있게 함

```json
@Controller
public class OrderAdminController {

	@PostMapping("/startShipping")
	public String startShipping(StartShippingRequest startReq) {
		try {
			startShippingService.startShipping(startReq);
			return "shippingStarted";
		} catch(OptimisticLockingFailureException | VersionConflictException ex) {
			return "startShippingTxConflict";
			// VersionConflictException : 누군가가 이미 애그리거트 수정
			// OptimisticLockingFailureException: 거의 동시에 애그리거트 수정
		}
	}
}
```

### 8.3.1 강제 버전 증가

- 애그리거트에 애그리거트 루트 외에 다른 엔티티가 존재하는데 기능 실행 도중 루트가 아닌 다른 엔티티의 값이 변경될 경우
    - JPA는 루트 엔티티의 버전 값을 증가시키지 않음
- 비록 루트 엔티티의 값이 바뀌지 않더라도 애그리거트 구성요소 중 일부 값이 바뀌면 논리적으로 그 애그리거트는 바뀐 것임
    - 루트 애그리거트의 버전 값이 증가해야 비선점 잠금이 올바르게 작동
- JPA는 EntityManager#find()메서드로 엔티티를 구할 때 강제로 버전 값을 증가시키는 잠금 모드를 지원함

```json
public Order findByIdOptimisticLockMode(OrderNo id) {
		return entityManager.find(
				Order.class, id, LockModeType.OPTIMISTIC_FORCE_INCREMENT);
				// 해당 엔티티의 상태가 변경되었는지에 상관없이 트랜잭션 종료 시점에 버전 값 증가 처리 
		}
}
```

## 8.4 오프라인 선점 잠금

---

- 컨플루언스는 사전 충돌 여부를 알려주지만 동시에 수정하는 것을 막지는 않음
- 오프라인 선점 잠금 방식을 사용함
    - 여러 트랜잭션에 걸쳐 동시 변경을 막음
    - 첫 번째 트랜잭션을 시작할 때 오프라인 잠금 선정, 마지막 트랜잭션에서 잠금 해제

![20240307211502.png](Ch8%20%E1%84%8B%E1%85%A2%E1%84%80%E1%85%B3%E1%84%85%E1%85%B5%E1%84%80%E1%85%A5%E1%84%90%E1%85%B3%20%E1%84%90%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8C%E1%85%A2%E1%86%A8%E1%84%89%E1%85%A7%E1%86%AB%20%E1%84%80%E1%85%AA%E1%86%AB%E1%84%85%E1%85%B5%207594db4849ed46d0b69b62f2bb226487/20240307211502.png)

- 사용자 A가 과정 3에서 프로그램 종료할 경우 잠금 해제가 되지 않으므로 다른 사용자가 영원히 잠금을 구할 수 없음
    - 오프라인 선점 방식은 잠금 유효 시간을 가져야 함
    - 일정 주기로 유효 시간을 증가시키는 방식이 필요함

### 8.4.1 오프라인 선점 잠금을 위한 LockManager 인터페이스와 관련 클래스

- 오프라인 선점 잠금 기능
    - 잠금 선점 시도
    - 잠금 확인
    - 잠금 해제
    - 잠금 유효시간 연장

```json
public interface LockManager {
		// 잠글 대상 타입과 식별자 
		LockId tryLock(String type, String id) throws LockException;
		
		void checkLock(LockId lockId) throws LockException;

		void releaseLock(LockId lockId) throws LockException;

		void extendLockExpiration(LockId lockId, long inc) throws LockException;
}
```

### 8.4.2 DB를 이용한 LockManager 구현

```json
create table locks (
	'type' varchar(255),
	id varchar(255),
	lockid varchar(255),
	expiration_time datetime,
	primary key ('type', id)
) character set ut8;

create unique index locks_idx ON locks(lockid);
```

- 코드…