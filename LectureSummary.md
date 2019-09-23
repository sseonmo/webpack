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

## Webpack Dev Server
* webpack-dev-server : webpack 자체에서 제공하는 개발 서버빠른 리로딩 기능을 제공한다.

### Options
* publicPath : Webpack으로 번들한 파일들이 위치하는 곳. default 값은 /
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

## Webpack Dev Middleware
* webpack-dev-middleward : 서버가 이미 구성된 경우에는 webpack을 미들웨어로 구성하여 서버와 연결.
