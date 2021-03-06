# PART1 04 강제변환

### 값 변환
* 명시적 강제변환

    - 다른 타입의 값으로 변환하는 의도가 확실한 코드.
    - 혼동의 여지가 적고, 가독성과 유지 보수성을 높임.

* 암시적 강제변환

    - 타입변환이 처리되는 과정이 명확하지 않음.
    - 변환 과정이 구체적으로 어떻게 일어나는지 명확하게 알고 사용해야 함.

#### ToString

- 문자열이 아닌 값을 문자열로 변환하는 작업
    - null -> "null"
    - undefined -> "undefined"
    - true -> "true"
    - 숫자는 그대로 문자열로 변환. 단, 너무 작거나 큰 값은 지수 형태로 바뀜

#### ToNumber

- 숫자 아닌 값을 수식 연산이 가능한 숫자로 변환하는 작업
    - true -> 1, false -> 0
    - undefined -> NaN
    - null -> 0
    - 문자열 값을 숫자 변환 실패 시 NaN

#### ToBoolean

- falsy 값
    - undefined
    - null
    - false
    - +0, -0, NaN
    - ""
- truthy 값
    - 위의 falsy 값 목록에 없는 값

#### 명시적 강제변환
- 문자열 <-> 숫자
    - String()과 Number() 이용 (앞에 new 키워드가 붙지 않기 때문에 객체 래퍼를 생성하는 것이 아님)
    - toString() 과 + 단항 연산자
- 날짜  <-> 숫자
    - new Date(), + 단항 연산자
    - Date 객체의 getTime(), now() 등의 메서드 이용 (권장)
- 비 불리언 -> 불리언
    - Boolean()
    - ! 단항 연산자 (= 부정 단항 연산자), !!
    
#### 암시적 변환
- 암시적 강제변환은 부수 효과가 명확하지 않게 숨겨진 형태로 일어나는 타입변환이다.

#### 느슨한/엄격한 동등 비교
- 느슨한 동등 비교 : == (값의 동등함)
- 엄격한 동등 비교 : === (값과 타입 모두의 동등함)

#### 추상 관계 비교
