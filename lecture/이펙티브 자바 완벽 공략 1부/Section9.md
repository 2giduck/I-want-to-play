### Section 9. try-finally 보다 try-with-resources를 사용하라 
#### 핵심 정리
- try-finally는 더이상 최선의 방법이 아니다.
- try-with-resources를 사용하면 코드가 더 짧고 분명하다.
- 만들어지는 예외 정보도 훨씬 유용하다.

#### 완벽 공략
- closable 인터페이스를 구현할 때는 멱등성이 보장되어야 함