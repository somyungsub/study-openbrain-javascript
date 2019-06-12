03.네이티브

이 얘기가 왜 나왔을까

```javascript
// 에러 케이스
10.toString(); // SyntaxError: Invalid or unexpected token

// 하지만
var a = 10;
a.toString(); // '10'

Number(10).toString(); // '10
a === Number(10); // true

// 근데 또
a === 10; // true

// 그렇다면 어떻게 해야
// a === 10 (Number(10) === 10)이 false가 나오게 할 수 있을까?
```