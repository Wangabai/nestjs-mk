<!--
 * @Author: 王鑫
 * @Description: 
 * @Date: 2022-01-04 15:41:09
-->
# 1.创建项目
## 1.1 创建项目
1. 输入命令
```
nest new blog-server
```
2. 选择包管理器,我选的npm,习惯了

# 2.搭建路由
## 2.1 创建模块
```
nest g module users
nest g controller users
nest g service users
```
一定要按照顺序来，nest会自动创建文件并把各个模块
```
PS D:\服务器搭建\blog-server-nestjs\blog-server> nest g module users
CREATE src/users/users.module.ts (82 bytes)
UPDATE src/app.module.ts (312 bytes)
PS D:\服务器搭建\blog-server-nestjs\blog-server> nest g controller users
CREATE src/users/users.controller.spec.ts (485 bytes)
CREATE src/users/users.controller.ts (99 bytes)
UPDATE src/users/users.module.ts (170 bytes)
PS D:\服务器搭建\blog-server-nestjs\blog-server>
PS D:\服务器搭建\blog-server-nestjs\blog-server> nest g service users
CREATE src/users/users.service.spec.ts (453 bytes)
CREATE src/users/users.service.ts (89 bytes)
UPDATE src/users/users.module.ts (247 bytes)
```
app.module.ts
```
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { UsersModule } from './users/users.module';

@Module({
  imports: [UsersModule],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```
users.module.ts
```
import { Module } from '@nestjs/common';
import { UsersController } from './users.controller';
import { UsersService } from './users.service';

@Module({
  controllers: [UsersController],
  providers: [UsersService]
})
export class UsersModule {}
```

## 2.2 安装依赖
1. 安装数据库的依赖
```
npm install @nestjs/typeorm typeorm mysql2
```
其中mysql2根据自己的数据库来安装,其他可见官网 https://nestjs.bootcss.com/techniques/sql

# 3. 配置数据库
## 1. 配置数据库
创建一个文件放在根目录，ormconfig.ts  
nestjs在运行的时候会先去这个文件

ormconfig.js
```
module.exports = {
  type: 'mysql',
  host: 'localhost',
  port: 3306,
  username: 'root',
  password: '123456',
  database: process.env.NODE_ENV === 'dev' ? 'blog-server' : 'blog-server',
  entities: ['dist/**/*.entity{.ts,.js}'],
  synchronize: true,
};
```
这个是mysql的配置文件，其他的看官网

引入app.module.ts文件
```
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { UsersModule } from './users/users.module';
import { ConfigModule } from '@nestjs/config';

@Module({
  imports: [    
    ConfigModule.forRoot({
      isGlobal: true,
      envFilePath: `.env.${process.env.NODE_ENV}`,
    }),
    TypeOrmModule.forRoot(),
    UsersModule
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

# 4. 启动项目
1. 查看package.json
```
{
  "name": "blog-server",
  "version": "0.0.1",
  "description": "",
  "author": "",
  "private": true,
  "license": "UNLICENSED",
  "scripts": {
    "prebuild": "rimraf dist",
    "build": "nest build",
    "format": "prettier --write \"src/**/*.ts\" \"test/**/*.ts\"",
    "start": "nest start",
    "start:dev": "nest start --watch",
    "start:debug": "nest start --debug --watch",
    "start:prod": "node dist/main",
    "lint": "eslint \"{src,apps,libs,test}/**/*.ts\" --fix",
    "test": "jest",
    "test:watch": "jest --watch",
    "test:cov": "jest --coverage",
    "test:debug": "node --inspect-brk -r tsconfig-paths/register -r ts-node/register node_modules/.bin/jest --runInBand",
    "test:e2e": "jest --config ./test/jest-e2e.json"
  },
  "dependencies": {
    "@nestjs/common": "^8.0.0",
    "@nestjs/core": "^8.0.0",
    "@nestjs/platform-express": "^8.0.0",
    "reflect-metadata": "^0.1.13",
    "rimraf": "^3.0.2",
    "rxjs": "^7.2.0"
  },
  "devDependencies": {
    "@nestjs/cli": "^8.0.0",
    "@nestjs/schematics": "^8.0.0",
    "@nestjs/testing": "^8.0.0",
    "@types/express": "^4.17.13",
    "@types/jest": "27.0.2",
    "@types/node": "^16.0.0",
    "@types/supertest": "^2.0.11",
    "@typescript-eslint/eslint-plugin": "^5.0.0",
    "@typescript-eslint/parser": "^5.0.0",
    "eslint": "^8.0.1",
    "eslint-config-prettier": "^8.3.0",
    "eslint-plugin-prettier": "^4.0.0",
    "jest": "^27.2.5",
    "prettier": "^2.3.2",
    "source-map-support": "^0.5.20",
    "supertest": "^6.1.3",
    "ts-jest": "^27.0.3",
    "ts-loader": "^9.2.3",
    "ts-node": "^10.0.0",
    "tsconfig-paths": "^3.10.1",
    "typescript": "^4.3.5"
  },
  "jest": {
    "moduleFileExtensions": [
      "js",
      "json",
      "ts"
    ],
    "rootDir": "src",
    "testRegex": ".*\\.spec\\.ts$",
    "transform": {
      "^.+\\.(t|j)s$": "ts-jest"
    },
    "collectCoverageFrom": [
      "**/*.(t|j)s"
    ],
    "coverageDirectory": "../coverage",
    "testEnvironment": "node"
  }
}
```
其中运行 start:dev ，这样修改代码时服务会自动重启
```
npm run start:dev
```

# 5. 创建实例
## 1. 创建用户实例表
1. 在users文件夹下创建user.entity.ts
```
import { Entity, Column, PrimaryGeneratedColumn } from 'typeorm';

@Entity()
export class User {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  nickname: string;

  @Column()
  mobile_number: number;

  @Column()
  email: string;

  @Column()
  password: string;
}
```

2. 导入users.module.ts
```
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';
import { UsersController } from './users.controller';
import { UsersService } from './users.service';
import { User } from './user.entity';

@Module({
  imports: [TypeOrmModule.forFeature([User])],
  controllers: [UsersController],
  providers: [UsersService],
})
export class UsersModule {}
```
服务自动重启或者重新启动服务，这样你就可以在你的数据库中看到user表
