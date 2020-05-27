# PART1 03 네이티브
* * *

## 가장 많이 쓰는 네이티브

1. String()
2. Number()
3. Boolean()
4. Array()
5. Object()
6. Function()
7. RegExp()
8. Date()
9. Error()
10. Symbol()

- 네이티브란 특정 환경(브라우저 등의 클라이언트 프로그램)에 종속되지 않은, ECMAScript 명세의 내장 객체이다.

```javascript

var a = new String("abc");

typeof a; // "object"

a instanceof String; // true

Object.prototype.toString.call(a); // "[object String]"

```

- 생성자의 결과는 원시 값 "abc"를 감싼 객체 래퍼다.

* * *

### 내부[[Class]]

- typeof가 'object'인 값에는 [[Class]]라는 내부 프로퍼티가 추가로 붙는다. 이 프로퍼티는 직접 접근할 수 없고 Object.prototype.toString()라는 메서드에 값을 넣어 호출함으로써 존재를 엿볼 수 있다.

```javascript

// [[Class]] 값이, 배열은 "Array", 정규식은 "RegExp"임을 알 수 있다.
Object.prototype.toString.call([1,2,3]); // "[Object Array]"
Object.prototype.toString.call(/regex-literal/i); // "[Object RegExp]"

// 대부분 내부[[Class]]는 해당 값과 관련된 내장 네이티브 생성자를 가리키지만, 그렇지 않을 때도 있다.(null, undefined)
Object.prototype.toString.call(null); // "[Object Null]"
Object.prototype.toString.call(undefined); // "[Object Undefined]"

// 그 밖의 문자열, 숫자, 불리언 같은 단순 원시 값은 이른바 박싱 과정을 거친다.(객체 래퍼 자동박싱)
Object.prototype.toString.call("abc"); // "[Object String]"
Object.prototype.toString.call(42); // "[Object Number]"
Object.prototype.toString.call(true); // "[Object Boolean]"

```
* * *

## 래퍼 박싱

- 원시 값엔 프로퍼티나 메서드가 없으므로 .length, .toString()으로 접근하려면 원시 값을 객체 래퍼로 감싸줘야 한다. 하지만 자바스크립트는 원시 값을 알아서 박싱한다.

```javascript

var a = "abc";

a.length; // 3
a.toUpperCase(); // "ABC"

```

- 직접 객체 형태로 써야 할 이유는 거의 없다. 필요시 엔진이 알아서 암시적으로 박싱하게 하는 것이 낫다.

```javascript

// 그래도 객체 래퍼를 사용해야 한다면 조심해야 할 함정이 있다.
var a = new Boolean(false);

if (!a) {
    console.log("Oops"); // 실행되지 않는다.
}

// false를 객체 래퍼로 감쌌지만 문제는 객체가 'truthy'란 점이다. 그래서 예상과는 다른 결과다.
// 수동으로 원시 값을 박싱하려면 Object() 함수를 이용하자.

```

## 언박싱

- 객체 래퍼의 원시 값은 valueOf() 메서드로 추출한다.

```javascript

var a = new String("abc");
var b = new Number(42);
var c = new Boolean(true);

a.valueOf(); // "abc"
b.valueOf(); // 42
c.valueOf(); // true

// 암시적인 언박싱

var c = new String("abc");
var d = c + ""; // d에는 언박싱된 원시 값 "abc"가 대입된다.

```

* * *

## 객체 리터럴

- 배열, 객체, 함수, 정규식 값은 리터럴 형태로 생성하는 것이 일반적이지만, 리터럴은 생성자 형식으로 만든 것과 동일한 종류의 객체를 생성한다.

### Array()

```javascript

var a = new Array(1,2,3);
a; // [1, 2, 3]

var b = [1, 2, 3];
b; // [1, 2, 3]

// Array 생성자는 인자로 숫자를 하나만 받으면 그 숫자를 원소로 하는 배열을 생성하는 것이 아니라 배열의 크기를 미리 정한다.

var a = new Array(3);
var b = [ undefined, undefined, undefined ];
var c = [];
c.length = 3;

a.map(function(v,i){ return i;}); // [empty * 3]
b.map(function(v,i){ return i;}); // [0, 1, 2]
c.map(function(v,i){ return i;}); // [empty * 3]

a.join('-'); // [--]
b.join('-'); // [--]
c.join('-'); // [--]

// 빈 슬롯의 크기가 있는 배열이 생성되어, 이상한 일이 발생한다.
// 생성자는 가급적 쓰지 않는 편이 좋다. (별별 오류와 함정에 빠지지 않기를)

```

### Object(), Function(), and RegExp()

- Object()/Function()/RegExp() 생성자도 선택 사항이지만, 분명한 의도가 아니라면 사용하지 않는 편이 좋다.

```javascript

// 사용예시

var c = new Object();
c.foo = "bar";
c; // { foo: "bar" }

var d = { foo: "bar" };
d; // { foo: "bar" }

var e = new Function( "a", "return a * 2;" );
var f = function(a) { return a * 2; }
function g(a) { return a *2; }

var h = new RegExp( "^a*b+", "g" );
var i = /^a*b+/g;

```

### Date(), Error()

- 네이티브 생성자 Date()와 Error()는 리터럴 형식이 없으므로 다른 네이티브에 비해 유용하다.

```javascript

// Date() 생성자
var a = new Date(); // Wed May 27 2020 11:27:46 GMT+0900 (대한민국 표준시)
var b = Date.now(); // 1590546486335

// Error() 생성자
function foo(x) {
    if(!x) {
        throw new Error( "x를 안 주셨어요!" );
    }
}
// Error() 생성자는 앞에 new가 있든 없든 결과는 같다.
// error 객체는 보통 throw 연산자와 함께 사용한다.

```

### Symbol()

- Symbol은 ES6에서 처음 선보인, 새로운 원시 값 타입이다. 충돌 염려 없이 객체 프로퍼티로 사용 가능한, 특별한 '유일 값'이다.
- 프로그램 코드나 개발자 콘솔 창에서 Symbol의 실제 값을 보거나 접근하는 건 불가능하다.
- Symbol()은 앞에 new를 붙이면 에러가 나는, 유일한 네이티브 생성자다.

```javascript

var mysym = Symbol( "my own symbol" );
mysym; // Symbol(my own symbol)
mysym.toString(); // "Symbol(my own symbol)"
typeof mysym; // "symbol"

var a = {};
a[mysym] = "foobar";

a[mysym]; // "foobar"

// Symbol은 유일한 값이라서 발생하는 예시
var mysym2 = Symbol( "my own symbol" );
a[mysym2]; // undefined

```

### 네이티브 프로토타입

- 내장 네이티브 생성자는 각자의 .prototype 객체를 가진다.
- prototype 객체에는 해당 객체의 하위 타입별로 고유한 로직이 담겨 있다.


