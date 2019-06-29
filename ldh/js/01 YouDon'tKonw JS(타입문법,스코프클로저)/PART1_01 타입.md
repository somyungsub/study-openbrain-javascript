# PART1 01 타입

### 내장타입 (7가지)
1. null
2. undefined
3. boolean
4. number
5. string 
6. object
7. symbol

### typeof

값 타입은 typeof 연산자로 알 수 있다.

다른 타입과 다르게 null 값은 정확히 확인하려면 조건이 하나 더 필요하다.

```javascript
var a = null;
(!a && typeof a === "object"); // true
```

typeof가 반환하는 문자열은 하나 더 있다.

```javascript
typeof function a(){/*...*/} === "function"; // true
```
typeof 반환 값을 보면 마치 function이 최상위 레벨의 내장 타입처럼 보이지만 명세를 읽어보면 실제로는 object의 '하위 타입'이다. 구체적으로 설명하면 함수는 '호출 가능한 객체'라고 명시되어 있다.

값에 타입이 있고, 변수엔 타입이란 없음

### undefined

##### 개념
* 값이 없는 : 접근 가능한 스코프에 변수 선언, 현재 아무런 값도 할당되지 않음
* 선언되지 않은 : 접근 가능한 스코프에 변수 자체가 선언조차 되지 않음

위 둘은 엄연히 다른 개념

##### 브라우저에서
* 값이 없는 : undefined
* 선언되지 않은 : Uncaught ReferenceError: b is not defined

######typeof 연산자에서
* 값이 없는 : "undefined"
* 선언되지 않은 : "undefined"

##### 안전가드

typeof의 선언되지 않은 변수를 ReferenceError가 나지 않게 하는 안전가드를 이용하여, 선언되지 않은 변수인지 체크할 때 사용시 유용하다. (특히 라이브러리를 불러오는 경우 등.)
```javascript
if(anyValue) {
 console.log("anyValue :"+anyValue); // anyValue가 선언되지 않았을 때, Error 발생할 수 있음
}

if(typeof anyValue !== "undefined") {
 console.log("anyValue :"+anyValue);
}
```