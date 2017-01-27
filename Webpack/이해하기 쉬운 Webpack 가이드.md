# 이해하기 쉬운 webpack 가이드
연초 계획으로 실서비스 코드를 개선하는 역할을 맡게 되었습니다. 그래서 작년 세미나(JSCON..기타)에서 강력하게 어필되었던 ES6 + Webpack 기반의 프론트 엔드 환경을 구성하기로 정하였습니다.
제 입장에서 webpack이란 무엇이고 왜 사용해야 되며 어떠한 기능들이 있는지 알기쉽게 풀어쓰도록 노력하였습니다. 모든 자료는 [github](https://github.com/young891221/SpringBoot-Webpackhttps://github.com/young891221/SpringBoot-Webpack)에 있습니다. 
미숙한 부분은 가감없이 댓글 부탁드립니다.

## 1. webpack이란?
webpack이란 Module Bundler입니다. [webpack](http://webpack.github.io/docs/)으로 들어가시면 심플하게 딱 쓰여있어서 바로 확인하실 수 있습니다. 그럼 bundler란 무엇일까요? wiki에는 다음과 같이 정의하고 있습니다.

<p align="center">
<img src="/images/Webpack/1.png"/>
</p>

해석하자면 `소프트웨어 및 일부 하드웨어와 함께 작동하는 데 필요한 모든 것을 포함하는 패키지`라고 설명이 나와 있습니다. 어휘적 의미로는 `객체의 그룹 포장 또는 묶는다는 의미`라고 나와 있습니다. 
프로그래밍적 의미에서 저 나름데로는 다음과 같이 결론 지였습니다.

> **bundler**: bundler란 필요한 의존성에 대해 정확하게 추적하여 해당하는 의존성들을 그룹핑 해주는 도구라고 정의하였습니다.

- 정리하자면 Module Bundler는 `각각의 모듈들에 대해 의존성 관계를 파악하여 그룹핑해주는 것`이라고 설명할 수 있습니다.
- 즉, webpack은 의존성들의 관계들을 묶으면서 여러가지 기능들을 자동 수행시켜주는 도구입니다.

## 2. webpack의 장점은?
webpack을 써야 하는 이유는 [webpack docs](http://webpack.github.io/docs/what-is-webpack.html)에 자세히 나와 있습니다. 그래도 빠르게 장점을 보고 싶은 분들을 위해 간략하게 장점을 요약하였습니다.
- 다른 module bundler에 비해 [performance](https://nolanlawson.com/2016/08/15/the-cost-of-small-modules/)가 우수합니다.
- Code Split: chunk 단위로 의존성 트리를 동기적, 비동기적으로 분할할 수 있습니다. 
- Loader가 존재하여 다른 리소스를 순수 JavaScript로 변환하여 모든 리소스에 대한 모듈을 구성해 줍니다.
	- babel을 사용하여 ES6와 같이 브라우저에서 지원되지 않는 script code를 변환하여 사용할 수 있습니다.
- 3rd-party library에 대해 모듈로 통합하는 기능을 제공합니다.
- module bundler의 대부분의 기능을 사용자가 커스터마이징하여 사용할 수 있습니다.
- 다양한 플로그인을 제공합니다.
> **chunk**: chunk란 코드 혹은 모듈을 묶은 하나의 단위로 정의하였습니다. 

## 3. webpack은 왜 쓰일까?
Browsify, Grunt, Gulp 등 도구들은 webpack과 무슨 차이가 있을까요?? 
- webpack은 모든 리소스들(javascript, css, images, fonts)을 [dependency graph](https://webpack.js.org/concepts/dependency-graph/)를 생성시켜주며 빌드시켜주는 도구입니다.
- webpack은 `require()`를 사용하는 것처럼 리소스들간의 의존성 관계를 형성시켜주며 어떻게 javascript를 bundling할 것인지 결정해 줍니다.
- 크고 복잡하며 다양한 리소스들이 들어있는 프로젝트에는 webpack을 사용하는 것이 최상의 선택일 것입니다.
- 질문에 대한 답으로 Grunt, Gulp는 오로지 리소스들에 대한 툴로 사용되며 dependency graph에 대한 개념이 없습니다. Browsify는 비슷한 도구이지만 속도면에서 webpack이 더 우월합니다.

## 4. webpack 주요 기능
webpack의 필요성을 아셨다면 본격적으로 webpack의 주요 기능들을 살펴 보겠습니다. 예제 코드를 보며 하나씩 살펴보도록 하겠습니다. webpack은 기본적으로 `webpack.config.js`파일에서 설정할 수 있습니다.

### entry/output
webpack에서는 bundling되기 위한 파일들을 묶을 수 있도록 entry와 output을 제공합니다.
- entry 프로퍼티는 bundling하고 싶은 파일들을 선언해 주면 됩니다.
	- key, value 방식으로 선언할 경우 key값은 파일의 이름이 됩니다.
- output 프로퍼티는 bundling되고 만들어질 파일에 대한 정보를 명시해 주면 됩니다.
	- entry 프로퍼티에서 선언된 key의 이름은 `[name]`으로 사용되어 파일이 만들어집니다.
다양한 선언방식은 [이곳](http://webpack.github.io/docs/configuration.html#entry)을 참고해 주시기 바랍니다. 
```javascript
//webpack.config.js
module.exports = {
	entry: {
        bundle: path.join(__dirname + '/src/main/resources/static/entry/entry.js')
    },
	output: {
	    path: path.join(__dirname, '/src/main/resources/static'),
	    filename: '[name].js'
	}
}
```

### loader
loader는 bundling을 진행하며 실행되는 일종의 함수역할을 맡고 있습니다. 주로 coffee.js나 JSX같은 파일들의 transfiling 기능을 수행합니다. 흔히 쓰이는 eslint와 babel을 사용해 보겠습니다.
- preLoaders: loaders가 실행되기 전에 실행할 loader들을 선언하는 부분입니다.
- loaders: 메인으로 돌아가는 loader들을 선언하는 부분입니다.
- postLoaders: loaders가 실행된 후에 실행할 loader들을 선언하는 부분입니다.
	- test: loader를 적용시킬 목적의 파일들을 정규식으로 명시해 줍니다.
	- loader: 사용할 loader를 명시합니다.
	- exclude: loader가 진행될 때 배제시킬 파일들을 명시해 줍니다.
	- query: loader는 query를 통해 매개 변수를 전달할 수 있습니다.
	- 기타 자세한 스펙은 여기를 [참고](http://webpack.github.io/docs/using-loaders.html)하세요.
```javascript
module.exports =  {
    preLoaders: [
        {
            test: /\.js$/,
            loader: 'eslint',
            exclude: /(node_modules|bower_components)/
        }
    ],
    loaders: [
        {
            test: /\.js$/,
            loader: 'babel',
            exclude: /(node_modules|bower_components)/,
            query: {
                presets: ['es2015']
            }
        },
        {
            test: /\.(css|scss)$/,
            loader: ExtractTextPlugin.extract({
                fallbackLoader: "style-loader",
                loader: "css-loader!postcss-loader!sass-loader"
            })
        },
        {
            test: /\.(gif|png|jpg)$/,
            loader: 'file-loader?name=images/[name].[ext]&mimeType=image/[ext]&limit=100000'
        }
    ]
}
```
 
 
### plugins
플러그인의 종류가 너무 많기에 예제로 쓰이는 기능들에 대해서만 간단하게 소개해 드립니다.

```javascript
plugins: [
        new webpack.optimize.OccurenceOrderPlugin(),
        new webpack.HotModuleReplacementPlugin(),
        new webpack.NoErrorsPlugin(),
        new CommonsChunkPlugin({
            name: "commons",
            filename: "common.js",
            minChunks: Infinity,
        })
    ]
```
`new webpack.optimize.OccurrenceOrderPlugin(preferEntry)`
- 발생 시점에 따라 ID값을 할당시켜주며 자주 사용되는 ID는 더 낮은 ID를 갖습니다. 그래서 예측 가능하게 해줍니다.
- preferEntry의 우선순위를 높입니다.

`new HotModuleReplacementPlugin()`
- dev-server 모드에서 Hot Module Replace를 가능하게 해줍니다.
- 현재 사용되는 github의 예제에서는 Spring 환경이기에 적용되지 않습니다.(궁금해서 넣어봤습니다.)

`new NoErrorsPlugin()`
- 컴파일 도중 오류가 일어난 리소스들은 제외시켜주고 컴파일 해줍니다.

`new webpack.optimize.CommonsChunkPlugin(option)`
- 공유되는 common script파일을 생성해 주며 각종 옵션을 통해 커스터마이징 설정이 가능합니다.
- [API 스펙](https://github.com/webpack/docs/wiki/list-of-plugins#commonschunkplugin)

`UglifyJsPlugin`
- output으로 나오는 스크립트 파일을 minify해 줍니다.
- [API 스펙](https://github.com/webpack/docs/wiki/list-of-plugins#uglifyjsplugin)

`new ExtractTextPlugin("styles.css")`
- js파일이 로딩되는 동안 그 안에 있는 css파일들도 같이 로드되지 못합니다.
- ExtractTextPlugin은 js bundle파일에서 css bundle파일을 분리해 줍니다.
- css bundle이 js bundle파일과 병렬로 로드되므로 프로젝트 규모가 클수록 더 빠를 것입니다.
- [API 스펙](https://github.com/webpack-contrib/extract-text-webpack-plugin)

`EnvironmentPlugin`
- process.env를 통해 환경 변수를 받습니다.
- 예제에서 watch mode인지 real mode인지 구분시켜주는 변수를 받습니다.

`new webpack.WatchIgnorePlugin(paths)`
- paths(정규식 or 경로)에 있는 리소스들은 watch시 제외됩니다.(유용할거 같아서 추가했습니다.) 

### 응용코드
```javascript
const path = require('path');
const webpack = require('webpack');
const CommonsChunkPlugin = require("webpack/lib/optimize/CommonsChunkPlugin");
const ExtractTextPlugin = require("extract-text-webpack-plugin");
const webpackMerge = require('webpack-merge');

const target = process.env.npm_lifecycle_event;
const common = {
    entry: {
        bundle: path.join(__dirname + '/src/main/resources/static/entry/entry.js')
    },
    output: {
        path: path.join(__dirname, '/src/main/resources/static'),
        filename: '[name].js'
    },
    resolve: {
        extensions: ['', '.js', '.css'],
        alias: {
            'styles': __dirname + '/static/styles',
            'images': __dirname + '/static/images'
        }
    },
    module: {
        preLoaders: [
            {
                test: /\.js$/,
                loader: 'eslint',
                exclude: /(node_modules|bower_components)/
            }
        ],
        loaders: [
            {
                test: /\.js$/,
                loader: 'babel',
                exclude: /(node_modules|bower_components)/,
                query: {
                    presets: ['es2015']
                }
            },
            {
                test: /\.(css|scss)$/,
                loader: ExtractTextPlugin.extract({
                    fallbackLoader: "style-loader",
                    loader: "css-loader!postcss-loader!sass-loader"
                })
            },
            {
                test: /\.(gif|png|jpg)$/,
                loader: 'file-loader?name=images/[name].[ext]&mimeType=image/[ext]&limit=100000'
            }
        ]
    },
    plugins: [
        new webpack.optimize.OccurenceOrderPlugin(),
        new webpack.HotModuleReplacementPlugin(),
        new webpack.NoErrorsPlugin(),
        new CommonsChunkPlugin({
            name: "commons",
            filename: "common.js",
            minChunks: Infinity,
        })
    ]
}

const prodConfig = {
    plugins: [
        new webpack.optimize.UglifyJsPlugin({
            compress: {warnings: false}
        }),
        new ExtractTextPlugin("[name].css")
    ]
}

var config;
if(target === 'watch') {
    console.log('watch build');
    config = common;
} else {
    console.log('real build');
    config = webpackMerge(common, prodConfig);
}

module.exports = config;
```

## 5. 끝으로...
실무에서 webpack을 써보니 일단 작년까지 핫하던 Grunt나 Gulp를 충분히 대체시켜주는게 인상적이였습니다. 무엇보다 ES6를 도입시킬 수 있게 해주었고 watch기능을 통해 빠르고 편리한 개발환경을 제공해 주는 것이 저한테는 가장 큰 장점이였습니다.
다만, Spring 환경에서는 watch로 변경을 감지해도 바로바로 리소스가 업데이트되지 않는 점(저는 devtool을 사용해서 리소스 업로드에 있어 livereload를 가능하게 해주었습니다.)과 document문서가 빠르게 파악하고 사용하게끔 심플하게 구성되지 못한 점이 아쉬웠습니다.
그래도 현재 이만한 bundling tool은 없다고 생각합니다. 다음에는 제가 적용한 coffee script 제거편을 써보도록 하겠습니다. 긴 글 읽어주셔서 감사합니다.

`참조 URL`
- http://webpack.github.io/docs/
- https://webpack.js.org/
- https://github.com/webpack/docs/wiki/list-of-plugins

