<!--
 * @Author: 王鑫
 * @Description: nestjs学习文档
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
![avatar](https://raw.githubusercontent.com/Wangabai/nestjs-mk/main/1.png)

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

ormconfig.json
```
{
  "type": "mysql",
  "host": "localhost",
  "port": 3306,
  "username": "root",
  "password": "123456",
  "database": "blog_server",
  "entities": [
    "dist/**/*.entity{.ts,.js}"
  ],
  "synchronize": true
}

// 其中synchronize，设置为true，是因为现在是生产环境，项目刚搭建，数据库可能会有更改，如此会方便一点，这个属性的意思是可以同步数据库，如果实例文件里面删除了某个属性，数据库会同步删除，正式环境会很危险，值得注意。 
```
这个是mysql的配置文件，其他的看官网

引入app.module.ts文件
```
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { UsersModule } from './users/users.module';

@Module({
  imports: [    
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
  uid: number;

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
创建一个用户实体

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
![avatar](https://raw.githubusercontent.com/Wangabai/nestjs-mk/main/2.png)

## 2. 添加校验
1. 安装依赖
```
npm install class-validator class-transformer
```
2. 在user文件夹下创建文件夹dtos，添加文件create-user.dto.ts  
create-user.dto.ts
```
import { IsEmail, IsString, IsNumber } from 'class-validator';

export class CreateUserDto {
  @IsString()
  nickname: string;

  @IsNumber()
  mobile_number: number;

  @IsEmail()
  email: string;

  @IsString()
  password: string;
}
```

3. 在main.ts中添加白名单

main.ts
```
import { NestFactory } from '@nestjs/core';
import { ValidationPipe } from '@nestjs/common';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.useGlobalPipes(
    new ValidationPipe({
      whitelist: true,
    }),
  );
  await app.listen(3000);
}
bootstrap();
```

4. 配置接口路由

users.controller.ts
```
import { Body, Controller, Post } from '@nestjs/common';
import { CreateUserDto } from './dtos/create-user.dto';

@Controller('auth')
export class UsersController {
  @Post('/signup')
  createUser(@Body() body: CreateUserDto) {
    console.log(body);
  }
}
```
5. 在POSTMAN中实验

