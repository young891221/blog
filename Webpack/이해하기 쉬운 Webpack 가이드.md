# 이해하기 쉬운 webpack 가이드
연초 계획으로 세운 실서비스 코드를 개선하는 역할을 맡게 되었습니다. 해서 야심차게 작년 세미나(JSCON..기타)에서 강력하게 어필되었던 ES6 + Webpack 기반의 프론트 엔드 환경을 구성하기로 정하였습니다.
제 입장에서 webpack이란 무엇이고 왜 사용해야 되며 각각의 기능들의 역할에 대해 알기쉽게 풀어쓰도록 노력하였습니다. 틀린점은 가감없이 댓글 부탁드립니다.

### 1. webpack이란?
webpack이란 Module Bundler이다. 간단합니다. webpack(TODO:사이트 주소)로 들어가시면 바로 확인하실 수 있습니다. 그럼 bundler란 무엇일까요? wiki에는 다음과 같이 정의하고 있습니다.

<p align="center">
<img src="/images/Webpack/1.png"/>
</p>

해석하자면 소프트웨어 및 일부 하드웨어와 함께 작동하는 데 필요한 모든 것을 포함하는 패키지라고 설명이 나와 있습니다. 사전적 의미로는 객체의 그룹 포장 또는 묶는다는 의미라고 나와 있습니다. 
프로그래밍적 의미에서 저 나름데로는 다음과 같이 결론 지였습니다.

> **bundler**: bundler란 필요한 의존성에 대해 정확하게 추적하여 해당하는 의존성들을 그룹핑 해주는 도구라고 정의하였습니다.

- 정리하자면 Module Bundler는 각각의 모듈들에 대해 의존성 관계를 파악하여 그룹핑해주는 것이라고 설명할 수 있습니다.
- 즉, webpack은 의존성들의 관계들을 묶으면서 여러가지 기능들을 자동 수행시켜주는 도구입니다.

### 2. webpack의 장점은?
webpack을 써야 하는 이유는 webpack docs(TODO:사이트 주소)에 자세히 나와 있습니다. 간략하게 장점을 정리하면 다음과 같습니다.
- 다른 module bundler에 비해 performance가 우수합니다.(TODO: 구글링 자료)
- Code Split: chunk 단위로 의존성 트리를 동기적, 비동기적으로 분할할 수 있습니다. 
- Loader가 존재하여 다른 리소스에 대해 JavaScript로 변환하여 모든 리소스에 대해 모듈을 구성합니다.
	- babel을 사용하여 ES6와 같이 브라우저에서 지원되지 않는 script code를 변환하여 사용할 수 있습니다.
- 3rd-party library에 대해 모듈로 통합하는 기능을 제공합니다.
- module bundler의 대부분의 기능을 사용자가 커스터마이징하여 사용할 수 있습니다.
- 다양한 플로그인을 제공합니다.
> **chunk**: chunk란 코드 혹은 모듈을 묶은 하나의 단위로 정의하였습니다. 

### 3. webpack없이 ES6를 개발한다면?
이제까지 webpack의 기본 개념에 대해 살펴보며 이론적으로 어떠한 개념인지 이해하셨을 생각합니다.(그렇죠...?) 그래도 정확하게 이해가 안된다면 아래 예제를 보면 더 쉽게 이해가 가실 겁니다.
프론트 코드를 개발하는데 coffee script나 ES6문법으로 개발하였다고 가정하겠습니다. 이 코드를 흔히 쓰는 Spring, Python, 기타.. 웹 어플리리케이션 환경에서 개발을 진행하는데 webpack이 없다면??
매번 빌드할때 마다 babel(TODO:사이트 주소)(JavaScript TransFiler)을 사용하여 coffee script, ES6 코드를 순수 Javascript 코드로 변환해 주어야 할 것입니다. 일일이 매번 수정이 일어날 때마다 말이죠.




### 4. webpack 주요 기능
webpack의 필요성을 아셨다면 본격적으로 webpack의 주요 기능들을 살펴 보겠습니다. 예제 코드를 보며 하나씩 살펴보도록 하겠습니다. webpack은 기본적으로 webpack.config.js파일에서 설정할 수 있습니다.

#### entry/output
webpack에서는 bundling되기 위한 파일들을 묶을 수 있도록 entry와 output을 제공합니다.
- entry: entry 프로퍼티는 bundling하고 싶은 파일들을 선언해 주면 됩니다.
	- key, value 방식으로 선언할 경우 key값은 파일의 이름이 됩니다.
- output: output 프로퍼티는 bundling되고 만들어질 파일에 대한 정보를 명시해 주면 됩니다.
	- entry 프로퍼티에서 선언된 key의 이름은 `[name]`으로 사용되어 파일이 만들어집니다.
다른 다양한 선언방식은 이곳(TODO:사이트 주소)을 참고해 주시기 바랍니다. 
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

#### loader
저희가 webpack을 사용하는 이유에 해당하는 기능입니다. loader는 bundling을 진행하며 실행되는 일종의 함수라 생각합니다. loader부분에서 흔히 사용하는 esLint와 babel을 사용하는 코드입니다.
- preLoaders: 
- loaders: 
- postLoaders: 
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
 
 
#### plugins

#### 응용코드
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
        new webpack.NoErrorsPlugin()
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

### 5. 끝으로...

