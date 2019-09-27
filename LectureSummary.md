## 환경설정
1. npm i webpack g
2. npm i webpack-cli g
3. getting-started 디렉토리 이동 후 npm init -y (package.json 생성)
4. npm i lodash --save
    ```text
        npm install (plugin) --save란?
            --save는 앱이 구동하기 위해 필요한 모듈&라이브러리 ex) react, vue                          
            패키지(plugin)를 ./node_moduels 디렉터리에 설치하고 ./package.json 파일의 dependencies 항목에 플러그인 정보가 저장 됩니다.
            --production 빌드시 해당 플러그인이 포함됩니다.
        npm install (plugin) --save-dev란?
            --save-dev 는 앱 개발시 필요한 모듈 & 라이브러기 ex) test, build tool, live reloading
            패키지(plugin)를 ./node_moduels 디렉터리에 설치하고 ./package.json 파일의 devDependencies 항목에 플러그인 정보가 저장 됩니다.
            --production 빌드시 해당 플러그인이 포함되지 않습니다.
    ```
5. webpack app/index.js --output dist/bundle.js --mode development ( 실행 )
    - webpack.config.js 를 파일을 생성해서 좀 더 간단하고 webpack 실행하는 방법
    - 실행 : webpack --mode development
    ````js
       // webpack.config.js
       // `webpack` command will pick up this config setup by default
       var path = require('path');
       
       module.exports = {
       	entry: './app/index.js',  //모듈의 의존성 그래프를 만드는 첫 시작점
       	output: {                 // dist 디렉토리 아래 bundle.js로 파일을 만들겠다.
       		filename: 'bundle.js',
       		path: path.resolve(__dirname, 'dist')
       	}
       };
    
    ````
## Webpack 명령어
* webpack: 웹팩 빌드 기본 명령어(개발용)
* webpack -p: minification 기능이 들어간 빌드(배포용) - 배포에 필요한 리소스를 압축형태로 제공한다.
* webpack -watch(-w): 개발에서 빌드할 파일의 변화를 감지
* webpack -d: sourcemap 포함하여 빌드
* webpack --display-error-details: error 발생시 디버깅 정보를 상세히 출력
* webpack --optimize-minimize --define process.env.NODE_ENV="'production'": 배포

## Webpack Entry
* webpack으로 묶은 모든 라이브러리들을 로딩하는 시작점 설정
* 1개 또는 2개 이상의 엔트리 포인트를 설정할 수 있다.( 문자, array, object 형태로 추가 가능)
```js
var config = {
  // 1. 간단한 entry 설정
  entry: './path/to/my/entry/file.js'
  // 2. 앱 로직용, 외부 라이브러리용
  entry: {
    app: './src/app.js',
    vendors: './src/vendors.js'
  }
  //3. 페이지당 불러오는 js 설정
  entry: {
    pageOne: './src/pageOne/index.js',
    pageTwo: './src/pageTwo/index.js',
    pageThree: './src/pageThree/index.js'
  }
};
``` 

## Webpack Output
* output name option
```js
var path = require('path');
module.exports = {
	entry: '{
      ...
    },
	output: {
		filename: 'bundle.js',
		path: path.resolve(__dirname, 'dist') 
        //  filename : '[name].js' - entry를 여러개 설정 할 경우 entry 값에 따라 파일이름이 생성된다.
	}
};

// filename
output: {
  filename: '[name].js',          // 엔트리 명에 따른 output 파일명 생성
  filename: '[hash].js',          // 특정 webpack build 에 따른 output 파일명 생성
  filename: '[chunkhash].js'      // 특정 webpack chunk에 따른 output 파일명 생성
}
```
### Tip
* output.path : 번들링한 결과가 위치할 번들링 파일의 절대경로
* output.publicPth : 브라우저가 참고할 번들링 결과 파일의 URL 주소(CDN을 사용하는 경우 CND 호스트 지정)
```js
//publicPath 예제 1
output:{
  path: "/home/proj/public/assets",
  publicPath: "/assets/"
}

//publicPath 예제 2
output:{
  path: "/home/proj/cdn/assets/[hash]",
  publicPath: "http://cdn.example.com/assets/[hash]/"
}
```

## Webpack Loader
* webpack은 자바스크립트 파일만 처리가 가능하도록 되어 있다. 
* loader를 이용하여 다른 형태의 웹 자원들을(img, css...) js로 변환하여 로딩
* Babel Loader - ES6
    - preset: Babel 플러그인 리스트
    ```js
      //webpack config
      module: {
        rules: [{
          test: /\.js$/,
          use: [{
          	loader: 'babel-loader',
             options: {
          		presets: [
                  ['es2015', 'react', {modeule: false}] // Tree Shaking : 모듈에서 사용되지 않는 모듈은 추가하지 않음 
                ]
              }
            }]
        }]
      }
    
    // .babelrc
    {
      "presets": ["react", "es2015"]
    } 
    ```
  
## Webpack Plugins
* 파일별 커스텀기능을 사용하기 위해서 사용한다.
* 로더랑 비교하면 로더는 파일을 해석하고 변환하는 과정에 관여하는 반면, 플러그인은 해당 결과물의 형태를 바꾸는 역활을 수행한다.

```js
// webpack.config.js
var webpack = require('webpack');
var HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  plugins: [
    new HtmlWebpackPlugin(),
    new webpack.ProgressPlugin()
  ]
``` 
 - HtmlWebpackPlugin : 웹팩으로 빌드한 결과물로 HTML 파일을 생성해주는 플러그인
 - ProgressPlugin : 웹팩의 빌드 진행율을 표시해주는 플러그인
 - `자주 사용하는 플러그인`
     - split-chunks-plugin
     - clean-webpack-plugin
     - image-webpack-loader
     - webpack-bundle-analyzer-plugin 

## Webpack Resolve
* Webpack의 모듈번들링 관점에서 봤을때, 모듈 간의 의존성을 고려하여 모듈을 로딩해야 한다. 
* 따라서, 모듈을 어떤 위치에서 어떻게 로딩할지에 관해 정의를 하는 것이 바로 Module Resolution이다.
```js
//일반적인 모듈 로딩 방식
//ES6
import foo from 'path/to/module';
//ES5
require('path/to/module');
```
### 사용법
```js
// webpack.config.js
module.exports = {
  mode: 'development',
  entry: './app/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  },

  // #2 - Using alias 
  // resolve: {
  //   alias: {
  //     Vendor: path.resolve(__dirname, './app/vendor/')
  //   }
  // }

  // #3 - Provide Plugin - 전역변수 설정이랑 비슷함 / lodash, jquery
  plugins: [
    new webpack.ProvidePlugin({
      $: 'jquery'
    })
  ]
};

// index.js
// #1 - Using NPM 
// var $ = require('jquery');
// console.log("loaded jQuery version is " + $.fn.jquery);

// #2 - Using alias
// import $ from 'Vendor/jquery-2.2.4.min.js';
// console.log("loaded jQuery version is " + $.fn.jquery);

// #3 - Using Provide Plugin
console.log("loaded jQuery version is " + $.fn.jquery);
```

## [Webpack Dev Server](https://webpack.js.org/guides/development/#using-webpack-dev-server)
* webpack-dev-server : webpack 자체에서 제공하는 개발 서버빠른 리로딩 기능을 제공한다.

### Options
* publicPath : Webpack으로 번들한 파일들이 위치하는 곳. default 값은 /
               브라우저가 참고할 번들링 결과파일의 URL주소를 지정.(CDN을 사용하는 경우 CDN 호스트 지정)
```js
// 항상 '/' 를 암뒤에 붙여야 한다.
publicPaht: "/assets/"
```
* contentBase : 서버가 로딩할 static 파일 경로를 지정. default 값은 `working directory`
```js
// 절대 경로를 사용할 것 
contentBase: path.join(__dirname, "public");
// 비활성화
contentBase: false
```
* compress : gzip 압축방삭을 이용하여 웹 자원의 사이즈를 줄인다.
```js
compress: true
```

```js
//webpack.config.js
module.exports = {
  entry: ...
  output: ...
  devServer: {
    // contentBase: path.join(__dirname, "dist")
    publicPath: "/dist/", // 절대 경로로 지정하고 앞뒤 '/' 꼭 지정
    compress: true,
    port: 9000
  }
}
```

## [Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware)
* webpack-dev-middleware : 서버가 이미 구성된 경우에는 webpack을 미들웨어로 구성하여 서버와 연결.
* 기존에 구성한 서버에 webpack에서 컴파일한 파일을 전달하는 middleware wrapper
* webpack에 설정한 파일을 변경시, 파일에 직접 변경내역을 저장하지 않고 메모리 공간을 활용한다. 
* 따라서, 변경된 파일 내역을 파일 디렉토리 구조안에서는 확인이 불가능하다. 
```js
// server.js - webpack dev middleware 설정부분
// #1
var webpackDevMiddleware = require("webpack-dev-middleware");
var webpack = require("webpack");
var webpackConfig = require("./webpack.config");
var compiler = webpack(webpackConfig);

// #2
app.use(webpackDevMiddleware(compiler, {
	publicPath: webpackConfig.output.publicPath,
	stats: {colors: true},
	lazy: true    //느린 컴파
}));
```

## Webpck watch 옵션
* webpack 설정에 해당되는 파일의 변경이 일어나면 자동으로 번들링을 다시 진행
* process - 번들링 진행사항을 확인하게 해준다.
```js
webpack --process --watch
```
* 참고: `npm install --save-dev serve` 한 후 아래처럼 `package.json`에 명령어 설정가능
```js
"scripts":{
  "start": "serve"
}
```

## 개발자 도구 연동 - [Devtool](https://webpack.js.org/configuration/devtool/)
* 브라우저에서 webpack로 컴파일된 파일을 디버깅 하기 어려움.
* 따라서, 아래와 같이 source-map 설정을 추가하여 원래의 파일구조에서 디버깅이 가능함.
```js
module.exports = {
  ...
  devtool: '#inline-source-map'
}
```
## [gulp](https://valuefactory.tistory.com/314)
## [Hot Module Replacement](https://webpack.js.org/guides/hot-module-replacement/)

## 참고자료
1. [Webpack2 Doc] (https://webpack.js.org/)
2. [Webpack1 Doc] (http://webpack.github.io/docs/)
3. [webpack-howto] (https://github.com/petehunt/webpack-howto)
4. [webpack-howto2] (https://gist.github.com/xjamundx/b1c800e9282e16a6a18e)
5. [webpack beginners guide, Site Point] (https://www.sitepoint.com/beginners-guide-to-webpack-2-and-module-bundling/?utm_source=frontendfocus&utm_medium=email)
6. [requireJS-to-webpackConfig] (https://www.npmjs.com/package/requirejs-to-webpack-cli)
7. [migration from requirejs to webpack] (https://medium.com/@ArtyomTrityak/migration-from-require-js-to-webpack-2-a733a4366ab5)
8. [webpack-shimming] (https://webpack.js.org/guides/shimming/)
9. [Critical-Dependencies] (http://webpack.github.io/docs/context.html#critical-dependencies)
10. [Gulp Webpack plugin] (https://www.npmjs.com/package/gulp-webpack)
11. [Webpack Dev Server StackOverFlow] (https://stackoverflow.com/questions/42712054/content-not-from-webpack-is-served-from-foo)
12. [Webpack Dev Middleware in Express] (http://madole.github.io/blog/2015/08/26/setting-up-webpack-dev-middleware-in-your-express-application/)
13. [Webpack Confusing Part] (https://medium.com/@rajaraodv/webpack-the-confusing-parts-58712f8fcad9)
14. [Regular Expression, MDN] (https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/%EC%A0%95%EA%B7%9C%EC%8B%9D)
15. [Regular Expression Test] (http://regexr.com/)
Webpack First Principle, Video https://www.youtube.com/watch?v=WQue1AN93YU  