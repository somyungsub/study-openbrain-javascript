# 09 익스프레스로 SNS 서비스 만들기

### 프로젝트 구조 갖추기

- Express-generator를 이용하지 않고 직접 생성.(파일과 폴더)
- 패키지 설치, 구조에 필요한 파일 생성, pug 작성
 * express / cookie-parser / express-session / morgan / connect-flash / pug
 * nodemon (서버 자동 재시작) / .env(프로퍼티 값 저장) 

### 데이터베이스 세팅

- models 하위에 user, post, hashtag 생성

```
config/config.js 파일 (DB정보 Setting)

{
  "development": {
    "username": "daepang",
    "password": "root1234",
    "database": "db_daepang",
    "host": "127.0.0.1",
    "dialect": "mysql",
    "operatorsAliases": false
  },
  "test": {
    "username": "root",
    "password": null,
    "database": "database_test",
    "host": "127.0.0.1",
    "dialect": "mysql",
    "operatorsAliases": false
  },
  "production": {
    "username": "root",
    "password": null,
    "database": "database_production",
    "host": "127.0.0.1",
    "dialect": "mysql",
    "operatorsAliases": false
  }
}

```

- sequelize db:create

### Passport

- 세션과 쿠키 처리등 복잡한 작업이 많으므로 검증된 모듈 사용.(Passport)

```
npm i passport passport-local passport-kakao bcrypt
```

```
app.use(passport.initialize()); // req 객체에 passport 설정을 심고
app.use(passport.session());    // req.session 객체에 poassport 정보를 저장한다.

```

##### serializeUser
- req.session 객체에 어떤 데이터를 저장할 지 선택.

##### deserializeUser
- 매 요청시 실행.
- serializeUser에서 세션에 저장했던 아이디를 받아 DB에서 조회

##### passport.authenticate

### Multer

- multipart/form-data -> multipart 처리용 모듈 Multer




```
npm i multer
```

