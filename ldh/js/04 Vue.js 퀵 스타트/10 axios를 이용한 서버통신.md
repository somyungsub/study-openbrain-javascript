# axios를 이용한 서버통신

- Vue.js 서버통신 라이브러리 : fetch, superagent, axios
- vue-resource 보다 [axios를 권장하는 이유](https://medium.com/the-vue--point/retiring-vue-resource-871a82880af4)

### 서비스 API

- [GitHub 소스](https://github.com/stepanowon/contactsvc)

![연락처 서비스](./img/10-1.jpg)

![연락처 서비스 API 목록](./img/10-2.jpg)

![axios 기능 테스트](./img/10-3.jpg)

![axios npm install](./img/10-4.jpg)

### HTTP 프록시 설정

- SOP

![SOP 보안 정책](./img/10-5.jpg)

- 컨슈머 서버 프록시

![컨슈머 서버 프록시](./img/10-6.jpg)

- Vue.js webpack 템플릿에서 컨슈머 서버 프록시 기능을 제공.

- 템플릿의 config 디렉터리의 index.js 파일 설정

```javascript

module.exports = {

//......(생략)
                  
  dev: {

    // Paths
    assetsSubDirectory: 'static',
    assetsPublicPath: '/',
    proxyTable: {

      '/api' : {
        target: 'http://localhost:3000',
        changeOrigin:true,
        pathRewrite: {
          '^/api':''
        }
      }

    },
//......(생략)
        

```

### axios 사용

```javascript
- npm : npm install --save axios
- CDN : <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
```

```javascript

<template>
    <div id="app">
        <div class="container">
            <div class="form-group">
                <button @click="fetchContacts">1페이지 연락처 조회</button>
            </div>
            <div  class="form-group">
                <input type="text" v-model="name" placeholder="이름을 입력합니다" />
                <input type="text" v-model="tel" placeholder="전화번호를 입력합니다" />
                <input type="text" v-model="address" placeholder="주소를 입력합니다" />
                <button @click="addContact">연락처 1건  추가</button>
            </div>
            <div  class="form-group">
                <input type="text" v-model="no" /> <button @click="fetchContactOne">연락처 1건  조회</button>
            </div>
            <div  class="form-group">
                <input type="text" v-model="no" />
                <input type="text" v-model="name" placeholder="이름을 입력합니다" />
                <input type="text" v-model="tel" placeholder="전화번호를 입력합니다" />
                <input type="text" v-model="address" placeholder="주소를 입력합니다" />
                <button @click="updateContact">수정</button>
            </div>
            <div class="form-group">
                <input type="text" v-model="no" /> <button @click="deleteContact">삭제</button>
            </div>
            <div class="form-group">
                <input type="text" v-model="no" />
                <input type="file" ref="photofile" name="photo" /> 
                <button @click="changePhoto">파일 변경</button>
            </div>
        </div>
        <span>JSON 출력</span>
        <div id="result" class="container">
            <xmp>{{result}}</xmp>
        </div>
    </div>
</template>

<script>
import axios from 'axios';

export default {
    name : "app",
    data() {
        return {
            no : 0, name : '', tel:'', address:'',
            result : null
        }
    },
    methods : {
        fetchContacts : function() {
            axios({
                method : 'GET',
                url : '/api/contacts',
                params : { pageno : 1, pagesize:5 }
            })
            .then((response) => {
                console.log(response);
                this.result = response.data;
            })
            .catch((ex)=> {
                console.log("ERROR!!!! : ", ex);
            })
        },
        addContact : function() {
            axios.post('/api/contacts', { name:this.name, tel:this.tel, address:this.address })
            .then((response) => {
                console.log(response);
                this.result = response.data;
                this.no = response.data.no;
            })
            .catch((ex)=> {
                console.log("ERROR!!!! : ", ex);
            })
        },
        fetchContactOne : function() {
            axios.get('/api/contacts/'+this.no)
            .then((response) => {
                console.log(response);
                this.result = response.data;
            })
        },
        updateContact : function() {
            axios.put('/api/contacts/'+this.no, { name:this.name, tel:this.tel, address:this.address })
            .then((response) => {
                console.log(response);
                this.name = '';
                this.tel = '';
                this.address='';
                this.result = response.data;
            })
            .catch((ex)=> {
                console.log("ERROR!!!! : ", ex);
            })
        },
        deleteContact : function() {
            axios.delete('/api/contacts/'+this.no)
            .then((response) => {
                console.log(response);
                this.no = 0;
                this.result = response.data;
            })
            .catch((ex)=> {
                console.log("ERROR!!!! : ", ex);
            })
        },
        changePhoto : function() {
            var data = new FormData();
            var file = this.$refs.photofile.files[0];
            data.append('photo', file);

            axios.post('/api/contacts/' +this.no + '/photo', data)
            .then((response) => {
                this.result = response.data;
            })
            .catch((ex) => {
                console.log('updatePhoto failed', ex);
            });
        }
    }
}
</script>

<style>
@import url("https://cdn.bootcss.com/bootstrap/3.3.5/css/bootstrap.css");
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.container { border:solid 1px gray; padding:10px; margin-bottom:10px; text-align:left; }
#result { text-align: left ; padding:20px; border:solid 1px black; }
.form-group { border:dashed 1px gray; padding:5px 5px 5px 20px; }
</style>

```

![소스 실행 페이지](./img/10-7.jpg)

![콘솔 캡쳐](./img/10-8.jpg)

![불러온 데이터](./img/10-9.jpg)

![data이외의 속성](./img/10-10.jpg)

![속성 값 캡쳐](./img/10-11.jpg)

![연락처 추가](./img/10-12.jpg)

![파일 변경](./img/10-13.jpg)

![데이터 추가 캡쳐](./img/10-14.jpg)

![파일 확인 캡쳐](./img/10-15.jpg)

### axios 요청과 config 옵션

- baseURL, transformRequest, transformResponse, headers

### Vue 인스턴스에서 axios 이용하기

- main.js

```javascript

    import axios from 'axios'

    Vue.prototype.$axios = axios;

// axios -> this.$axios

```

### axios 사용 시 주의 사항


```javascript

    this.$axios.get('/api/contacts/' + this.no)
    .then((response) => {
        console.log(response);
        this.result = response.data;
    })

    // 클로저 접근

    var vm = this;
    this.$axios.get('/api/contacts/' + this.no)
    .then(function(response) {
        console.log(response);
        vm.result = response.data;
    })

```



