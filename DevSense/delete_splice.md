## Javascript 배열 삭제(delete, splice)에 대해 

: 개발을 진행하다가 겪은 하나의 이슈를 정리한다.<br>
이전 변경점과 현재 변경점이 같으면 pass, 다르면 거기에 맞는 쿼리를 수행하는 함수가 있다. compare(objA, objB)<br>
여기서 분명 배열을 잘 지워주고 객체에 대한 변경점이 없음에도 불구하고, 다르다고 되서 디버깅 결과는 다음과 같았다.<br>

### DELETE ARR[...]
arr = [0,1,2,3]; <br>
delete arr[0] 의 경우 : arr[empty, 1, 2, 3]으로 배열 값을 삭제하고 해당 인덱스를 empty로 채워 넣는다. <br>
즉, 인덱스 값 삭제, 배열 길이 유지

### ARR.splice(start Index, ~length)
arr = [0,1,2,3]; <br>
arr.splice(0,1) 의 경우 : arr[1,2,3]으로 원하는 바와 같이 기존 0번 인덱스의 값과 arr length까지 달라진다. <br>
즉, 인덱스 값 삭제, 배열 길이 변경


JavaScript에서 배열안의 원소를 완전히 제거하고 싶은 경우에 delete(empty)가 아닌, splice( )를 활용하면 된다!
<br>
<br>