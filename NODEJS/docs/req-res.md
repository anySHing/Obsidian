# `req`, `res` 객체

Node.js의 `http` 모듈에서 자원하는 객체를 확장시켰다

## req

- `req.app`: `req` 객체를 통해 `app` 객체에 접근할 수 있다. ex) req.app.get('PORT');
- `req.body`: `body-parser` 미들웨어가 만드는 요청의 본문을 해석한 객체 (POSt 요청 보냈을 때 여기로 들어온다는 소리)
- `req.cookies`: `cookie-parser` 미들웨어가 만드는 요청의 쿠키를 해석한 객체
- `req.ip`: 요청한 ip의 주소가 담겨있다.
- `req.params`: 라우트 매개 변수에 대한 정보가 담긴 객체
- `req.query`: 쿼리스트링에 대한 정보가 담긴 객체이다.
- `req.signedCookies`: 서명된 쿠키들은 `req.cookies` 대신 여기에 담김
- `req.get(헤더 이름)`: 헤더의 값을 가져오고 싶을 때 사용하는 메서드

<br />

## res

- `res.app`: `req.app`과 동일
- `res.cookie(키, 값, 옵션)`: 쿠키를 설정하는 메서드 
- `res.clearCookie(키, 값, 옵션)`: 쿠키를 제거하는 메서드
- `res.end()`: 데이터 없이 응답을 보낸다.
- `res.json(JSON)`: JSON 형식의 객체를 담아서 응답한다.
- `res.locals`: 하나의 요청 안에서 미들웨어 간에 데이터를 전달하고 싶을 때 사용하는 객체
- `res.redirect(주소)`: 리다이렉트할 주소와 함께 응답을 보낸다.
- `res.render(뷰, 데이터)`: 템플릿 엔진을 렌더링해서 응답할 때 사용한다. (리액트같은거 쓰면 안쓴다는 소리)
- `res.send(데이터)`: 데이터와 함께 응답을 보낸다. 데이터의 종류는 문자열, HTML, 버퍼, 객체, 배열 등 상관없음
- `res.sendFile(경로)`: 경로에 위치한 파일을 응답한다.
- `res.set(헤더, 값)`: 응답의 헤더를 설정한다.
- `res.status(코드)`: 응답 시의 HTTP 상태 코드를 지정한다.