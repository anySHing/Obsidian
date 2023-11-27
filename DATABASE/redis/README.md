# REDIS

하고많은 NoSQL 중 Redis를 사용하는 이유

-> `In-Memory`라는 특성 때문

> In-Memory가 뭔데

서버를 재시작해도 데이터를 유지하고 싶다면 DB를 사용해야 하는데 보통 mySQL, mongoDB같은 DB는 접근하고, 커넥션을 유지하는 데엔 비용이 많이 든다. 세션같은 데이터를 관리하는데에 이런 DB를 끌어다 쓰는 것은 비용 낭비이므로 Redis를 활용 <br />

DB이긴 한데 데이터를 불러올 때 메모리에 로드하기 때문에 접근 속도가 매우 빠름. 세션이나 간단한 key-value, 간단한 자료 구조 데이터를 유지할 수 있으면서 속도도 빠르게 사용할 수 있음

노드 클러스터링 시 pid가 달라 세션이 유지되지 않는 문제도 Redis를 통해 세션을 공유함으로 해결할 수 있다.

**PUB/SUB** 서버(구독)로도 사용할 수 있다.

**단점** 이라하면 데이터가 항상 정확하게 유지됨을 보장할 수 없다.(날아가도 크게 문제없는 데이터를 보관하는 용도로 쓰자.)

<br />

## 설치

**npm i redis**로 설치할 수 있다.<br />

```ts
const redis = require('redis');
const client = redis.createClient();
```

호스팅을 사용하는 경우엔 `createClient()`의 파라미터로 주소를 넣으면 된다.

<br />

## 자료구조

### string

가장 일반적인 `키-값` 문자열이다. `set`으로 설정하고 `get`으로 가져온다.

```
client.set('name', 'sonminah');
client.get('name', (err, reply) => {
  console.log(reply); // 'sonminah'
})
```

<br />

### hash

`키-해시`이다. 객체를 저장한다고 보면 됨. `hmset`으로 설정하고 `hgetall`로 가져온다.


```ts
client.hmset('friends', 'name', 'son', 'age', 25);
client.hgetall('friends', (err, obj) => {
  console.log(obj); // { name: 'son', age: 25 }
})
```

<br />

### list

`키-배열`이다. 중복 데이터를 허용한다. `rpush`는 js의 `push`랑 비슷하고, `lpush`는 `unshift`와 비슷하다. 가져올 때는 `lrange`를 사용한다. 0, -1은 처음과 끝 인덱스를 의미한다.

```ts
client.rpush('fruits', 'apple', 'orange', 'apple'); 
client.lpush('fruits', 'banana', 'pear');
client.lrange('fruits', 0, -1, (err, arr) => {
  console.log(arr); // ['pear', 'banana', 'apple', 'orange', 'apple'] -> [ apple, orange, apple]에서 -> [banana, apple, orange, apple] -> [pear, banana, apple, orange, apple]이 된 듯
})
```

<br />

### set

`-셋`이다. 배열과 비슷하지만 중복을 허용하지 않는다. `sadd`로 저장, `smembers`로 출력할 수 있다.

```ts
// 'cat'을 두 번 넣었으나 중복은 무시되었고, 넣은 순서와 관계없이 섞인 결과가 출력되었다.
client.sadd('animal', 'dog', 'cat', 'bear', 'cat', 'lion');
client.smembers('animal', (err, set) => {
  console.log(set); // ['cat', 'dog', 'bear', 'lion'];
})
```
<br />

### sorted set

`키-정렬 셋`이다. `set`인데 순서를 정렬할 수 있다.

```ts
// zrange -> 오름차순(ASC), zrevrange -> 내림차순(DESC)
client.zadd('height', 180, 'park', 170, 'son', 160, 'lee', 150, 'kim');
client.zrange('height', 0, -1, (err,sset) => {
  console.log(sset); // ['kim', 'lee', 'son', 'park']
});
```

<br />

### geo

`키-경도위도`이다. longitude가 먼저인 것에 주의. 위치들을 추가한 후 위치간 거리나 특정 좌표 중심으로 해당하는 지역을 구할 수 있다.

```ts
client.geoadd('cities', 126.97, 37.56, 'seoul', 129.07, 35.17, 'busan', 126.70, 37.45, 'incheon');
client.geodist('cities', 'seoul', 'busan', (err, dist) => {
  console.log(dist); // 325619.5465
});
client.georadius('cities', 126.8, 37.5, 50, 'km', (err, cities) => {
  console.log(cities); // ['incheon', 'seoul']
});
```

<br />

## 기타 명령어

1. 해당하는 key 지우기

```ts
client.del('name');
```

2. 존재하는 키인지 확인

```ts 
client.exists('name'); // 있으면 1, 없으면 0 return
```

3. 키 이름 바꾸기

```ts
client.rename('animals', 'pets') // animals -> pets
```

다른 커맨드들은 [여기](https://redis.io/commands/)에서 찾아볼 수 있다.

## express-session과 연동하기

*npm i redis connect-redis*

```ts
const session = require('express-session');
const connectRedis = require('connect-redis');
const RedisStore = connectRedis(session);
const sess = { // => store 뺴고 express-session 설정하던거랑 똑같음
  resave: false,
  saveUninitialized: false,
  secret = sessionSecret,
  name: 'sessionId',
  cookie: {
    httpOnly: true,
    secure: false
  },
  store: new RedisStore({ url: '레디스 호스팅 주소', logErrors: true }),
};

app.use(session(sess));
```