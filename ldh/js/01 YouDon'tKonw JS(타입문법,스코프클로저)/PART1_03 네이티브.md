# PART1 03 네이티브

### 가장 많이 쓰는 네이티브
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

**네이티브는 생성자처럼 사용할 수 있지만, 실제 결과물은 Object이다.(typeof 연산자 이용)**

**그리고 생성자의 결과 값은 원시 값을 감싼 객체 래퍼.**

### 내부[[Class]]
- Object.prototype.toString()라는 메서드에 값을 넣어 호출함으로써 존재확인

### 래퍼 박싱
* new String(), new Number()보다는 직접 원시 값 이용 권장. 이유는 필요시 엔진이 알아서 박싱하는 것이 효율적.

__ 배열 생성 시 생성자를 이용하여 생성할 때도 주의할 점이 많기에, 원시 값으로 생성하는 것 권장. __

### 언박싱
* 객체 래퍼의 원시 값은 valueOf() 메서드로 추출.

```javascript
var a = new String("abc");
var b = new Number(42);
var c = new Boolean(true);

a.valueOf(); // "abc"
b.valueOf(); // 42
c.valueOf(); // true
```

### Object(), Function(), and RegExp()
- 생성자 폼으로 사용할 필요 X

### Date(), Error()
* 생성자 형식을 이용하는 것이 다른 네이티브에 비하여 유용(리터럴 형식이 없으므로)
    * new Date() -> 날짜와 시간을 인자로 받음
    * Error()는 new가 있든 없든 결과가 같다.

### Symbol()
* 객체 프로퍼티로 사용 가능한, 특별한 유일 값.
* 프로그램 코드나 개발자 콘솔 창에서 심벌의 실제 값을 보거나 접근은 불가능.
* Symbol() 네이티브를 이용하며, 앞에 new를 붙이면 에러가 나는, 유일한 네이티브 '생성자'

### 네이티브 프로토타입
* 내장 네이티브 생성자는 각자 .prototype 객체를 가짐.
* prototype 객체에는 해당 객체의 하위 타입별로 고유한 로직이 담겨 있음.

## 확인해야할 것.

* 심볼 타입과 네이티브 프로토타입 객체는 많이 활용해봐야 할 것.


