# 02 Vue.js 기초

### 기본 디렉티브

##### v-text

- innerText 속성에 연결됨. 태그 문자열을 HTML 인코딩하여 나타내며, 화면에 태그 문자열을 그대로 나타남.

##### v-html

- innerText 속성에 연결됨. 태그 문자열을 파싱하여 화면에 나타남.

##### v-bind

- 요소 객체의 속성들을 바인딩하기 위해 사용
- v-bind:속성="값" / ex) v-bind:src="imagePath"

##### v-model

- 양방향 데이터 바인딩이 필요한 경우 사용.

##### v-if, v-else, v-else-if

- 자바스크립트, 자바, C언어 등에서 사용하는 if문과 동일한 개념

##### v-show

- v-if 디렉티브는 조건에 부합되지 않으면 렌더링을 하지 않는 반면 v-show는 일단 요소를 렌더링한 후에 display 스타일 속성으로 화면에 보여주는 것을 결정.
- 자주 화면이 변경되는 부분에 대해서 바람직함.

##### v-for

- 자바스크립트 언어의 for문과 유사.

```html

<tr v-for="contact in contacts">
    <td>{{contact.no}}</td>
    <td>{{contact.name}}</td>
    <td>{{contact.tel}}</td>
    <td>{{contact.address}}</td>
</tr>

```

### 계산형 속성

- 연산 로직이 필요한 경우

```javascript

    var vmSum = new Vue({
        el : "#example",
        data : {num : 0}
        computed : {
            sum : function() {
                var n = Number(this.num);
                if(Number.isNaN(n) || n < 1) return 0;
                return ((1+n)*n) / 2;
            }
        }
    });

```