# 01 Node.js 플랫폼에 오신 것을 환영합니다

### Node.js 철학

- Node.js의 철학은 JavaScript 문화와 Unix 철학으로 부터 영향을 받음

##### 경량 코어

- 최소의 기능 세트, 나머지는 사용자의 몫
- 핵심 기능 세트를 최소한으로 유지하면, 유지 보수 측면에서 편리하고, 전체 생태계의 발전에 긍정적인 문화적 영향을 가져옴

##### 경량 모듈

- 모듈 개념을 프로그램 코드를 구성하는 기본 수단으로 사용 => 어플리케이션과 패키지라고 하는 재사용 가능한 라이브러리를 만들기 위한 블록
- 유닉스의 철학을 뿌리를 둠 => "작은 것이 아릅답다", "각 프로그램이 각기 한 가지 역할을 잘 하도록 만든다"
- 작고 잘 집중화된 수많은 의존성들로 구성하여 재사용성을 극도로 높임 => 이해하기 쉽고, 사용하기 쉽다, 테스트와 유지보수가 간단하다, 브라우저와 완벽한 공유 가능
- DRY(Don't Repeat Yourself) 원칙 적용

##### 작은 외부 인터페이스

- 최소한의 기능 노출 => API 유용성이 향상된다 => API 사용이 보다 명확해지고 잘못된 사용에 덜 노출된다
- 명백한 단일 진입점 제공 => 하나 이외의 다른 기능은 존재하지 않는다
- 확장 용도 보다 실제 사용하도록 만듬

##### 간결함과 실용주의

- 리차드 가브리엘 : "불완전 한 것이 더 낫다"
- 구현하는데 소요되는 노력과 자원을 적게 사용하여 더 빨리 보급할 수 있고 적응과 유지보수 및 이해가 쉽다
- Node.js의 이러한 원칙이 JavaScript에 의해 가능하다


### Node.js 6와 ES2015에 대한 소개

##### let과 const 키워드

```javascript

if(false) {
    var x = 'hello';
}
console.log(x);

// undefined


if(false) {
    let x = 'hello';
}
console.log(x);

// ReferenceError: x is not defined

```

- let과 함께 도입된 이 보호 동작을 통해 더 안전한 코드를 작성

```javascript

const x = 'This will never change';
x = '...';

// TypeError: Assignment to constant variable

const x = {};
x.name = 'John';

```

- 객체 내부에서 속성을 변경하면, 실제 값이 변경되지만 변수와 객체 사이의 바인딩은 변경되지 않음, 반대로 전체 변수를 재할당하면 변수와 값 사이의 바인딩이 변경되어 오류 발생
- 상수는 코드 내에서 스칼라 값이 여러분의 실수로 변경되지 않도록 보호하려 하거나, 할당된 변수를 재할당하지 않도록 하는데 유용합니다

##### 화살표 함수

```javascript

const numbers = [2, 6, 7, 8, 1];
const even = nembers.filter(function(x) {
    return x%2 === 0;
});

// 화살표 함수 구문을 사용하여 재 작성

const numbers = [2, 6, 7, 8, 1];
const even = nembers.filter(x => x%2 === 0);

```

- function 키워드는 제거되고 =>(화살표) 다음에 매개변수 목록만 남기고 바로 함수 본문이 나옴
- 함수의 본문이 한 줄일 경우, 암시적으로 return 키워드 적용

```javascript

DelayedGreeter.prototype.greet = function() {
    setTimeout( (function cb() {
        console.log('Hello' + this.name);
    }).bind(this), 500);
};

// 화살표 함수 이용

DelayedGreeter.prototype.greet = function() {
    setTimeout( () => console.log('Hello' + this.name), 500);
};

```

- 화살표 함수가 도입되어 어휘 범위에 바인딩 되기 때문에 화살표 함수를 콜백 함수로 사용

##### 클래스 구문

```javascript

function Person(name, surname, age) {
    this.name = name;
    this.surname = surname;
    this.age = age;
}

Person.prototype.getFullName = function() {
    return this.name + '' + this.surname;
};

Person.older = function(person1, person2) {
    return (person1.age >= person2.age) ? person1 : person2;
};


// 클래스 구문을 사용하여 구현

class Person {
    constructor (name, surname, age) {
        this.name = name;
        this.surname = surname;
        this.age = age;
    }

    getFullName () {
        return this.name + ' ' + this.surname; 
    }
    
    static older (person1, person2) {
        return (person1.age >= person2.age) ? person1 : person2;
    }
}

```

- 클래스 구문을 이용하면 생성자를 명시적으로 기술하고 정적 함수(static) 선언 가능하다

```javascript

class PersonWithMiddlename extends Person {
    constructor (name, middlename, surname, age) {
        super(name, surname, age);
        this.middlename = middlename;
    }
    
    getFullName (){
        return this.name + '' + this.middlename + '' + this.surname;
    }
}

```

- 확장하고자 하는 클래스로부터 새로운 클래스를 선언하고, super 키워드를 사용하여 부모 생성자를 호출하는 새로운 생성자를 정의, 함수 오버라이드 이용

##### 향상된 객체 리터럴

- ES2015에서 향상된 객체 리터럴 표기법 소개, 변수 및 함수를 객체의 멤버로 지정하고, 객체를 생성할 때 동적인 멤버명을 정의할 수 있도록 하며, 편리한 setter 및 getter 함수들을 제공

```javascript

// 변수를 가지는 객체
const x = 22;
const y = 17;
const obj = {x, y};

// 함수를 가지는 객체
module.exports = {
    square (x) {
        return x * x;
    },
    cube (x) {
        return x * x * x;
    } 
};

// 동적으로 속성 명 사용
const namespace = '-webkit-';
const style = {
    [namespace + 'box-sizing'] : 'border-box',
    [namespace + 'box-shadow'] : '10px10px5px #888888'
};

// setter 및 getter 구문
const person = {
    name : 'George' ,
    surname : 'Boole',
    
    get fullname () {
        return this.name + '' + this.surname;
    },
    
    set fullname (fullname) {
        let parts = fullname.split('');
        this.name = parts[0];
        this.surname = parts[1];
    }
};

console.log(person.fullname); // "George Boole"
console.log(person.fullname = 'Alan Turing'); // "Alan Turing"
console.log(person.name); // "Alan"

```

##### Map과 Set Colletion

```javascript

const profiles = new Map();
profiles.set('twitter', '@adalovelace');
profiles.set('facebook', 'adalovelace');
profiles.set('googleplus', 'ada');

profiles.size; //3
profiles.has('twitter'); //true
profiles.get('twitter'); //"@adalovelace"
profiles.has('youtube'); //false
profiles.delete('facebook');
profiles.has('facebook'); //false
profiles.get('facebook'); //undefined
for (const entry of profiles){
    console.log(entry);
}

```

- Map 프로토타입에는 set, get, has, delete와 같은 편리한 메소드와 size 속성 제공, for 구문을 사용하여 모든 항목을 반복 가능하다

```javascript

// 함수와 객체를 Map의 키로 사용 가능
const tests = new Maps();
tests.set(() => 2+2, 4);
tests.set(() => 2*2, 4);
tests.set(() => 2/2, 1);

for (const entry of tests) {
    console.log((entry[0]() === entry[1]) ? 'PASS' : 'FAIL');
}

```

- 함수를 키로, 예상되는 결과를 값으로

```javascript

const s = new Set([0, 1, 2 ,3]);
s.add(3); //추가되지 않음
s.size; //4
s.delete(0);
s.has(0) //false

for (const entry of s) {
    console.log(entry);
}

```

- Map에서 본 것과 유사, add와 has, delete 함수와 size 속성이 존재

##### WeakMap 및 WeakSet Collection

- WeakMap은 Map과 매우 유사하지만, 요소 전체를 반복 구문으로 탐색할 방법이 없고, 객체만을 키로 가질 수 있다

```javascript

let obj = {};
const map = new WeakMap();
map.set(obj, {key: "some_value"});
console.log(map.get(obj)); //{key: "some_value"}
obj = undefined; //다음 가비지 컬렉트 사이클에서 맵에 관련된 객체와 데이터가 정리됨

```

```javascript

let obj1 = {key: "val1"};
let obj2 = {key: "val2"};
const set = new WeakSet([obj1, obj2]);
console.log(set.has(obj1)); //true
obj1 = undefined; //이제 obj1이 set에서 제거됨
console.log(set.has(obj1)); //false

```

- WeakMap과 WeakSet이 Map과 Set 보다 좋거나 나쁜 것이 아니라 사용처가 다르다

##### Template 표기법

```javascript

const name = "Leonardo";
const interests = ["arts", "architecture", "science", "music", "mathematics" ];
const birth = { year : 1452, place : 'Florence'};
const text = '${name} was an Italian polymath
    interested in many topics such as
    ${interests.join(', ')}.He was born
    in ${birth.year} i
console.log(text);

```

- 템플릿 표기 구문을 문자열 내에서 ${expression}의 형식으로 사용하여 변수 또는 표현식을 삽입할 수 있다

##### ES2015의 기타 기능

- 기본 매개 변수, 나머지 매개 변수, 전개 연산자, 비구조화, new.target, Proxy, Reflect, Symbols

### Reactor 패턴

- Node.js의 비동기 특성의 핵심 패턴







