# PART2 04 제너레이터

### 완전-실행 타파

- 코드가 덩이 단위로 실행 (2부 1장 비동기성) -> 제너레이터라는 새로운 종류의 함수 등장(ES6 추가)

```javascript

var x = 1;

function foo() {
    x++;
    bar();
    console.log(x);
}

function bar() {
    x++;
}

foo(); // x: 3

```

```javascript

var x = 1;

function *foo() {
    x++;
    yield;
    console.log(x);
}

function bar() {
    x++;
}

var it = foo(); // 이터레이터를 선언하여 제너레이터를 제어.

it.next();
x; // 2
bar();
x; // 3
it.next() // 3

```

- 분명히 foo가 시작됐지만 '완전-실행'되지 않고 yield문에서 잠깐 멈춤.
- 나중에 foo()를 재개하여 완료하지만 꼭 이렇게 해야 할 필요는 없음. (반드시 끝까지 실행할 필요X)

### 입력과 출력

```javascript

function *foo(x,y) {
    return x * y;
}

var it = foo(6, 7);

var res = it.next();

res.value; // 42

```

- 제너레이터를 제어하는 이터레이터 객체를 변수에 할당하고 .next() 해야 제너레이터가 현재위치에서 다음 yield 또는 제너레이터의 끝까지 실행할 수 있음.
- next()의 결괏값은 *foo()가 반환한 값을 value 프로퍼티에 저장한 객체다. (즉, yield는 실행 도중에 제너레이터로부터 값을, 일종의 중간 반환 값 형태로 돌려줌.)


```javascript

function *foo(x) {
    var y = x * (yield);
    return y;
}

var it = foo(6);

it.next();

var res = it.next(7);
res.value; // 42

// next의 파라미터 참고.!

```

- 일반적으로 yield 개수보다 next() 호출이 하나 더 많다.
- 양방향 메시징 가능

```javascript

function *foo(x) {
    var y = x * (yield "hello");
    return y;
}

var it = foo(6);

var res = it.next();
res.value;

res = it.next(7);
res.value;

// next()와 next().value

```

- return 문이 따로 없으면 return문이 있다고 치고 암시적 처리.

### 다중 이터레이터

```javascript

function *foo() {
    var x = yield 2;
    z ++;
    var y = yield(x * z);
    console.log(x, y, z);
}

var z = 1;

var it1 = foo();
var it2 = foo();

var val1 = it1.next().value; // 2
var val2 = it2.next().value; // 2

val1 = it1.next(val2 * 10).value; // 40
val2 = it2.next(val1 * 5).value; // 600

it1.next(val2 / 2); // y:300
                // 20 300 3
it2.next(val1 / 4); // y:10
                // 200 10 3

```

#### 인터리빙


```javascript

var a = 1;
var b = 2;

function foo() {
    a++;
    b = b * a;
    a = b + 3;
}

function bar() {
    b--;
    a = 8 + b;
    b = a * 2;
}

```

```javascript

var a = 1;
var b = 2;

function foo() {
    a++;
    yield;
    b = b * a;
    a = (yield b) + 3;
}

function bar() {
    b--;
    yield;
    a = (yield 8) + b;
    b = a * (yield 2);
}

function step(gen) {
    var it = gen();
    var last;
    
    return function() {
        last = it.next(last).value;
    };
}

```

```javascript

a = 1;
b = 2;

var s1 = step(foo);
var s2 = step(bar);

s1();
s1();
s1();

s2();
s2();
s2();
s2();

console.log(a, b); // 11 22

```


```javascript

a = 1;
b = 2;

var s1 = step(foo);
var s2 = step(bar);

s2();
s2();
s1();
s2();

s1();
s1();
s2();

console.log(a, b); // 12 18

```

### 값을 제너레이터링

```javascript

var gimmeSomething = (function(){
    var nextVal;
    
    return function() {
        if(nextVal === undefined){
            nextVal = 1;
        }
        else {
            nextVal = (3 * nextVal) + 6;
        }
                       
       return nextVal;
    
    };

})();

gimmeSomething(); // 1
gimmeSomething(); // 9 
gimmeSomething(); // 33
gimmeSomething(); // 105
    
```

```javascript

var something = (function() {
    var nextVal;
    
    return {
        [Symbol.iterator]: function(){return this;},
        next: function(){
            if (nextVal === undefined) {
                nextVal = 1;
            }
            else {
                nextVal = (3 * nextVal) + 6;
            }
    
            return {done:false, value:nextVal};
        }
    }
})();

something.next().value; // 1
something.next().value; // 9
something.next().value; // 33
something.next().value; // 105

// [Symbol.iterator] ?

``` 

- for...of 루프 : 표준 이터레이터를 자동으로 기존 루프 구문 형태로 쓸 수 있다.
- for...of 루프는 매번 자동으로 next()를 호출하다가 done:true를 받으면 그 자리에서 멈춤. 데이터를 순회할 때 편리함.

```javascript

for (var v of something){
    console.log(v);
    
    if(v > 500){
        break;
    }
}
// 1 9 33 105 321 969


```

```javascript

var a = [1,3,5,7,9]

for (var v of a) {
    console.log(v);
}
// 1 3 5 7 9

```

- 손수 이터레이터를 제작해도 상관없지만 ES6부터 배열 같은 자바스크립트의 내장 자료 구조 대부분에는 기본 이터레이터가 장착되어 있다.


### 이터러블

- 이터러블 : next() 메서드로 인터페이스하는 객체와 순회 가능한 이터레이터를 포괄한 객체.
- ES6에서는 심볼값 Symbol.iterator라는 이름을 가진 함수를 지니고 있어야 이 함수를 호출하여 이터러블에서 이터레이터를 가져올 수 있다.

```javascript
[Symbol.iterator]: function(){ return this; }
```

### 제너레이터 이터레이터

- 제너레이터는 일종의 값을 생산하는 공장이며, 이렇게 만들어진 값들은 이터레이터 인터페이스의 next()를 호출하여 한번에 하나씩 추출.
- 엄밀히 말하면 제너레이터 자체는 이터러블이 아니지만 아주 흡사해서 제너레이터를 실행하면 이터레이터를 돌려받게 된다.?

```javascript

function *something() {
    var nextVal;
    
    while (true) {
        if (nextVal === undefined) {
            nextVal = 1;
        }
        else {
            nextVal = (3 * nextVal) + 6;
        }
    
        yield nextVal;
    }
}

```

- 스코프 그대로 유지, 클로저 구문X, 


```javascript

for (var v of something()){
    console.log(v);
    
    if(v > 500){
        break;
    }
}
// 1 9 33 105 321 969


```

```javascript

function *something() {
    try{
        var nextVal;

        while (true) {
            if (nextVal === undefined) {
                nextVal = 1;
            }
            else {
                nextVal = (3 * nextVal) + 6;
            }

            yield nextVal;
        }
   }
   finally {
        console.log("정리 완료!");
    }
}

```

- for...of 루프가 '비정상 완료'되면 제너레이터의 이터레이터를 중지하도록 신호를 줌.
- 제너레이터가 외부적으로 완료된 다음에도 내부에서 try...finally 절을 사용하면 실행가능.
- 자원을 정리할 때 유용.


- 외부에서 return()문을 써서 수동으로 제너레이터의 이터레이터 인스턴스를 멈출 수 있음.

```javascript

var it = something();
for (var v of it){
    console.log(v);
    
    if(v > 500){
        console.log(
            it.return("Hello World").value
        );
        break;
    }
}
// 1 9 33 105 321 969

```

- it.return()하면 제너레이터 실행은 즉시 끝나고 finally 절로 옮겨감.
- done:true 이므로 break 문을 넣지 않아도 된다.

### 제너레이터를 비동기적으로 순회

```javascript

function foo(x, y, cb) {
    ajax(
        "http://some.url.1/?x=" + x + "&y=" + y,
        cb
    );
}

foo( 11, 31, function(err, text) {
        if (err){
            console.log(err);        
        }
        else {
            console.log(text);
        }
    }
);

```

```javascript

function foo(x,y){
    ajax(
        "http://some.url.1/?x=" + x + "&y=" + y,
        function(err, data){
            if(err) {
                it.throw(err);
            }
            else {
                it.next(data);
            }
        }
    );
}

function *main() {
    try {
        var text = yield foo(11, 31);
        console.log(text);
    }
    catch(err) {
        console.error(err);
    }
}

var it = main();

it.next();

```

- 여기서 yield는 메시지 전달 수단이 아닌, 멈춤/중단을 위한 흐름 제어 수단으로 사용
- 동기적으로 작동할 것 같지만 그 속을 들여다보니 완전히 비동기적으로 작동.
- 흐름을 제어 가능

#### 동기적 에러 처리

- yield-멈춤 기능은 비동기 함수 호출로부터 넘겨받은 값을 동기적인 형태로 return하게 해줄 뿐만 아니라 에러를 동기적으로 catch 할 수 있다.



### 제너레이터 + 프라미스

```javascript

function foo(x,y) {
    return request(
        "http://some.url.1/?x=" + x + "&y=" + y
    );
}

foo( 11, 31 )
.then(
    function(text){
        console.log(text);
    },
    function(err){
        console.log(err)
    }
);


```

```javascript

function foo(x,y) {
    return request(
        "http://some.url.1/?x=" + x + "&y=" + y
    );
}

function *main(){
    function(text){
        var text = yield foo(11, 31);
        console.log(text);
    },
    function(err){
        console.log(err)
    }
}

var it = main();

var p = it.next().value;

// 'p' 프라미스가 귀결될 때까지 기다린다.
p.then(
    function(text){
        it.next(text);
    },
    function(err){
        it.throw(err);
    }
);

```

### 정리
- 제너레이터는 ES6부터 도입된 새로운 유형의 함수로, 일반 함수처럼 완전-실행하지 않고 실행 도중 멈출 수도 있고 멈춤 지점에서 나중에 다시 시작할 수도 있다.
- 제너레이터는 선점적이라기보다는 협동적인 툴이다.
- yield / next() 이중성은 제어 장치뿐 아니라 양방향 메시징 체계로 활용.
- 비동기 코드의 순차/동기/중단적 패턴을 유지함으로써 콜백식 비동기 코드의 치명적인 단점 해결



















