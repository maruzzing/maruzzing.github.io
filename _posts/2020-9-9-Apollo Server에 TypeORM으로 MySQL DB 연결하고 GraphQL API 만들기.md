---
layout: post
title: Apollo Server에 TypeORM으로 MySQL DB 연결하고 GraphQL API 만들기
date: 2020-09-08
comments: true
categories: [Study, graphql]
tags: [Typescript, Apollo Server, GraphQL, MySQL, TypeORM]
excerpt: 기본적인 GraphQL API 서버 구축이 되었으니 이제 db을 연결해 볼 차례이다. 여기서는 db는 MySQL을, ORM은 TypeORM을 사용할 예정이다. Sequelize가 더 익숙하지만 TypeGraphQL을 사용하게 되면서 TypeORM을 사용했을 떄 훨씬 생산성이 높아지기 때문에 TypeORM을 사용해 보기로 했다.

---

기본적인 GraphQL API 서버 구축이 되었으니 이제 db을 연결해 볼 차례이다. 여기서는 db는 MySQL을, ORM은 TypeORM을 사용할 예정이다. Sequelize가 더 익숙하지만 TypeGraphQL을 사용하게 되면서 TypeORM을 사용했을 떄 훨씬 생산성이 높아지기 때문에 TypeORM을 사용해 보기로 했다.

# MySQL과 TypeORM 연결하기

### 라이브러리 설치

먼저 필요 모듈을 설치한다. 

```bash
$ npm i mysql2 typeorm class-validator
``` 
<br>

### DB 커넥션 설정

DB 커넥션 설정을 위해 `.env` 파일에 아래 내용을 추가해 준다. TypeORM의 DB 커넥션 설정은 `.env` 뿐만 아니라, `ormconfig.json`, `ormconfig.js`, `ormconfig.xml` 등 다양한 방법으로 할 수 있다. [공식문서 참고](https://github.com/typeorm/typeorm/blob/master/docs/using-ormconfig.md)

```
TYPEORM_CONNECTION=mysql
TYPEORM_HOST= // host
TYPEORM_USERNAME= // username
TYPEORM_PASSWORD= // password
TYPEORM_DATABASE= // db name
TYPEORM_PORT= // db port
TYPEORM_SYNCHRONIZE=true
TYPEORM_LOGGING=true
TYPEORM_ENTITIES=src/entity/**/*.ts
```

### 서버와 연결하기

`server.ts` 파일의 `const app = express();` 아래 코드를 추가하고 실행시키면, DB가 연결되었음을 확인할 수 있다.

```typescript
...
import { createConnection } from 'typeorm';
...

async function initalize() {
  try {
    await createConnection();
    console.log('DB Connected!');
  } catch (e) {
    console.log(e);
  }
}
initalize();
...
```

# Entity 추가하기

이제 Entity를 생성 할 차례인데, 아래와 같이 1:N 관계의 DB를 만들어 볼 생각이다. 

<div style='display: flex; justify-content: center;'>
  <img src="/images/graphql-db-structure.png" alt="graphql-db-structure" width="500em">
</div>


### Project Entity 

먼저, src 폴더 내 entity 폴더를 생성하고, Project.ts 파일을 생성해 준다. 

```typescript
import {
  Entity,
  PrimaryGeneratedColumn,
  Column,
  BaseEntity,
  CreateDateColumn,
  UpdateDateColumn,
} from 'typeorm';
import User from './User'

@Entity()
export default class Project extends BaseEntity {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  title: string;

  @Column({ nullable: true })
  content: string;

  @Column('timestampz')
  @CreateDateColumn()
  createdAt: string;

  @Column('timestampz')
  @UpdateDateColumn()
  updatedAt: string;

  @ManyToOne(() => User, (user) => user.projects, {
    onDelete: 'CASCADE',
  })
  @JoinColumn({ name: 'ownerId' })
  user: User;
}
```

이 때 프로퍼티가 이니셜라이즈 되지 않았다는 오류가 뜨는데 이를 해결해 주기 위해 `tsconfig.json`의 `compilerOptions`에 `"strictPropertyInitialization": false`를 추가해 주거나 프로퍼티에 `!`를 추가해 해결할 수 있다. 

여기에 바로 TypeGraphQL의 타입을 함께 정의해 줄 수 있다.
```typescript
import {
  Entity,
  PrimaryGeneratedColumn,
  Column,
  BaseEntity,
  CreateDateColumn,
  UpdateDateColumn,
  ManyToOne,
  JoinColumn,
} from 'typeorm';
import { Field, Int, ObjectType } from 'type-graphql';
import User from './User';

@ObjectType()
@Entity()
export default class Project extends BaseEntity {
  @Field(() => Int)
  @PrimaryGeneratedColumn()
  id: number;

  @Field()
  @Column()
  title: string;

  @Field({ nullable: true })
  @Column({ nullable: true })
  content: string;

  @Column('timestampz')
  @CreateDateColumn()
  createdAt: string;

  @Column('timestampz')
  @UpdateDateColumn()
  updatedAt: string;

  @ManyToOne(() => User, (user) => user.projects, {
    onDelete: 'CASCADE',
  })
  @Field(() => User)
  @JoinColumn({ name: 'ownerId' })
  user: User;
}
```

### User Entity

동일한 방식으로 User entity를 정의한다.

```typescript
import {
  Entity,
  PrimaryGeneratedColumn,
  Column,
  BaseEntity,
  CreateDateColumn,
  UpdateDateColumn,
  OneToMany,
} from 'typeorm';
import { Field, Int, ObjectType } from 'type-graphql';
import Project from './Project';

@ObjectType()
@Entity()
export default class User extends BaseEntity {
  @Field(() => Int)
  @PrimaryGeneratedColumn()
  id: number;

  @Field()
  @Column({ unique: true })
  email: string;

  @Field()
  @Column()
  password: string;

  @Column('timestampz')
  @CreateDateColumn()
  createdAt: string;

  @Column('timestampz')
  @UpdateDateColumn()
  updatedAt: string;

  @OneToMany(() => Project, (project) => project.user)
  @Field(() => [Project])
  projects: Project[];
}
```

# CRUD 작성하기

### User Resolver

먼저, `src/inputs/userInput.ts` 파일을 생성하고, 아래와 같이 input type을 정의한다. [`class-validator`](https://github.com/typestack/class-validator)를 이용해 타입을 검증해 줄 수 있다. 

```typescript
import { InputType, Field } from 'type-graphql';
import { IsEmail, Length } from 'class-validator';
import User from '../entity/User';

@InputType()
export class UserInput implements Partial<User> {
  @Field()
  @IsEmail()
  email: string;

  @Field()
  @Length(6, 12)
  password: string;
}

@InputType()
export class UpdateUserInput implements Partial<User> {
  @Field()
  @Length(6, 12)
  password: string;
}
```

다음으로, `src/resolvers/userResolver.ts` 파일을 생성하고, 아래와 같이 CRUD를 작성한다. 실제 서비스를 구축할때는 비밀번호는 암호화하여 저장해야 한다.

```typescript
import { Query, Resolver, Mutation, Arg } from 'type-graphql';
import User from '../entity/User';
import { UserInput, UpdateUserInput } from '../inputs/userInput';

@Resolver()
export class UserResolver {
  @Query(() => [User])
  async getAllUsers() {
    return await User.find();
  }

  @Query(() => User, { nullable: true })
  async getUser(@Arg('id') id: number) {
    try {
      return await User.findOne({ where: { id } });
    } catch (err) {
      return err;
    }
  }

  @Mutation(() => User, { nullable: true })
  async createUser(@Arg('data') data: UserInput) {
    try {
      const { email, password } = data;
      return await User.insert({ email, password });
    } catch (err) {
      return err;
    }
  }

  @Mutation(() => User, { nullable: true })
  async updateUser(@Arg('id') id: number, @Arg('data') data: UpdateUserInput) {
    try {
      const user = await User.findOne({ where: { id } });
      if (!user) {
        throw new Error(`The user with id: ${id} does not exist!`);
      }
      Object.assign(user, data);
      await user.save();
      return user;
    } catch (err) {
      return err;
    }
  }

  @Mutation(() => Boolean)
  async deleteUser(@Arg('id') id: number) {
    try {
      const user = await User.findOne({ where: { id } });
      if (!user) {
        throw new Error(`The user with id: ${id} does not exist!`);
      }
      await user.remove();
      return true;
    } catch (err) {
      return err;
    }
  }
}
```

### Project Resolver

먼저, `src/inputs/projectInput.ts` 파일을 생성하고, type을 정의해 준다.

```typescript
import { InputType, Field } from 'type-graphql';
import Project from '../entity/Project';

@InputType()
export class ProjectInput implements Partial<Project> {
  @Field()
  title: string;

  @Field({ nullable: true })
  content?: string;
}

@InputType()
export class UpdateProjectInput implements Partial<Project> {
  @Field({ nullable: true })
  title?: string;

  @Field({ nullable: true })
  content?: string;
}
```

먼저, `src/resolvers/projectResolver.ts` 파일을 생성하고, CRUD를 작성해 준다.

```typescript
import { Query, Resolver, Mutation, Arg } from 'type-graphql';
import User from '../entity/User';
import Project from '../entity/Project';
import { ProjectInput, UpdateProjectInput } from '../inputs/projectInput';

@Resolver()
export class ProjectResolver {
  @Query(() => [Project])
  async getProjects() {
    return await Project.find({ relations: ['user', 'user.projects'] });
  }

  @Query(() => [Project])
  async getProjectsByUser(@Arg('id') id: number) {
    return await Project.find({ where: { ownerId: id } });
  }

  @Query(() => Project, { nullable: true })
  async getSingleProject(@Arg('id') id: number) {
    try {
      return await Project.findOne({ where: { id } });
    } catch (err) {
      return err;
    }
  }

  @Mutation(() => Boolean)
  async createProject(@Arg('id') id: number, @Arg('data') data: ProjectInput) {
    try {
      const user = await User.findOne({ where: { id } });
      if (!user) {
        throw new Error(`The user with id: ${id} does not exist!`);
      }
      const { content, title } = data;
      await Project.insert({ content, title, user });
      return true;
    } catch (err) {
      return err;
    }
  }

  @Mutation(() => Boolean)
  async updateProject(
    @Arg('id') id: number,
    @Arg('data') data: UpdateProjectInput,
  ) {
    try {
      const project = await Project.findOne({ where: { id } });
      if (!project) {
        throw new Error(`The project with id: ${id} does not exist!`);
      }
      Object.assign(project, data);
      await project.save();
      return true;
    } catch (err) {
      return err;
    }
  }

  @Mutation(() => Boolean)
  async deleteProject(@Arg('id') id: number) {
    try {
      const project = await Project.findOne({ where: { id } });
      if (!project) {
        throw new Error(`The project with id: ${id} does not exist!`);
      }
      await project.remove();
      return true;
    } catch (err) {
      return err;
    }
  }
}
```


