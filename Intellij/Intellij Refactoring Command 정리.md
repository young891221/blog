# Intellij Refactoring Command 정리
백명석님의 클린 코더스 강의를 들으며 정말 좋은 팁들을 알게되어서 정리하고자 마음 먹었습니다. Intellij에서 쓰기 좋은 리펙토링 커맨드를 소개합니다.

## 0. 추천 Refactoring command
개인적으로 모든 command를 외우기 힘들었는데 다음의 명령어를 통해 리펙토링이나 다른 action을 취하는 기능을 사용하고 있습니다.
>window command: ctrl + shift + A(동일)
>mac command: 

Refactoring this라고 하여 리펙토링 기법들을 한방에 나열해 줍니다. 선택은 개취로!
>window command: ctrl + alt + shift + T(동일)
>mac command: ⌥⇧⌘T

## 1. extract method
아마 대부분 알고 계시고 제일 리펙토링 기법의 기본이 되는 기능입니다. 말 그대로 선택한 영역의 코드를 method로 빼주는 기능입니다.
>window command: ctrl + alt + M(shift + alt + M)
>mac command: ⌥⌘M

## 2. extract method object
extract method와는 다르게 새로운 class를 생성해 줍니다. 로컬 변수들을 필드 변수로 변경시키며  
