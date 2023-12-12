### 아이템 14. Comparable을 구현할지 고민하라

- Object.equals에 더해서 순서까지 비교할 수 있으며 Generic을 지원한다.
- 자기 자신이 compareTo에 전달된 객체보다 작으면 음수, 같으면 0, 크면 양수 리턴
- 반사성, 대칭성, 추이성 만족
- x.compareTo(y)== 0이라면 x.equals(y)가 true여야 함
