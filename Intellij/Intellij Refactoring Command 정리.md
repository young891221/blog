# Intellij Refactoring Command TOP 10
리펙토링 고자였던 저를 탈출시켜준 커맨드들을 소개합니다. 더 많은 커맨드들이 있지만 자주 쓰이는 기능들만 정리하였습니다. 
주로 커맨드의 장단점과 해당 기능이 실행할때의 before, after 위주로 써보았습니다. 물론 더 자세한 내용들은 Intellij 공식 문서에 잘 정리되어 있지만 한 번에 요약하여 보시기에 좋으실것 같습니다.
<br>
command 단축키는 window 기본, window eclipse, mac ox 기본 keymap을 기준으로 아래와 같은 포맷으로 정리하였습니다.
>window command: Intellij keymap(eclipse keymap)
<br>mac command: mac keymap

## 0. 추천 Refactoring command
개인적으로 모든 command 단축키를 외우기 힘들어 아래 두 가지 방법을 사용하고 있습니다. 선택은 개취(개인취향)로!
- 다음의 명령어를 통해 action의 이름을 찾아 실행하는 기능이 있습니다.
- 장점은 리펙토링 action뿐만 아니라 다양한 action을 손쉽게 찾아 실행시켜 줍니다.
- 단점은 해당 action의 이름을 알아야 합니다.
>window command: ctrl + shift + A(동일)
<br>mac command: 

- Refactoring this라는 action으로 리펙토링 기법들을 쫙 나열해 줍니다.
- 장점은 모든 Refactoring action들을 한 눈에 확인하며 0~9단축키를 통해 손쉽게 실행시킬 수 있습니다.
- 단점은 단축키를 4개나 눌러야 하는 점이...
- ***개인적으로 이 방법을 추천해 드립니다.***
>window command: ctrl + alt + shift + T(동일)
<br>mac command: ⌥⇧⌘T

## 1. extract method
리펙토링 기법의 제일 기본이 되는 기능입니다. 말 그대로 선택한 영역의 코드를 method로 빼주는 기능입니다.
>window command: ctrl + alt + M(shift + alt + M)
<br>mac command: ⌥⌘M

## 2. extract method object
extract method와 비슷하지만 선택된 영역을 새로운 class로 생성해 줍니다. 로컬 변수들을 필드 변수로 변경시키며 좀 큰 method 덩어리를 class로 변환하고 싶을 때 유용합니다.

## 3. field...
선택된 변수를 원하는 class의 field값으로 추출하며 선택된 표현식의 초기화 지정위치도 지정할 수 있습니다. 기존의 표현식들은 필드에 선언된 값으로 대체됩니다.
>window command: ctrl + alt + F(ctrl + alt + F)
<br>mac command: ⌥⌘F

## 4. variable...
선택된 변수를 공용 변수로 빼주는 action입니다. 
>window command: ctrl + alt + V(Shift + alt + L)
<br>mac command: ⌥⌘V

## 5. inline
variable...과는 반대로 선택된 변수의 공용 변수를 inline 방식으로 대체시켜 줍니다. 단순히 변수의 리펙토링 뿐만 아니라 method, constructor, superclass까지 리펙토링 기능을 지원합니다.
(보다 자세한 예제는 [여기](https://www.jetbrains.com/help/idea/2016.3/inline.html#inline_variable)를 참고하세요)
>window command: ctrl + alt + N(shift + alt + I)
<br>mac command: ⌥⌘N

## 6. parameter...
선택된 변수를 method의 paratemter값으로 받을 수 있게끔 추출해 준다.

