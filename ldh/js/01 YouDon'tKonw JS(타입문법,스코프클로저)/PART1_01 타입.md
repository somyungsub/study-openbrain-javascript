# PART1 01 타입
* * *

## 내장타입 (7가지)

1. null
2. undefined
3. boolean
4. number
5. string 
6. object
7. symbol

###### JavaScript에는 위와 같은 7가지 내장 타입(원시 타입)이 있다. 
###### Symbol 타입은 ES6에서 추가 된 유일한 값을 가지는 타입이다. (자세한 내용은 뒷장에서 다룸.)

* * *

### typeof 연산자

- 값의 타입은 typeof 연산자로 알 수 있다.

- 다른 타입과 다르게 null 값은 정확히 확인하려면 조건이 하나 더 필요하다.

```javascript

var a = null;
(!a && typeof a === "object"); // true

// null은 'falsy'한 유일한 원시 값이면서, 타입은 'object'인 특별한 존재이다.

```

- typeof가 반환하는 문자열은 하나 더 있다.

```javascript

typeof function a(){/*...*/} === "function"; // true

// typeof 반환 값을 보면 마치 function이 최상위 레벨의 내장 타입처럼 보이지만 명세를 읽어보면 실제로는 object의 '하위 타입'이다. 구체적으로 설명하면 함수는 '호출 가능한 객체'라고 명시되어 있다.

```

- 배열의 typeof

```javascript

typeof [1,2,3] === "object"; // true

// 객체이다. 숫자 인덱스를 가지며, length 프로퍼티가 자동으로 관리되는 등의 추가 특성을 지닌, 객체의 '하위 타입'이라 할 수 있다.

```

> 값에 타입이 있고, 변수엔 타입이 없다. 따라서 typeof 연산자를 대어보는 건 "이 변수에 들어있는 값의 타입은 무엇이니?" 라고 묻는 것이다.

* * *

## 값이 없는 vs 선언되지 않은

##### 개념

* 값이 없는 변수의 값은 undefined이며, typeof 결과는 "undefined" 이다.
* 값이 없는 : 접근 가능한 스코프에 변수 선언, 현재 아무런 값도 할당되지 않음
* 선언되지 않은 : 접근 가능한 스코프에 변수 자체가 선언조차 되지 않음

위 둘은 엄연히 다른 개념

```javascript

var a;

a; // undefined
b; // ReferenceError: b is not declared

typeof a; // "undefined"
typeof b; // "undefined"

```

- 선언되지 않은 변수도 typeof하면 "undefined" 로 나온다. b는 분명 선언조차 하지 않은 변수인데, 브라우저는 오류 처리를 하지 않는다.

##### 안전가드

- typeof의 선언되지 않은 변수를 ReferenceError가 나지 않게 하는 안전가드를 이용하여, 선언되지 않은 변수인지 체크할 때 사용시 유용하다. (특히 라이브러리를 불러오는 경우 등.)

```javascript

// 안전가드 example

if(anyValue) {
 console.log("anyValue :"+anyValue); // anyValue가 선언되지 않았을 때, Error 발생할 수 있음
}

if(typeof anyValue !== "undefined") {
 console.log("anyValue :"+anyValue);
}

// 디버깅이나, API 기능체크 등의 경우에 사용가능

```

##### window 변수를 이용한 전역 변수

```javascript

if(window.anyValue) {
 console.log("anyValue :"+anyValue); // anyValue가 선언되지 않아도 에러가 발생하지 않음.
}

// window 객체를 통한 변수 참조는 가급적 삼가는 것이 좋다. 특히 다중 자바스크립트 환경이라면 더욱 그렇다.

```
