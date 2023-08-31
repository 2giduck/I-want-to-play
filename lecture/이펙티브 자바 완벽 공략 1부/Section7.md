### Section 7. 다 쓴 객체 참조를 해제하라
#### 핵심 정리
- 어떤 객체에 대한 레퍼런스가 남아있다면 해당 객체는 가비지 컬렉션의 대상이 되지 않는다.
- 자기 메모리를 직접 관리하는 클래스라면 메모리 누수에 주의해야 한다. ex. 스택, 캐시 등
- 참조 객체를 null 처리하는 일은 예외적인 경우이며, 가장 좋은 방법은 유효 범위 밖으로 밀어내는 것이다.

#### 완벽 공략 
- NullPointerException
- WeakHashMap
- Executor

#### 완벽 공략 19. NullPointerException
- 메서드에서 null을 리턴하기 때문에 && null 체크를 하지 않았기 때문에
- 대안
  - 예외를 던짐
  - null을 리턴
  - Optional을 리턴함 

#### 완벽 공략 20. WeakHashMap
- Key가 더이상 강하게 레퍼런스되는 곳이 없다면 해당 엔트리를 제거
- 레퍼런스 종류
  - Strong, Soft, Weak, Phantom
- 맵의 엔트리를 맵의 Value가 아니라 Key에 의존해야 하는 경우에 사용 가능
- 캐시를 구현하는데 사용할 수 있지만, 캐시를 직접 구현하는 것은 권장하지 않음 

#### 완벽 공략 21. Executor 
- Thread, runnable, ExecutorService
- 쓰레드풀의 개수를 정할 때 주의 할 것
  - CPU, I/O
- 쓰레드툴 종류
  - Single, Fixed, Cached, Scheduled
- Runnable, Callable, Future
- CompletableFuture, ForkJoinPool