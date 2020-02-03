# 08 웹 어플리케이션을 위한 범용 JavaScript

### 브라우저와 코드 공유하기

- 클라이언트와 서버 모두를 위한 코드를 개발하는 것은 본질적으로 다른 두 환경에서 동일한 코드가 올바르게 실행될 수 있도록 하기 위해 많은 노력이 필요함.
- Node.js에는 DOM이나 long-living 뷰가 없으며, 브라우저에는 파일 시스템이나 새 프로세스를 시작할 수 있는 기능이 없음.
- 어플리케이션이 브라우저 호환 코드와 Node.js 코드 사이에서 동적으로 또는 빌드할 때 전환할 수 있도록 해주는 추상화 및 패턴을 사용

##### 모듈 공유

- 브라우저와 서버 사이에서 코드를 공유하고자 할 때 서버에서 사용하는 모듈 시스템과 브라우저에서 사용되는 모듈 시스템의 이기종 환경으로 인한 불일치 해결이 필요.

```javascript

// UMD 모듈 만들기 (범용 모듈 정의: Universal Module Definition)

(function(root, factory) {
    if(typeof define === 'function' && define.amd) {
        define(['mustache'], factory);
    } else if(typeof module === 'object' && 
            typeof module.exports === 'object') {
        var mustache = require('mustache');
        module.exports = factory(mustache);
    } else {
        root.UmdModule = factory(root.Mustache);
    }
}(this, function(mustache) {
    var template = '<h1>Hello <i>{{name}}</i></h1>';
    mustache.parse(template);
    
    return {
        sayHello:function(toWhom) {
            return mustache.render(template, {name: toWhom});
        }
    };
}));

```

```javascript

    const umdModule = require('./umdModule');
    console.log(umdModule.sayHello('Server!'));
    
    // '<h1>Hello <i>Server!</i></h1>'

```

```html

<html>
    <head>
        <script src="node_modules/mustache/mustache.js"></script>
        <script src="umdModule.js"></script>
    </head>
    <body>
        <div id="main"></div>
        <script>
            document.getElementById('main').innerHTML =
                UmdModule.sayHello('Browser!');
        </script>
    </body>
</html>

```

##### ES2015 모듈

- ES2015 사양에서 소개된 기능 중 하나는 내장(build-in) 모듈 시스템이다.
- ES2015 모듈의 목표는 CommonJS 및 AMD 모듈을 최대한 활용하는 것.
    * 이 규격은 CommonJS와 마찬가지로 압축된 구문과 단일 exports를 선호하며 종속성 순환 지원을 제공.
    * AMD와 마찬가지로 비동기 로드 및 환경설정 가능한 모듈 로드를 직접 지원.
    
- Node.js에 Babel과 같은 변환기를 채택하여 이러한 새로운 모듈 구문을 사용할 수도 있다. 

### Webpack 소개

- Webpack은 Node.js 모듈 규칙을 사용하여 모듈을 작성한 다음, 컴파일 단계에서 모듈이 브라우저에서 작업하는데 필요한 모든 종속성을 포함하는 번들을 작성한다.
- 번들을 웹 페이지에 쉽게 불러오고 브라우저 내에서 실행할 수 있습니다.

##### Webpack의 마력 탐구

- Webpack을 사용할 경우 이전 섹션에서 만든 umdModule이 어떻게 작동하는지 확인.
    * 먼저 Webpack 설치 (npm install webpack -g)

```javascript

var mustache = require('mustache');
var template = '<h1>Hello <i>{{name}}</i></h1>';
mustache.parse(template);
module.exports.sayHello = function(toWhom) {
    return mustache.render(template, {name: toWhom});
};

```

- 확실히 UMD 패턴을 적용한 것보다 간단하다.

```javascript

// main.js

window.addEventListener('load', function(){
    var sayHello = require('./sayHello').sayHello;
    var hello = sayHello('Browser!');
    var body = document.getElementsByTagName("body")[0];
    body.innerHTML = hello;
});

```

- 앞의 코드에서 sayHello 모듈은 Node.js에서와 똑같은 방법으로 require하므로 종속성을 관리하거나 경로를 구성하는데 별다른 신경을 쓰지 않아도 된다.
    * mustache 설치 (npm install mustache)

- 명령 실행
    * webpack main.js

```javascript

// magic.html

<html>
    <head>
        <title>Webpack magic</title>
        <script src="bundle.js"></script>
    </head>
    <body>
    </body>
</html>

```

##### Webpack 사용의 이점

- Webpack은 브라우저와 호환되는 많은 Node.js 코어 모듈용 버전을 자동으로 제공
- 브라우저와 호환되지 않는 모듈이 있는 경우, 이를 빌드에서 제외하거나 빈 객체나 다른 모듈로 대체하거나 브라우저와 호환 구현을 제공하는 다른 모듈로 바꿈
- 다른 모듈에 대한 번들을 생성
- 서드파티 로더와 플러그인을 사용하여 소스 파일의 추가적인 처리를 가능하게 함.
- JavaScript 파일뿐 아니라 스타일시트, 이미지, 폰트 및 템플릿과 같은 모든 프로젝트 리소스를 관리하고 전처리 할 수 있음.
- 브라우저에서 필요할 때마다 로드할 수 있도록 Webpack을 구성할 수 있음.

##### Webpack과 함께 ES2015 사용하기

- Webpack의 주요 장점 중 하나는 로더와 플러그인을 사용하여 소스코드를 번들링하기 전에 변환할 수 있는 기능.
- 현재 사용 가능한 모든 브라우저와의 호환성을 최대한 보장하기 위해 결과 코드를 브라우저의 ES5로 전환한다.

```javascript
// src/sayHello.js

const mustache = require('mustache');
const template = '<h1>Hello <i>{{name}}</i></h1>'
mustache.parse(template);
module.exports.sayHello = toWhom => {
    return mustache.render(template, {name: toWhom});
};

```

```javascript
// src/main.js

window.addEventListener('load', () => {
    const sayHello = require('./sayHello').sayHello;
    const hello = sayHello('Browser!');
    const body = document.getElementsByTagName("body")[0];
    body.innerHTML = hello;
});

```

```javascript
// webpack.config.js

const path = require('path');

module.exports = {
    entry: path.join(__dirname, "src", "main.js"),
    output: {
        path: path.join(__dirname, "dist"),
        filename: "bundle.js"
    },
    module: {
        loaders: [
            {
                test: path.join(__dirname, "src"),
                loader: 'babel-loader',
                query: {
                    presets: ['es2015']
                }
            }
        ]
    }  
};

```

- 이 파일은 커맨드 창에서 아무런 인자 없이 호출할 때 Webpack이 읽을 환경설정 객체를 익스포트하는 모듈이다.

- Webpack을 실행하기 전에 Babel과 ES2015 사전설정을 다음 명령을 통해 설치
    * npm install babel-core babel-loader babel-preset-es2015
    * 번들을 생성하기 위해 실행(webpack)

### 크로스 플랫폼 개발의 기본

- 다른 플랫폼용으로 개발할 때 우리가 직면하는 가장 일반적인 문제는 플랫폼에 종속적인 세부사항들에 대해 서로 다른 구현을 제공하면서 컴포넌트의 공통적인 부분을 공유하는 것.

##### 런타임 코드 분기(branching)

- 호스트의 플랫폼에 따라 다양한 구현을 제공하는 가장 간단하고 직관적인 기술은 코드를 동적으로 분기하는 것.

```javascript

if(typeof window !== "undefined" && window.document) {
    // 클라이언트 측 코드
    console.log('Hey browser!');
} else {
    // Node.js 코드
    console.log('Hey Node.js!');
}

```

- 런타임 분기를 사용하는 것은 분명 가장 직관적이고 간단한 패턴이지만, 몇 가지 불편한 점이 있다.
    * 두 플랫폼의 코드가 동일한 모듈에 포함됨으로써, 최종적인 번들에 포함되어 감당할 수 없이 코드의 크기가 커진다.
    * 너무 광범위하게 사용하면 비즈니스 로직이 플랫폼 간 호환성을 위해 추가한 로직과 뒤섞여 코드의 가독성을 상당히 떨어뜨릴 수 있다.
    * 동적 분기를 사용하여 플랫폼에 따라 다른 모듈을 로드하면 대상 플랫폼과 상관없이 모든 모듈이 최종 번들에 추가된다.

##### 빌드 타임 코드 분기

- Webpack을 사용하여 빌드할 때 서버에서만 사용할 코드의 모든 부분을 제거하는 방법.
- 더 가벼운 번들 파일을 얻을 수 있고, 실수로 서버에만 있어야 할 유용한 코드가 노출되는 것을 방지 할 수 있음.

```javascript

if (typeof __BROWSER__ !== "undefined") {
    console.log('Hey browser!');
} else {
    console.log('Hey Node.js!');
}

```

```javascript
// webpack.config.js

const path = require('path');
const webpack = require('webpack');

const definePlugin = new webpack.DefinePlugin({
    "__BROWSER__": "true"
});

const uglifyJsPlugin = new webpack.optimize.UglifyJsPlugin({
    beautify: true,
    dead_code: true
});

module.exports = {
    entry: path.join(__dirname, "src", "main.js"),
    output: {
        path: path.join(__dirname, "dist"),
        filename: "bundle.js"
    },
    plugins: [definePlugin, uglifyJsPlugin]
};

```

- DefinePlugin을 사용하여 소스코드의 특정 부분을 동적 코드 또는 상수 값으로 바꿀 수 있음.
- UglifyJS를 사용하여 번들 파일의 JavaScript 코드를 나독화하고 최소화 하는데 사용.

```javascript
// 다음과 같이 변환

if (true) {
    console.log('Hey browser!');
} else {
    console.log('Hey Node.js!');
}

```

- 이 기술이 런타임 코드 분기보다 훨씬 더 좋다고 하더라도, 남용한다면 여전히 소스코드를 복잡하고 무겁게 만들 수 있다.
- 이 또한 바람직하지 않다.

##### 모듈 교환(module swapping)

- 빌드 타임에 클라이언트 번들에 포함되어야 하는 코드와 그렇지 않은 것들은 이미 알고 있고, 이 결정을 미리 정의하여 번들러에게 빌드시 모듈의 구현을 바꾸도록 지시할 수 있다.
- 불필요한 모듈을 제외할 수 있기 때문에 더 작은 번들이 생성되는 경우가 많으며, 런타임과 빌드타임의 브랜치에 필요한 if...else문을 모두 갖고 있지 않아 더 가독성 있는 코드가 된다.

```javascript

// alertServer.js
module.exports = console.log;

// alertBrowser.js
module.exports = alert;

```

```javascript

// main.js
const alert = require('./alertServer');
alert('알람을 켜든 말든 아침은 옵니다.');

```
- node.js main.js 실행
    * 콘솔에 출력됨.

```javascript

const path = require('path');
const webpack = require('webpack');

const moduleReplacementPlugin =
    new webpack.NormalModuleReplacementPlugin(/alertServer.js$/,
    './alertBrowser.js');

module.exports = {
    entry: path.join(__dirname, "src", "main.js"),
    output: {
        path: path.join(__dirname, "dist"),
        filename: "bundle.js"
    },
    plugins: [moduleReplacementPlugin]
};

```

- Webpack 및 모듈 교체를 위한 플러그인 덕분에 플랫폼 간의 구조적인 차이를 쉽게 다룰 수 있다.
- 플랫폼 별 코드를 제공하는 별도의 모듈을 작성하는데 주력할 수 있으며, 그런 다음 최종 번들에 특정 브라우저 전용 모듈만 포함하여 해당 모듈을 교체할 수 있다.

### 리액트 소개

- React는 컴포넌트의 개념에 초점을 맞추어 뷰의 추상화를 제공.
    * 컴포넌트는 버튼, 입력 폼, HTML의 div 또는 사용자 인터페이스의 다른 모든 엘리먼트 같은 간단한 컨테이너
- 이 개념은 특정한 책임을 가진, 고도로 재사용 가능한 컴포넌트를 정의하고 구성하는 것만으로 어플리케이션의 사용자 인터페이스를 구축할 수 있어야 한다.
- React가 웹에 대한 다른 뷰의 구현체들과 다른 점은 설계 상 DOM에 바인딩되어 있지 않다.
- 실제로 웹과 매우 잘 어울리는 Virtual DOM이라는 더 높은 수준의 추상화를 제공하지만 다른 영역에서도 사용될 수 있다. 예를 들면 모바일 어플리케이션 구축과 3D 환결 모델링, 더 나아가 하드웨어 컴포넌트 간의 상호작용 정의 같은 것들이 있다.
- React의 모토 : "한번 배우고 모든 곳에 사용하세요"
- React가 범용 JavaScript개발과 관련해 매우 흥미로운 주된 이유는 거의 동일한 코드를 사용하여 서버와 클라이언트 모두에서 뷰 코드를 렌더링 할 수 있기 때문.
- 대부분의 작업이 브라우저에서 이루어지고 변경해야 할 페이지의 일부만 새로 고쳐지는 SPA(Single-PageApplications)를 구축할 수 있다.

##### 첫 번째 React 컴포넌트

- npm install webpack babel-core babel-loader babel-preset-es2015
- npm install react react-dom babel-preset-react

```javascript

const React = require('react');

const books = [
    'Dubliners',
    'A Portrait of the Artist as a Young Man',
    'Exiles and poetry',
    'Ulysses',
    'Finnegans Wake'
];

class JoyceBooks extends React.Component {
    render() {
        return (
            <div>
                <h2>James Joyce's major works</h2>
                    <ul className="books">{
                        books.map((book, index) =>
                            <li classNeme="book" key={index}>{book}></li>
                        )
                    }
                    </ul>
            </div>
        );
    }
}

```

##### JSX

- JavaScript를 실행하기 위해 일반 JavaScript로 변환해야 하는 JavaScript의 슈퍼셋이다.
- JavaScript 구문의 일부를 보는 것처럼 JSX 코드의 중간에 직접 HTML 태그를 넣을 수 있다.

##### JSX 변환을 위한 Webpack 설정

```javascript

const path = require('path');
module.exports = {
    entry: path.join(__dirname, "src", "main.js"),
    output: {
        path: path.join(__dirname, "dist"),
        filename: "bundle.js"
    },
    module: {
        loaders: [
            {
                test: path.join(__dirname, "src"),
                loader: 'babel-loader',
                query: {
                    cacheDirectory: 'babel_cache',
                    presets: ['es2015', 'react']
                }
            }
        ]
    }
};

```

##### 브라우저에서 렌더링하기

```javascript
//src/main.js

const React = require('react');
const ReactDOM = require('react-dom');
const JoyceBooks = require('./joyceBooks');

window.onload = () => {
    ReactDOM.render(<JoyceBooks/>, document.getElementById('main'))
};

```

```javascript
//index.html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8" />
        <title>React Example - James Joyce books</title>
    </head>
    <body>
        <div id="main"></div>
        <script src="dist/bundle.js"></script>    
    </body>
</html>

```

##### React Router 라이브러리

```javascript
// src/components/authorsIndex.js

const React = require('react');
const Link = require('react-router').Link;

const authors = [
    {id: 1, name: 'James Joyce', slug: 'joyce'},
    {id: 2, name: 'Herbert George Wells', slug: 'h-g-wells'}
];

class AuthorsIndex extends React.Component {
    render() {
        return (
            <div>
                <h1>List of authors</h1>
                <ul>{
                    authors.map( author =>
                        <li key={author.id}><Link to={`/author/${author.slug}`}>
                                {author.name}</Link></li>    
                    )
                }</ul>
            </div>
        )
    }
}

module.exports = AuthorsIndex;

```

```javascript
// components/joyceBooks.js

const React = require('react');
const Link = require('react-router').Link;

const books = [
    'Dubliners',
    'A Portrait of the Artist as a Young Man',
    'Exiles and poetry',
    'Ulysses',
    'Finnegans Wake'
];

class JoyceBooks extends React.Component {
    render() {
        return (
            <div>
                <h2>James Joyce's major works</h2>
                <ul className="books">{
                    books.map( (book, key) =>
                        <li key={key} className="book">{book}</li>
                    )
                }</ul>
                <Link to="/">Go back to index</Link>
            </div>
        );
    }
}

module.exports = JoyceBooks;

```

```javascript
// components/wellsBooks.js

const React = require('react');
const Link = require('react-router').Link;

const books = [
    'The Time Machine',
    'The War of the Worlds',
    'The First Men in the Moon',
    'The Invisible Man'
];

class WellsBooks extends React.Component {
    render() {
        return (
            <div>
                <h2>Herbert George Wells's major works</h2>
                <ul className="books">{
                    books.map( (book, key) =>
                        <li key={key} className="book">{book}</li>
                    )
                }</ul>
                <Link to="/">Go back to index</Link>
            </div>
        );
    }
}

module.exports = WellsBooks;

```

```javascript
// routes.js

const React = require('react');
const ReactRouter = require('react-router');
const Router = ReactRouter.Router;
const Route = ReactRouter.Route;
const hashHistory = ReactRouter.hashHistory;
const AuthorsIndex = require('./components/authorsIndex');
const JoyceBooks = require('./components/joyceBooks');
const WellsBooks = require('./components/wellsBooks');
const NotFound = require('./components/notFound');

class Routes extends React.Component {
    render() {
        return (
            <Router history={hashHistory}>
                <Route path="/" component={AuthorsIndex}/>
                <Route path="/author/joyce" component={JoyceBooks}/>
                <Route path="/author/h-g-wells" component={WellsBooks}/>
                <Route path="*" component={NotFound} />
            </Router>
        )
    }
}

module.exports = Routes;

```


