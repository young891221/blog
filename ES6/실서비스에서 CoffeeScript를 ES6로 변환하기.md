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
- 무엇보다 커피의 카페인은 몸에 해롭습니다...!!
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
<p align="center">
<code>변환되기 전 샘플 코드(Coffee -> JS)</code>
</p>

<p align="center">
<img src="/images/es6/coffee/sample-code.png"/>
</p>
<p align="center">
<code>일반적으로 변환된 샘플 코드(아직은 코드가 좀 더럽습니다..)</code>
</p>

- 일단 바로 쓸 수 있는 코드로 변환되어 지지는 않습니다.(다듬는 과정이 필요합니다)
- 변수의 선언은 const, let으로 이루어 집니다.(decaffeinate시 `--prefer-const`를 옵션으로 줍니다)
- 규격에 어느정도 맞으면 class로 변환이 되지만 안되는 경우가 훨씬 많습니다.
- for...of, Array함수 등 ES6의 함수들로 변환이 이루어 졌습니다.

### 4. ES6 아름답게 구성하기
여기서부터 가장 오랜 시간 공을 들였습니다. 제일 먼저 디렉토리 구조를 각각 의미에 맞는 구조로 정리하였습니다. 또한 미흡하게 변환되어진 코드를 모듈화하고 누구나 한눈에 볼 수 있도록 정형화된 구조로 변형하도록 노력하였습니다.

<p align="center">
<img src="/images/es6/coffee/before-directory.png"/>
<img src="/images/es6/coffee/after-directory.png"/>
</p>
<p align="center">
<code>변환되기 이전/이후 디렉토리 구조</code>
</p>

<p align="center">
<img src="/images/es6/coffee/code1.png"/>
</p>
<p align="center">
<code>정리 후 심플해진 샘플 코드</code>
</p>

### 5. ES6 코드 변환 이슈
문제는 mobile에서 ES6 기능들이 다 될것이라는 막연한 추측이였습니다. 하지만 대부분 지원되지 않는게 현실이었다는...[이곳](https://kangax.github.io/compat-table/es6/)에서 확인 가능합니다.(모바일웹은 꼭 IE에서 테스트를 하세요)

<p align="center">
<img src="/images/es6/coffee/mobile-es6.png"/>
</p>

>해결Tip: Array.from, includes, generator, promise...기타 등 지원 안되는 ES6 함수들 제거(대부분 IE11 대상으로 테스트 하시면 편리합니다)<br>
나중에 안 사실이지만 decaffeinate의 option 중 '--loose-for-expressions', '--loose-for-of', '--loose-includes' 등을 부여해 주면 됩니다.(나중에 찾은 슬픈 현실...ㅠㅠ) 더 자세한 옵션은 decaffeinate docs를 참고하세요.

`나머지 정리 포인트`
- set 메서드는 정말 필요한 경우가 아니면 제외했습니다. 객체 인스턴스화시 초기화되어 필요없기 때문입니다. 
- ES6 class 사용시 private 변수 선언이 힘듭니다.
	- 대안은 여러가지가 있지만 [장단점](http://webcache.googleusercontent.com/search?q=cache:NeFjoJ7ey4wJ:www.2ality.com/2016/01/private-data-classes.html+&cd=1&hl=ko&ct=clnk&gl=kr)이 있습니다.
	- Object.assign을 사용하면 constructor 안에서 선언할 수 있지만 메소드에 인스턴스를 직접 할당하므로 각 인스턴스들의 독립성이 보장되지 않아 class 사용의미가 없어집니다.
	- Symbols, WeakMap 등을 사용하면 어느정도 해결되는 것 같지만 class 구조가 ugly해 지고 다른 스크립트에서 name이 같은 코드끼리 충돌이 생깁니다. 하지만 무엇보다 모바일웹 환경에서 해당 객체들은 지원되지 않습니다.
	- 저는 깔끔한 코드스타일을 선호하기에 [stackoberflow](http://stackoverflow.com/questions/22156326/private-properties-in-javascript-es6-classes)에서 찾은 naming rule을 지정방법을 선택하였습니다.(underscore를 private 구분자로 정하여 사용하였습니다.)
- 이미 남발되어 있는 전역객체들이 너무 많았습니다. 충격적인 것은 window 객체에 지정하여 전역으로 사용되어 진다는 것이...decaffeinate의 잘못이 아니라 코드자체가 문제였습니다.
	- 공통 object를 관리하는 commonObject를 생성하여 전역으로 사용되어 지는 객체들을 관리하게끔 하였습니다.
- Backbone같은 프레임워크를 사용하는 것이 편리하겠지만 ES6가 AMD(Asynchronous Module Design)방식이라는 점과 규모가 방대한 환경이 아니라는 점 때문에 제외시켰습니다. 즉, 스크립트 모듈화만...
- 스크립트 별 실행 여부를 결정짓는 url 체크 정규표현식들이 사용되어 코드가 지저분하였습니다. 해당 이슈는 다음 카테고리에서 해결됩니다.

### 5. Router 도입
각 스크립트에서 아래 코드처럼 상단에 정규식을 넣어 url에 따라 코드의 실행여부를 결정지었습니다. 때문에 Router의 도입은 필수적이였습니다.

<p align="center">
<img src="/images/es6/coffee/before-router.png"/>
</p>
<p align="center">
<code>스크립트의 존재여부를 결정짓는 정규식</code>
</p>

React-Router같은 라이브러리를 쓰고 싶었지만...React까지 도입하기에는 여유가 없었기에 모듈화된 Router를 만들었습니다. 내부 로직을 몰라도 필요한 메서드만 호출하여 사용하게끔 구성하였습니다.
<p align="center">
<img src="/images/es6/coffee/after-router.png"/>
<img src="/images/es6/coffee/entry.png"/>
</p>
<p align="center">
<code>모듈화된 Router와 entry point에서 각각의 스크립트 실행여부를 결정짓는 코드</code>
</p>



### 후기
아쉬운 점이 많습니다. 로직의 세부적인 개선을 하지 못하였습니다. 이유는 테스트 코드가 없다는 것. 그래서 공격적인 리펙토링이 어려웠습니다. 대부분 그러하시겠지만 테스트 코드가 없으면 처음에 개발하기에 편할지 몰라도 이후의 
유지보수 관점에서 절약한 시간의 배로 시간이 더 걸리는 것 같습니다. 무엇보다 안정적인 코드를 구성할 수 없다는 것도 문제인것 같습니다. 그래도 하나에 통합되거나 스크립트 네임과는 달리 얽히고 섥혀있던 의존성들을 제거한 것에 
큰 위안을 얻습니다. Coffee를 모르는 개발자가 오더라도 더 빨리 생산성을 낳을 수 있다는 것이 이번 변환작업의 가장 큰 의미인것 같습니다. 긴 글 읽어주셔서 감사합니다.