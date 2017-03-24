# Spring Boot 환경에서 webpack dev server 사용하기
Spring Boot에서 Webpack을 사용하며 script 개발시 `webpack --watch`를 사용하여 디버깅하였습니다. 
Boot의 [devtool](http://haviyj.tistory.com/11)를 설정하여 live reloading이 가능하였지만 `watch`기능이 bundle를 아에 새것으로 바꿔주는 형식이라 실시간으로 변경을 감지하지 못하였고 그에 따라 디버깅이 굉장히 불편했습니다.
<br>
프론트 개발시에도 실시간 디버깅을 위해 `webpack-dev-server`를 사용하였고 이제는 F5를 손에서 떼버리게 되었습니다.
(부끄럽게도 처음에는 Spring 환경에서 `webpack-dev-server`를 못쓰는줄 알았습니다...) 모든 코드는 [github](https://github.com/young891221/blog/blob/master/Webpack/Spring%20Boot%20%ED%99%98%EA%B2%BD%EC%97%90%EC%84%9C%20Webpack%20dev%20server%20%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0.md)에 있으며 아래는 이에 관한 설정내용입니다.

>저의 주 개발환경이 freemarker(Server Tmeplate Engine)을 사용하기 때문에 프론트와 백엔드를 따로 분리 하지 않고 하나의 프로젝트로 개발하였습니다.

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

### paskage.json script 설정
```javascript
{
	...
	"scripts": {
	    "start": "webpack-dev-server --progress --inline",
	    "watch": "webpack -d --watch",
	    "dev": "webpack -d",
	    "prod": "webpack -p",
	    "test": "echo \"Error: no test specified\" && exit 1"
	  }
	...
}
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
디버깅을 위한 source mapping style을 선택할 수 있습니다. 변환되기 전, 변환된 후 혹은 각각의 성능에 따라 devtool option을 선택할 수 있습니다.
>자세한 설명은 [devtool docs](https://webpack.js.org/configuration/devtool)를 참고하세요. 저에게 도움이 되었던 [양권성님의 블로그](https://perfectacle.github.io/2016/11/14/Webpack-devtool-option-Performance/)도 참고하시면 성능 테스트까지 확인하실 수 있습니다.


### devServer option
- historyApiFallback: [HTML5 History API](https://developer.mozilla.org/en-US/docs/Web/API/History)를 사용해야 한다면 'true'값으로
- progress: console에 진행사항 표시(%)
- quiet: error, warning을 console에서 안보이게 합니다.
- compress: enable gzip compress(압축)
- **publicPath**
	- 브라우저에서 bundle 파일이 위치한 경로를 잡아줘야 합니다.(**중요합니다** 이것 때문에 삽질을...)
	- `http://localhost:3000/static/bundle.js`에 위치한다면 다음과 같이 잡습니다.
	```npm
	publicPath: "/static/"
	```
	- HMR(Hot Module Replacement)을 사용한다면 전체 경로를 잡아줘야 합니다.
    ```npm
    publicPath: "http://localhost:3000/static/bundle.js"
    ```
- host: 디폴트는 `localhost`. 만약 외부의 다른 host를 잡고 싶다면 `0.0.0.0`.
- **proxy**: proxying하기 원하는 URL을 설정해 줄 수 있습니다. 현재 백엔드 서버 호스트를 설정해 주었습니다. 자세한 설명은 [여기](https://webpack.js.org/configuration/dev-server/#devserver-proxy)를 참고하세요.

>`webpack-dev-server`는 중요한 옵션들을 CLI를 사용하여 설정할 수 있습니다. 설정이 유연하게 제공되어 자칫 충돌을 일으킬 수 있습니다.(저 또한 HMR설정에 삽질을 많이 했습니다...) CLI설정은 [여기](http://webpack.github.io/docs/webpack-dev-server.html#webpack-dev-server-cli)를 참고하세요.

### HMR(Hot Module Replacement)
`webpack-dev-server`의 또 하나의 장점인 HMR은 특정 모듈의 변화를 감지하여 변경된 부분만 페이지 reload 없이 빠르게 변경해 줍니다. HMR은 **HMR을 구현하는 'loaders'에서만 작동**됩니다.('react-hot-loader', 'style-loader' etc)
<br>
설정방식이 다양하기 때문에 본인에게 맞는 것, 그리고 되는 것으로 취사선택하시면 됩니다. react-hot-loader 방식은 [여기](https://webpack.js.org/guides/hmr-react)를 참조하시면 빠르게 세팅가능합니다.

### 작동방법
```npm
npm start
```
```npm
Spring Boot Run
```

<p align="center">
<img src="/images/Webpack/webpack-dev-server/start.png"/>
</p>
<p align="center">
<code>npm start 실행<code>
</p>

<p align="center">
<img src="/images/Webpack/webpack-dev-server/result.gif"/>
</p>
<p align="center">
<code>결과 화면</code>
</p>


## 3. 삽질해소에 도움이 되었던 참조 사이트
- watch vs hot: http://stackoverflow.com/questions/38089785/webpack-watch-vs-hot-whats-the-difference
- HMR 관련: https://github.com/webpack/webpack-dev-server/issues/395
- devserver 설정관련: https://github.com/webpack/webpack-dev-server/issues/24