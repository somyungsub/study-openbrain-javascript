# PART1 02 값
* * *

## 배열

* 자바스크립트 배열은 타입이 엄격한 다른 언어와 달리 문자열, 숫자, 객체 심지어 다른 배열이나 어떤 타입의 값이라도 담을 수 있는 그릇

```javascript

var a = [1, "2", [3]];

a.length; // 3
a[0] === 1; // true;
a[2][0] === 3; // true;

```

* 크기를 미리 정하지 않고도 선언 및 원하는 값 추가.

```javascript

var a = [];
a.length; // 0

a[0] = 1;
a[1] = "2";
a[3] = [3];

a.length; //3

```

* 구멍난 배열을 다룰 때 유의.

```javascript

a[0] = 1;
// a[1] 슬롯을 건너뜀.
a[2] = [3];

a[1]; // undefined

a.length; // 3

// 실행은 되지만 이런 코드에서 중간에 건너뛴 '빈 슬롯'은 혼란을 부추김.

```

* 배열 자체도 하나의 객체여서 키 / 프로퍼티 문자열을 추가 (typeof 배열 = "object")

```javascript

var a = [];

a[0] = 1;
a["foobar"] = 2;

a.length; // 1
a["foobar"]; // 2
a.foobar; // 2

// 키/프로퍼티 추가 시 length는 증가하지 않는다.

// 하지만 키로 문자열 값이 10진수 숫자로 타입이 바뀌면, 숫자 키를 사용한 것 같은 결과가 초래되어 정말 주의해야한다.
a.["13"] = 42;

a.length; // 14

// 배열에 키/프로퍼티룰 두는 건 추천X, 배열 원소의 인덱스는 확실히 숫자만 쓰자.

```

###### 유사 배열

- 배열처럼 []로 감싸져있지만 배열이 아닌 친구들을 유사배열이라고 함.
- 배열 유틸리티 함수(indexOf(), concat(), forEach() 등)를 사용하여 해결. (유사 배열은 배열 유틸리티 함수정도만 알고 넘어가자.)

* * *

## 문자열

* 문자열과 문자의배열은 다르다. 
* 문자열은 불변 값, 배열은 가변 값. 즉, 문자열은 내용을 변경하지 않고 항상 새로운 문자열을 생성 후 반환
* 문자열을 다룰 때 유용한 대부분의 배열 메서드는 사용 불가.

* * *

## 숫자

* 자바스크립트에서 정수와 부동 소수점 숫자를 모두 아우른다.

```javascript

// 10진수 리터럴로 표시.
var a = 42;
var b = 42.3;

// 소수점 앞 정수가 0이면 생략 가능.
var a = 0.42;
var b = .42;

// 소수점 이하가 0일 때도 생략 가능.
var a = 42.0;
var b = 42.;

// 대부분의 숫자는 10진수가 디폴트고 소수점 이하 0은 뗀다.

```

* Number 객체 래퍼로 박싱할 수 있기 때문에 Number.prototype 메서드로 접근 가능.

```javascript

//Number.prototype 메서드 Example

var a = 42.59;

// toFixed() 메서드로 지정한 소수점 이하 자릿수까지 숫자를 나타냄.
a.toFixed(0); // "43"
a.toFixed(1); // "42.6"
a.toFixed(2); // "42.59"
a.toFixed(3); // "42.590"
a.toFixed(4); // "42.5900"

// toPrecision() 메서드로 유효 숫자 개수를 지정할 수 있음.
a.toPrecision(1); // "4e+1"
a.toPrecision(2); // "43"
a.toPrecision(3); // "42.6"
a.toPrecision(4); // "42.59"
a.toPrecision(5); // "42.590"
a.toPrecision(6); // "42.5900"

// 큰 숫자는 지수형으로 표시
var onethousand = 1E3; // 1 * 10^3
var onemilliononehundredthousand = 1.1E6; // 1.1 * 10^6

// 숫자 리터럴은 2진, 8진, 16진 등 다른 진법으로도 나타낼 수 있다.
0xf3; // 243의 16진수
0363; // 243의 8진수
0b11; // 3의 2진수

```

#### 작은 소수 값

```javascript

0.1 + 0.2 === 0.3; // false
// 이유를 간단히 말하면, 0.1과 0.2는 원래의 숫자와 일치하지 않아서 둘을 더한 결과는 0.30000000000000004에 가깝다.
// '가깝다고' 해도 '같은' 것은 아니다.

```

###### 작은 수 비교 방법

- 일반적으로 미세한 '반올림 오차'를 허용 공차로 처리하는 방법이 있다. 이렇게 미세한 오차를 '머신 입실론'이라고 하는데, 자바스크립트 숫자의 머신 입실론은 2^-52다.

```javascript

if(!Number.EPSILON) {
    Number.EPSILON = Math.pow(2, -52);
}

function numbersCloseEnoughToEqual(n1, n2) {
    return Math.abs( n1 - n2 ) < Number.EPSILON;
}

var a = 0.1 + 0.2;
var b = 0.3;

numbersCloseEnoughToEqual(a, b); // true
numbersCloseEnoughToEqual(0.0000001, 0.0000002); // false

```

###### 정수인지 확인

```javascript

Number.isInteger(42); // true;
Number.isInteger(42.000); // true;
Number.isInteger(42.3); // false;

// 폴리필 (ES6 이전 버전)
if (!Number.isInteger) {
    Number.isInteger = function(num) {
        return typeof num == "number" && num % 1 == 0;
    };
}

// ES6부터 Number.isSafeInteger()로 체크

Number.isSafeInteger(Number.MAX_SAFE_INTEGER); // true
Number.isSafeInteger(Math.pow(2, 53)); // false
Number.isSafeInteger(Math.pow(2, 53) - 1); // true

// 폴리필
if (!Number.isSafeInteger){
    Number.isSafeInteger = function(num) {
        return Number.isInteger(num) &&
            Math.abs(num) <= Number.MAN_SAFE_INTEGER;
    };
}

// *폴리필 : 웹 개발에서 기능을 지원하지 않는 웹 브라우저 상의 기능을 구현하는 코드

```

* * *

## 특수 값

* undefined 타입의 유일한 값 : undefined
* null 타입의 유일한 값 : null

* undefined는 실종된 값, 즉 값을 아직 가지지 않은 것.
* null은 빈 값, 즉 예전에 값이 있었지만 지금은 없는 상태.

```javascript

function foo() {
    undefined = 2;
}

// null은 특별한 키워드이므로 null이라는 변수에 할당할 수 없지만, undefined는 할당가능 (느슨한 모드에서)
// 하지만 undefined 변수를 사용하는 것은 정말 비추천한다.

```

###### void 연산자

```javascript

var a = 42;

console.log(void a, a); // undefined 42
 
// void 연산자의 값은 항상 undefined
// void 연산자는 어떤 표현식의 결괏값이 없다는 걸 확실히 밝혀야 할 때 긴요하다.

```

## 특수 숫자

###### NaN

* NaN -> (숫자 아님, 하지만 타입은 number) 그래서 유효하지 않은 숫자, 실패한 숫자 등의 표현이 더 명확하다. 

```javascript

var a = 2 / "foo"; // NaN

typeof NaN; // "number"

// NaN은 다른 어떤 NaN과도 동등하지 않다. (심지어 자기 자신과도)

a === NaN; // false

// isNaN() 함수
isNaN(a); // true;

// 하지만 isNaN()은 인자 값이 숫자인지 여부를 평가
var b = "foo";
window.isNaN(b); // true
// "foo"는 숫자는 아니지만 그렇다고 NaN도 아니다.

// Number.isNaN() 함수 이용 (ES6 부터)
Number.isNaN(a); // true;

// 폴리필
if (!Number.isNaN) {
    Number.isNaN = function(n) {
        return (
            typeof n === "number" &&
            window.isNaN(n)
        );
    };
}

Number.isNaN("foo"); // false

```

###### 무한대

```javascript

// Number.POSITIVE_INFINITY
var a = 1 / 0; // Infinity
// Number.NAGATIVE_INFINITY
var b = -1 / 0; // -Infinity

// 무한대 나누기 무한대는 NaN
var c = Infinity / Infinity; // NaN

```

###### 영(0)

```javascript

var a = 0 / -3; // -0
var b = 0 * -3; // -0

// -0은 문자열화하면 항상 "0"이다.
a.toString(); // "0"

// 하지만 반대로 하면 -0으로 보여준다.
Number("-0"); // -0
JSON.parse("-0"); // -0

// 비교 연산 결과 역시 고의로 거짓말을 한다.
0 === -0; // true
0 > -0; // false

// 확실하게 0과 -0을 구분하고 싶다면 콘솔 창 결과에만 의존할 게 아니라 조금 더 영리해야 한다.

```

* * *

## 값 vs 레퍼런스

* 값의 타입만으로 값 복사, 레퍼런스 복사 둘 중 하나 결정.
    - null, undefined, string, number, boolean 그리고 symbol 같은 단순 값은 언제나 값-복사 방식으로 할당/전달된다.
    - 객체나 함수 등 합성 값은 할당/전달 시 반드시 레퍼런스 사본을 생성한다.
