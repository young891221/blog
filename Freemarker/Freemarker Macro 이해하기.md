# Freemarker Macro 이해하기
서버 템플릿 엔진으로 Freemarker를 많이 사용해 왔지만 궁금했던 함수들은 그때그때 [Freemarker docs](http://freemarker.org/docs/)에서 확인해 보고 따로 정리는 하지 않았었습니다. 
모든 것을 다 정리할 필요는 없을것 같고 그동안 해깔려했던 macro 함수를 간단한 예제를 통해 정리해 보았습니다. 모든 소스는 [github](https://github.com/young891221/Freemaker-Macro)에 있습니다.

## 1. macro란?
macro는 반복되는 구문에 대해 틀(템플릿?)을 미리 지정해 놓고 반복하여 사용할 수 있게 해주는 함수입니다. 파라미터를 넣을수도 있고 nested, return 함수를 사용하여 유연하게 응용하여 사용할 수 있습니다. 
또한, macro는 선언 위치에 상관없이 사용가능 합니다.(마치 자바스크립트의 함수 호이스팅같이?) 기본 구조는 다음과 같습니다.
```html
<#macro name param1 param2 ... paramN>
  ...
  <#nested loopvar1, loopvar2, ..., loopvarN>
  ...
  <#return>
  ...
</#macro>

<@name>
	Target nested
</@name>
```

- macro를 선언하고 name을 지정해 줍니다.
- name 뒤에 띄어쓰기 후 원하는 파라미터를 선언할 수 있습니다.
- `<@name>`형식을 통해 선언된 macro를 사용합니다.
- macro 내부의 `<#nested>`는 사용하는 쪽에서 원하는 내용을 받아와서 넣을 수 있습니다.(자세한 내용은 예제에서...)
- `<#return>`을 사용하면 `<#return>`이하의 구문은 실행시키지 않습니다.

## 2. macro 간단 예제
### 간단 생성
```html
<#--macro1 생성-->
<#macro macro>
macro 간단 호출
</#macro>
<#-- 메크로 호출 -->
<@macro/>
```

### 파라미터 사용하기
```html
<#macro macro a b c="I'm default parameter">
macro1 파라미터 사용: ${a}, ${b}, ${c}
</#macro>
<@macro a="a" b=4 />
```

### 배열 넘겨주기
```html
<#macro list title language>
<p>${title?cap_first}:
<ul>
    <#list language as x>
    <li>${x?cap_first}
    </#list>
</ul>
</#macro>
<@list language=["java", "javascript", "freemarker"] title="Language"/>
```

<p align="center">
<img src="/images/Freemarker/1.png"/>
</p>
<p align="center">
<code>간단 예제 출력 결과</code>
</p>

## 3. macro 응용
주로 사용하는 구문을 따로 파일로 만들어 놓고 해당 파일을 `import`시켜서 사용하면 코드가 더 깔끔해 집니다.
```html
<#--import.ftl-->
<#import "macro3.ftl" as layout1>
<#import "macro4.ftl" as layout2>
<head>
    <meta charset="UTF-8">
    <title>Freemaker Macro Import Test</title>
</head>
<body>
<h1>Import Test</h1>

<@layout1.macro3 1 2 3 4 5 />

<br>

<@layout1.macro3 a=1 b=2 c=3 d=4 e=5 data\-foo=6 myns\:bar=7 />

<br>

<@layout2.macro4 ; x>
    ${x}.nested 안에 이 내용이 들어감!<br>
</@layout2.macro4>
</body>
```

```html
<#--macro3.ftl-->
<#macro macro3 a b ext...>
a = ${a}
b = ${b}
	<#if ext?is_sequence>
        <#--여긴 숫자이면 들어옴-->
		<#list ext as e>
		${e?index} = ${e}!
		</#list>
	<#else>
		<#list ext as k, v>
		${k} = ${v}?
		</#list>
	</#if>
</#macro>
```

```html
<#--macro4.ftl-->
<#macro macro4>
	<#nested 1>
	<#nested 2>
	<#return>
	3번째는 실행이 안된다.
	<#nested 3>
</#macro>
```

<p align="center">
<img src="/images/Freemarker/2.png"/>
</p>
<p align="center">
<code>import 응용 출력 결과</code>
</p>

## 참조
>모든 예제는 [Freemaker Macro docs](http://freemarker.org/docs/ref_directive_macro.html)를 참고하였습니다. 더 자세한 내용은 docs를 참고해 주시기 바랍니다.