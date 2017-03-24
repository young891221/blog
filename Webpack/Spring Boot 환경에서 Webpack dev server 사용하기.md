# Spring Boot 환경에서 webpack dev server 사용하기
Spring Boot에서 Webpack을 사용하며 script 개발시 `webpack --watch`를 사용하여 디버깅하였습니다. Boot의 [devtool](http://haviyj.tistory.com/11)를 설정하여 live reloading이 가능하였지만 `watch`기능이 bundle를 아에 새것으로 바꿔주는 형식이라 
실시간으로 변경을 감지하지 못하였고 그에 따라 디버깅이 굉장히 불편했습니다. 
프론트 개발시에도 실시간 디버깅을 위해 `webpack-dev-server`를 사용하였고 이제는 F5를 손에서 떼버리게 되었습니다.(부끄럽게도 처음에는 Spring 환경에서 `webpack-dev-server`를 못쓰는줄 알았습니다...) 아래는 이에 관한 설정내용입니다.

>Spring Boot에서 백엔드와 프론트를 분리하여 따로 서버를 구성하는 [심플한 영어자료](http://justincalleja.com/2016/04/17/serving-a-webpack-bundle-in-spring-boot/)가 있습니다. 저의 환경은 서버 템플릿(freemarker)를 주로 사용하기 때문에 따로 분리는 하지 않고 하나의 프로젝트로 개발하였습니다.

## 1. webpack-dev-server란?
webpack-dev-server는 말그대로 임시 개발 서버를 하나 띄워서 live reloading을 제공합니다. 의존성추가와 몇 개의 옵션 추가로 손쉽게 추가할 수 있습니다. 
`webpack --watch`(writing disk)는 bundle파일을 새로만들지만 `webpack-dev-server`(writing memory)는 memory에 bundle 파일을 올려놓고 변경사항이 생길때 마다 이에 대한 결과를 live reload시켜 줍니다. 고로 훨씬 좋은 performance를 보여줍니다.

## 2. webpack-dev-server 설정
```npm
npm install webpack-dev-server --save-dev
```
```html
<script type="text/javascript" src="bundle.js"></script>
```
### webpack.config.js에 추가 / 혹은 webpack.dev.config.js를 따로 두어 로컬개발시에만 적용 
```javascript
module.exports = {
    devtool: 'inline-source-map',
    devServer: {
        historyApiFallback: true,
        compress: true,
        publicPath: '/',
        host: "0.0.0.0",
        port: 3000,
        proxy: {
            "**": "http://localhost:8080"
        }
    },
    plugins: [
        new webpack.NamedModulesPlugin() //브라우저에서 HMR 에러발생시 module name 표시
    ]
}
```

### devtool option
디버깅을 위한 source mapping style을 선택할 수 있습니다. 변환되기 전, 변환된 호 혹은 각각의 성능에 따라 devtool option을 선택할 수 있습니다.
>자세한 설명과 성능은 [devtool docs](https://webpack.js.org/configuration/devtool)를 찾고하세요. 저에게 도움이 되었던 [양권성님의 블로그](https://perfectacle.github.io/2016/11/14/Webpack-devtool-option-Performance/)도 참고하시면 이해하시는데 더 좋습니다.


### devServer option
- historyApiFallback: [HTML5 History API](https://developer.mozilla.org/en-US/docs/Web/API/History)를 사용해야 한다면 'true'값으로
- progress: console에 진행사항 표시(%)
- quiet: error, warning을 console에서 안보이게 합니다.
- compress: enable gzip compress(압축)
- **publicPath**
	- 브라우저에서 bundle 파일이 위치한 경로를 잡아줘야 합니다.
	- `http://localhost:3000/static/bundle.js`에 위치한다면 다음과 같이 잡습니다.
	```npm
	publicPath: "/static/"
	```
	- HMR(Hot Module Replacement)을 사용한다면 전체 경로를 잡아줘야 합니다.
    ```npm
    publicPath: "http://localhost:3000/static/bundle.js"
    ```
- host: 디폴트는 `localhost`. 만약 외부의 다른 host를 잡고 싶다면 `0.0.0.0`.
- **proxy**: proxying하기 원하는 URL을 설정해 줄 수 있습니다. 현재 백엔드에서 돌고 있는 경로를 설정해 주었습니다. 자세한 설명은 [여기](https://webpack.js.org/configuration/dev-server/#devserver-proxy)를 참고하세요.

>`webpack-dev-server`는 중요한 옵션들을 CLI를 사용하여 설정할 수 있습니다. 설정이 유연하게 제공되어 자칫 충돌을 일으킬 수 있습니다.(저 또한 HMR설정에 삽질을 많이 했습니다...) CLI설정은 [여기](http://webpack.github.io/docs/webpack-dev-server.html#webpack-dev-server-cli)를 참고하세요.



