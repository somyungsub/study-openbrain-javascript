# PART1 02 this가 이런 거로군!

### 호출부

호출부는 현재 실행 중인 함수 '직전'의 호출 코드 '내부'에 있다.
```javascript
function baz() {
    
    console.log("baz");
    bar();
}
function bar() {
    
    console.log("bar");
    foo();
}
function foo() {
    
    console.log("foo");
    
}

baz();
```

### 기본바인딩

기본 바인딩 적용시 전역객체 프로퍼티 생성. this는 전역 객체를 참조

```javascript
function foo(){
    console.log(this.a);
}

var a = 2;

foo();
```
단, 함수 내부에서 엄격 모드이면 에러 발생

### 암시적바인딩

호출부에 콘텍스트 객체, 객체의 소유/포함 여부 확인

```javascript
function foo(){
    console.log(this.a);
}

var obj = {
    a : 2,
    foo : foo
};

obj.foo();
```

```javascript
function foo() {
    console.log(this.a);
}

var obj2 = {
    a : 42,
    foo : foo
};

var obj1 = {
    a : 2,
    obj2 : obj2
};

obj1.obj2.foo();
```

**암시적 소실**
암시적으로 바인딩 된 함수에서 바인딩이 소실되는 경우가 있음. this 바인딩이 헷갈리기 쉬운 경우.
```javascript
function foo() {
    console.log( this.a );
}

var obj = {
    a : 2,
    foo: foo
};

var bar = obj.foo;

var a = "전역";

bar();
```

```javascript
function foo(){
    console.log(this.a);
}

function doFoo(fn){
    fn();
}

var obj = {
    a : 2,
    foo : foo
};

var a = "전역";

doFoo(obj.foo);
```

```javascript
function foo(){
    console.log(this.a);
}

var obj = {
    a : 2,
    foo : foo
};

var a = "전역";

setTimeout(obj.foo, 100);
```

### 명시적바인딩

call(), apply() 메서드 이용

```javascript
function foo() {
    console.log( this.a );
}

var obj = {
    a : 2
};

foo.call(obj);

```

객체 대신 단순 원시 값을 전달하면 객체로 래핑됨(=박싱)

**하드 바인딩**

함수 내부에 call메서드를 이용하여 강제로 바인딩시킴.

```javascript
function foo() {
    console.log( this.a );
}

var obj = {
    a: 2
}

var bar = function() {
    foo.call(obj);
};

bar();
setTimeout( bar, 100);

bar.call( window );
```



### new 바인딩

new 를 붙여 생성자 호출 시
- 새 객체 생성
- 새로 생성된 객체의 [[Prototype]]이 연결됨
- 새로 생성된 객체는 해당 함수 호출 시 this로 바인딩 됨.
- 이 함수가 자신의 또 다른 객체를 반환하지 않는 한 new와 함께 호출된 함수는 자동으로 새로 생성된 객체를 반환

```javascript
function foo(a) {
    this.a = a;
}
var bar = new foo(2);
console.log(bar.a);
```

foo호출 시 this에 바인딩.

### 순서

1. new 바인딩
2. 명시적 바인딩
3. 암시적 바인딩
4. 기본 바인딩

### 바인딩 예외

- call, apply, bind 메서드의 첫 번째 인자로 null, undefined를 넘기면 this 바인딩이 무시되고 기본 바인딩 규칙이 적용.
```javascript
function foo() {
    console.log(this.a);
}

var a = 2;

foo.call(null);
```

```javascript
function foo() {
    console.log(this.a);
}

var a = 2;

var undefined = {
    a : 2
} 
foo.call(undefined);
```

##### DMZ 객체

##### 간접 레퍼런스

무조건 기본 바인딩 규칙 적용.

##### 화살표 함수

- 표준 바인딩 규칙을 무시하고 렉시컬 스코프로 this를 바인딩
- this를 확실히 보장하는 수단.
