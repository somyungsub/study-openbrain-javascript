# PART1 06 작동 위임

### 위임 지향 디자인
- [[Prototype]]이 클래스와는 다른 디자인 패턴

```javascript

// 클래스 이론

class Task {
    id;
    
    // 'Task()' 생성자
    Task(ID) { id = ID; }
    outputTask() { output(id); }
}

class XYZ inherits Task {
    label;
    
    // 'XYZ()' 생성자
    XYZ(ID, Label) { super( ID ); label = Label; }
    outputTask() { super(); output( label ); }
}

class ABC inherits Task {
    // ...
}

```

- 클래스 디자인 패턴
    - 메서드를 오버라이드 할 것을 권장 (상속)
    - 공통요소는 추상화하여 부모 클래스의 일반 메서드로 구현 -> 자식 클래스는 이를 세분화

- 인스턴스가 생성되면 원하는 작동은 인스턴스에 모두 복사되어 옮겨진 상태이므로 일반적으로 오직 인스턴스와 상호 작용을 한 게 된다.

```javascript

// 위임 이론

Task = {
    setID: function(ID) { this.id = ID; },
    outputID : function() { console.log( this.id ); }
};

// 'XYZ'가 'Task'에 위임한다.
XYZ = Object.create( Task );

XYZ.prepareTask = function(ID,Label) {
    this.setID( ID );
    this.label = Label;
};

XYZ.outputTaskDetails = function(){
    this.outputID();
    console.log( this.label );
};

// ABC = Object.create( Task );
// ABC ... =  ...

```

- 작동위임 디자인 패턴
    - OLOO(객체를 다른 객체에 연결, Objects Linked Other Objects)
    - XYZ 객체가 Task 객체에 작동을 위임하는 부분만 신경 씀.
    - 추상화 체계 X
    - 작동 위임 패턴에서 서로 다른 수준의 [[Prototype]] 연쇄에서 같은 명칭이 뒤섞이는 일은 될 수 있으면 피해야 한다. (같은 이름끼리 충돌하면 레퍼런스를 정확히 가려낼 수 없는 부자연스럽고 취약한 구문이 만들어지며, 작동 위임 패턴에서는 오버라이드 하기 딱 좋은 일반적인 메서드 명칭보다는 각 객체의 작동 방식을 잘 설명하는 서술적인 명칭이 필요하다.) -> 코드의 가독성과 유지 보수성을 높임.

- 작동 위임 이란 찾으려는 프로퍼티/메서드 레퍼런스가 객체에 없으면 다른 객체로 수색 작업을 위임하는 것을 위미함.
    - 객체들이 서로 수평적으로 배열된 상태에서 위임 링크가 체결된 모습.

### 상호 위임

- 복수의 객체가 양방향으로 상호 위임을 하면 발생하는 사이클은 허용되지 않는다. (에러가 난다.)
- 모든 레퍼런스가 확실히 존재한다면 양방향 위임은 가능하다.

```javascript

// 상호 위임 예제

Task = {
    setID: function(ID) { this.id = ID; },
    outputID : function() { console.log( this.id ); }
};

// 'XYZ'가 'Task'에 위임한다.
XYZ = Object.create( Task );

XYZ.prepareTask = function(ID,Label) {
    this.setID( ID );
    this.label = Label;
};

Task.prepareTask('abc', 'abcL'); // Uncaught TypeError: Task.prepareTask is not a function

```

### 디버깅

```javascript

// 디버깅
function Foo() {}
var a1 = new Foo();

a1; // Foo {}

```

- 크롬 : Foo {}
    - {}는 Foo라고 명명된 함수가 생성한 빈 객체

- 파이어폭스 : Object {}
    - {}는 Object에 의해 일반적으로 생성된 빈 객체
    
- 사파리 : Foo

```javascript

function Foo() {}
var a1 = Object.create( Foo );

a1; // Object {}

Object.defineProperty ( Foo, "constructor", {
    enumerable: false,
    value: function Gotcha(){}
});

a1;

```

- 크롬 : Gotcha {}

- 파이어폭스 : Object {}

- 사파리 : Gotcha

- 크롬 콘솔창이 .constructor.name을 찾고 이용함. 이것이 크롬의 버그로 리포팅 됨


### 멘탈 모델 비교

```javascript

function Foo(who) {
    this.me = who;
}
Foo.prototype.identify = function() {
    return "I am " + this.me;
};

function Bar(who) {
    Foo.call( this, who );
}
Bar.prototype = Object.create( Foo.prototype );

Bar.prototype.speak = function() {
    alert( "Hello, " + this.identify() + "." );
};

var b1 = new Bar( "b1" );
var b2 = new Bar( "b2" );

b1.speak();
b2.speak();

```

```javascript

Foo = {
    init: function(who) {
        this.me = who;
    },
    identify: function() {
        return "I am " + this.me;
    }
};

Bar = Object.create( Foo );

Bar.speak = function() {
    alert( "Hello, " + this.identify() + "." );
};

var b1 = Object.create( Bar );
b1.init("b1");
var b2 = Object.create( Bar );
b2.init("b2");

b1.speak();
b2.speak();

```

- OLOO 스타일 코드로 인하여 간단해짐.

- call(), apply(), bind() 메서드에 접근할 수 있는 건 [[Prototype]]이 연결되어 위임 가능한 기본 메서드가 있기에 가능함.

### 인트로스펙션

```javascript

// 인트로스펙션

function Foo() {
    // ...
}
Foo.prototype.somthing = function() {
    // ...
}

var a1 = new Foo();

if (a1 instanceof Foo) {
    a1.something();
}

```

```javascript

function Foo() { }

function Bar() { }
Bar.prototype = Object.create( Foo.prototype );

var b1 = new Bar( "b1" );

Bar.prototype instanceof Foo; // true
Object.getPrototypeOf( Bar.prototype ) === Foo.prototype; // true
Foo.prototype.isPrototypeOf( Bar.prototype ); // true
b1 instanceof Foo; // true
b1 instanceof Bar; // true
Object.getPrototypeOf( b1 ) === Bar.prototype; // true
Foo.prototype.isPrototypeOf( b1 ); // true
Bar.prototype.isPrototypeOf( b1 ); // true

```


### 정리

- 클래스와 상속은 선택해도, 선택하지 않아도 되는 디자인 패턴 중 1개
- 자바스크립트의 [[Prototype]]은 태생적으로 작동 위임 체계
- 자바스크립트에 억지로 클래스 체계를 얹어 구현할 필요X

**객체만으로 구성된 코드를 구성한다면 사용 구문도 단순해지고, 실제로 코드 아키텍처 또한 더 간단하게 가져갈 수 있다.**