ORM에 구애받지 않으나 공식 문서상에서는 [[Sequelize]]와 [[typeORM]]을 사용하는 예시를 보여주고 있다.

## 1. TypeORM

NestJS가 [[TYPESCRIPT/han-ip-size/README|타입스크립트]]로 작성되었으므로 잘 어울린다.

```
$ npm i @nestjs/typeorm typeorm mysql2
```

설치가 완료되면 `AppModule`의 `TypeOrmMoudle`로 가져올 수 있다.

#### app.module.ts

```ts
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';

@Module({
	imports: [
		TypeOrmModule.forRoot({
		type: 'mysql',
		host: 'localhost',
		port: 3306,
		username: 'root',
		password: 'root'
		database: 'test',
		entities: [],
		synchronize: true, // 실제 배포시에는 false
		}),
	],
})
export class AppModule {}
```

> 실제 배포시에는 `synchronize: true` 옵션을 사용하면 안된다.

`forRoot()` 메서드의 다른 옵션들도 살펴보자.

|||
|:-:|:-:|
|retryAttempts| DB에 연결이 실패했을 경우 몇번까지 재시도 할것인지. default: 10|
|retryDelay|연결 재시도 간격.(ms) default: 3000|
|autoLoadEntities|true로 설정할 경우 entity를 자동으로 생성. default: false|

또 다른 방법으로는

[typeORM DataSource Options](https://typeorm.io/data-source-options)를 참고하여 객체를 집어넣어 만들어주는 방법도 있다.

```ts
import { DataSource } from 'typeorm';

@Module({
imports: [TypeOrmModule.forRoot(), UsersModule],
})

export class AppModule {
constructor(private dataSource: DataSource){}
}
```

깔끔하긴 하다

## 레포지토리 패턴

typeORM은 *repository design pattern*을 지원하므로 각 엔터티는 고유한 레포지토리를 가진다. 이 레포지토리들은 DB datasource에서 가져올 수 있다

예시를 위해 `User`라는 엔터티를 하나 만들어보자

#### user.entity.ts
```ts
import { Entity, Column, PrimaryGeeratedColumn } from 'typeorm';

@Entity()
export class User {
	@PrimaryGeneratedColumn()
	id: number;

	@Column()
	firstName: string;

	@Column()
	lastName: string;

	@Column({ default: true })
	isActive: boolean;
}
```

> 엔터티의 설정을 더 알아보려면 [typeORM docs](https://typeorm.io/entities)

`User` 엔터티 파일은 `users` 디렉토리에 만들자. `users` 디렉토리에는 `UsersModule`과 관련된 모든 파일을 집어넣는다(controller, service 등등..)

꼭 여기다 만들 필요는 없지만 이렇게 하는게 좋다고 한다

`User` 엔터티를 다 만들었으면 이제 `module`에 등록을 해야한다.

#### app.module.ts
```ts
import { Module } from '@nestjs/common';
import { TypeOrModule } from '@nestjs/typeorm';
import { User } from './users/user.entity';

@Module({
	imports: [
		TypOrmModule.forRoot({
			type: 'mysql',
			host: 'localhost',
			port: 3306,
			username: 'root',
			password: 'root',
			database: 'test',
			entities: [User], // 이 부분
			synchronize: true,
		})
	]
})

export class AppModule {}
```
## 2. Sequelize