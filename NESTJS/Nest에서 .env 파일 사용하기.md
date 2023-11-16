
Express에서 하듯이 dotenv 패키지를 설치하고 임포트해와 .config()으로 하는게 아니다

# 설치
```
$ npm i @nestjs/config
```

요 [@nestjs/config](https://www.npmjs.com/package/@nestjs/config) 패키지에서 내부적으로 `dotenv` 패키지를 사용한다.

# 시작하기

#### app.module.ts
```ts
import { Module } from '@nestjs/common';
import { ConfigModule } from '@nestjs/config'

@Module({
	imports: [ConfigModule.forRoot()],
})

export class AppModule {}
```