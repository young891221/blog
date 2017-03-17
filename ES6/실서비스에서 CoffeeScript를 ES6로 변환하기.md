# 실서비스에서 CoffeeScript를 ES6로 변환하기

<p align="center">
<img src="/images/es6/coffee/coffee.png"/>
</p>

사내의 모바일웹 프로젝트의 프론트 환경이 CoffeeScript로 개발되어 있어 여러가지 불편을 겪었습니다. CoffeeScript의 개발환경 문제는 다음과 같았습니다.
- 전역객체 사용의 남발(CoffeeScript와는 무관합니다)
- 기존 코드가 모듈화 되어 있지 않았습니다.(CoffeeScript와는 무관합니다)
- 그래서 의존 관계 파악이 어려웠고 유지보수, 기능 개발에 제한이 있었습니다.
- 개발 진행중 수정된 사항을 즉시 변환해 주는 컨버팅 도구(Webpack, browsify)를 쓰고 있지 않아 개발진행이 느렸습니다.
- CoffeeScript의 장점이 있지만 러닝커브가 존재하였습니다. 그런데 학습비용을 쓰기에는...
- 그래서 이 프로젝트를 맡게되는 사람마다 어려움을 겪게 되었습니다.
- 무엇보다 커피의 카페인은 몸에 해롭습니다...하하핳
<p align="center">
<img src="/images/es6/coffee/trend.png"/>
</p>
`사장 위기의 CoffeeScript`

이러한 단점들 때문에 트렌디한 Webpack + ES6를 도입하기로 하였습니다. 물론 이유도 있습니다.
- ES6가 대세이기 때문입니다.
- Webpack과 ES6는 찰떡궁합의 호흡을 자랑하며 개발 진행중에도 디버깅이 편리합니다.
- ES6의 모토중 하나가 ***다른 언어의 개발자들도 이해하기 쉽게 구성하는 것이기에 러닝커브가 상대적으로 줄어듭니다.***
- Webpack의 ***Dependency Tree개념을 사용하여 의존관계를 형성***할 수 있습니다.
- 기존의 코드를 모듈화하여 읽기 편한 ***직관적인 코드***로 표현할 수 있습니다.
- 향후 React나 다른 프레임워크를 도입하기 수월합니다.

## 도입 절차
1)각각의 스크립트 구조와 역할에 대해 분석
2)의존관계를 형성하여 모듈화된 구조 설계
3)Webpack 도입을 위한 스터디 진행 및 기존 프로젝트에 필요한 Webpack의 기능 분석
4)CoffeeScript를 ES6로 변환(decaffeenate를 사용하면 짱 편합니다)
5)변환된 스크립트 아름답게 구성(convention, modulization)
6)Router 도입
7)의존성 연결 및 전역객체 삭제

### 1. 기존 스크립트 설계하기
사실 모바일웹 프로젝트의 프론트 부분을 개발한 적이 거의 없어 해당 스크립트들이 어떠한 기능을 하는지 부터 파악해야 했습니다.
 
<p align="center">
<img src="/images/es6/coffee/explain-feature.png"/>
</p>

각각의 주요 기능들을 파악한 이후 스크립트들의 구조를 파악하였습니다. 공통되는 모듈과 카테고리별 주요 스크립트를 정리하였습니다. 

<p align="center">
<img src="/images/es6/coffee/neo-structure.png"/>
</p>
 
마지막으로 스크립트들의 의존관계를 정의하였습니다.

<p align="center">
<img src="/images/es6/coffee/neo-dependency.png"/>
</p>

> 이를 통해 설계된 부분은 4번 카테고리에서 셜명 하겠습니다.

### 2. Webpack 도입하기
Webpack에 대한 개념과 기능들은 개인적인 학습을 통해 도입하였습니다. 자세한 내용은 [개인 블로그](http://haviyj.tistory.com/17)에 정리해 두었습니다.(도입 이후 3일만에 Webpack2가 나와 많이 당황스러웠던 기억이...)

### 3. CoffeeScript 걷어내기
사내 동료 직원에게 좋은 OpenSource를 추천받았습니다. 이름부터 필이 팍팍 오는 decaffeinate! 몸에 해로운 카페인을 제거하자는 느낌이 물씬나며 저에게 격공되는 멘트를 메인에 달고 있습니다.

<p align="center">
<img src="/images/es6/coffee/decaffeinate-comment.png"/>
</p>

[decaffeinate](https://www.npmjs.com/package/decaffeinate)를 사용하면 간편하게 CoffeeScript를 ES6로 변환해 줍니다. 기대했던 것보다 퀄러티 좋게 변환이 되어 놀랐습니다!(물론 완벽하진 않습니다...)

<p align="center">
<img src="/images/es6/coffee/before-code.png"/>
</p>

`변환되기 전 코드`

<p align="center">
<img src="/images/es6/coffee/sample-code.png"/>
</p>

`일반적으로 변환된 샘플 코드`

- 일단 바로 쓸 수 있는 코드로 변환되어 지지는 않습니다.
- 변수의 선언은 const, let으로 이루어 집니다.
- 규격에 어느정도 맞으면 class로 변환이 되지만 안되는 경우가 훨씬 많습니다.
- for...of, Array함수 등 ES6의 함수들로 변환이 이루어 졌습니다.

### 4. ES6 아름답게 구성하기
여기서부터 가장 오랜 시간 공을 들였습니다. 미흡하게 변환되어진 코드를 모듈화하고 각각의 알맞는 함수를 사용해 주어야 합니다. 디렉토리 구조는 다음과 같습니다.

<p align="center">
<img src="/images/es6/coffee/before-directory.png"/>
</p>

`변환되기 이전 디렉토리 구조`

<p align="center">
<img src="/images/es6/coffee/after-directory.png"/>
</p>

`변환되기 이후 디렉토리 구조`

ES6로 변환하면서 문제가 되는 점들을 적어보았습니다.
- 문제: mobile에서는 ES6 기능들이 다 될거라는 막연한 생각이였습니다. 하지만 대부분 지원되지 않는게 현실이었다는...(사실 크롬에서만 테스트를 수행하여 마지막에 IE11에서 다시 테스트를 해야 했습니다)
<p align="center">
<img src="/images/es6/coffee/mobile-es6.png"/>
</p>
- 해결: Array.from, includes, generator, promise...기타 등 지원 안되는 ES6 함수들 제거(대부분 IE11 대상으로 테스트 하시면 편리합니다)

- 문제: 테스트 코드가 없어 공격적인 리펙토링이 어렵다.
	- 해결: 해결하지 못하였습니다. 변명일 수도 있지만 모든 테스트 코드를 작성하는 것이 시간적으로 제약이 있었고 처음 시작부터 작성되지 않으면 추후의 반영은 힘들다고 생각합니다.

