### Section 8. finalizer와 cleaner 사용을 피하라 
#### 핵심 정리
- finalizer와 cleaner는 즉시 수행된다는 보장이 없음
- finalizer와 cleaner는 실행되지 않을 수도 있음
- finalizer 동작 중에 예외가 발생하면 정리 작업이 처리되지 않을 수 있음
- finalizer와 cleaner는 심각한 성능 문제가 있음
- finalizer는 보안 문제가 있음
- 반납할 자원이 있는 클래스는 AutoCloseable을 구현하고 close()를 호출하거나 try-with-resources를 사용

#### 완벽 공략 22. Finalizer 공격
- finalzer
  - 방어
    - final 클래스로 만들거나
    - finalizer() 메서드 오버라이딩 후 final을 붙여서 하위 클래스에서 오버라이딩 될 수 없도록 만듦

#### 완벽 공략 23. AutoClosable
- 예외를 꼭 던져야 한다면 구체적인 예외로 던지기 
