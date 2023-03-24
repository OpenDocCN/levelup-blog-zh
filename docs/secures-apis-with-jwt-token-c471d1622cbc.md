# 用 JWT 令牌保护 API

> 原文：<https://levelup.gitconnected.com/secures-apis-with-jwt-token-c471d1622cbc>

![](img/ae8c55367c914d06af74aeab6d482461.png)

照片由[弗兰克](https://unsplash.com/@franckinjapan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在[上一篇文章中，](https://medium.com/@hantsy/connecting-to-mongodb-c8166e0a6cb7)我们连接到一个 MongoDB 服务器，并使用一个真实的数据库来替换虚拟数据存储。在本帖中，我们将探讨如何在向客户端应用程序公开 API 时保护您的 API。

当我们谈到 web 应用程序的安全性时，从技术上讲，它包括:

*   **认证** —应用程序会要求您提供您的委托人，然后识别您的身份。
*   **授权** —根据您的声明，检查您是否有权限执行某些操作。

[Passportjs](http://www.passportjs.org/) 是 [Expressjs](https://expressjs.com/) 平台上最流行的认证框架之一。Nestjs 通过其`@nestjs/passportjs`模块与 passportjs 进行了很好的集成。我们将遵循官方指南的[认证](https://docs.nestjs.com/techniques/authentication)章节，将*本地*和 *jwt* 策略添加到我们之前帖子所做的应用中。

# 先决条件

安装与 passportjs 相关的依赖项。

```
$ npm install --save @nestjs/passport passport passport-local @nestjs/jwt passport-jwt
$ npm install --save-dev @types/passport-local @types/passport-jwt
```

在开始认证工作之前，我们先生成一些骨架代码。

首先生成一个`AuthModule`和`AuthService`。

```
nest g mo auth
nest g s auth
```

身份验证应该与应用程序中的用户一起工作。

类似地，创建一个独立的`UserModule`来处理用户查询。

```
nest g mo user
nest g s user
```

好了，让我们开始充实`AuthModule`。

# 实施身份验证

首先，我们为用户模型创建一些资源，一个`Document`和`Schema`文件。

在*/用户*文件夹下创建新文件。

```
import { SchemaFactory, Schema, Prop } from '@nestjs/mongoose';
import { Document } from 'mongoose';@Schema()
export class User extends Document {
  @Prop({ require: true })
  readonly username: string; @Prop({ require: true })
  readonly email: string; @Prop({ require: true })
  readonly password: string;
}export const UserSchema = SchemaFactory.createForClass(User);
```

`User`类是用 Mongoose 包装一个文档，`UserSchema`是描述`User`文档。

在`UserModule`中注册`UserSchema`，然后可以使用`Model<User>`对`User`文档进行一些操作。

```
@Module({
  imports: [MongooseModule.forFeature([{ name: 'users', schema: UserSchema }])],
  providers: [//...
  ],
  exports: [//...
  ],
})
export class UserModule {}
```

这里的*用户*作为*令牌*在注入一个`Model`时用来标识不同的`Model`。在 mongoose 中注册一个`UserSchema`时，上面`MongooseModule.forFeature`中的 name 属性也是`User`文档的收藏名称。

在`UserService`中增加一个`findByUsername`方法。

```
@Injectable()
export class UserService {
  constructor(@InjectModel('users') private userModel: Model<User>) {} findByUsername(username: string): Observable<User | undefined> {
    return from(this.userModel.findOne({ username }).exec());
  }
}
```

在`UserModule`的`@Module`声明中，将`UserService`注册到`providers`中，不要忘记将其添加到`exports`中，这样其他模块在导入`UserModule`时可以使用该服务。

```
//...other imports
import { UserService } from './user.service';@Module({
  providers: [UserService],
  exports:[UserService]//exposing users to other modules...
})
export class UserModule {}
```

创建一个测试用例来测试`findByUsername`方法。

```
describe('UserService', () => {
  let service: UserService;
  let model: Model<User>; beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      providers: [
        UserService,
        {
          provide: getModelToken('users'),
          useValue: {
            findOne: jest.fn(),
          },
        },
      ],
    }).compile(); service = module.get<UserService>(UserService);
    model = module.get<Model<User>>(getModelToken('users'));
  }); it('should be defined', () => {
    expect(service).toBeDefined();
  }); it('findByUsername should return user', async () => {
    jest
      .spyOn(model, 'findOne')
      .mockImplementation((conditions: any, projection: any, options: any) => {
        return {
          exec: jest.fn().mockResolvedValue({
            username: 'hantsy',
            email: 'hantsy@example.com',
          } as User),
        } as any;
      }); const foundUser = await service.findByUsername('hantsy').toPromise();
    expect(foundUser).toEqual({
      username: 'hantsy',
      email: 'hantsy@example.com',
    });
    expect(model.findOne).lastCalledWith({username: 'hantsy'});
    expect(model.findOne).toBeCalledTimes(1);
  });
});
```

`UserService`依赖于一个`Model<User>`，使用一个 provider 通过 jest 嘲弄特性来嘲弄它。使用`jest.spyOn`方法，你可以存根一个方法的细节，并观察这个方法的调用。

让我们转到`AuthModule`。

使用`@nestjs/passpart`，通过扩展`PassportStrategy`设置您的 passport 策略很简单，我们将在这里创建两个 passport 策略。

*   `LocalStrategy`根据请求中的用户名和密码字段处理验证。
*   `JwtStrategy`通过给定的 JWT 令牌头来处理认证。

简化，通过嵌套命令行生成两个文件。

```
nest g class auth/local.strategy.ts --flat
nest g class auth/jwt.strategy.ts --flat
```

首先，让我们实现`LocalStrategy`。

```
@Injectable()
export class LocalStrategy extends PassportStrategy(Strategy) {
  constructor(private authService: AuthService) {
    super({
      usernameField: 'username',
      passwordField: 'password',
    });
  } validate(username: string, password: string): Observable<any> {
    return this.authService
      .validateUser(username, password)
      .pipe(throwIfEmpty(() => new UnauthorizedException()));
  }
}
```

在构造器中，使用`super`提供您正在使用的策略的基本选项。对于本地策略，它需要用户名和密码字段。

validate 方法用于根据给定的信息验证认证信息，这里是从请求中提供的*用户名*和*密码*。

> *关于本地策略的配置选项和验证的更多细节，查看*[*passport-local*](http://www.passportjs.org/packages/passport-local/)*项目。*

在`AuthService`中，增加一个方法`validateUser`。

```
@Injectable()
export class AuthService {
  constructor(
    private userService: UserService,
    private jwtService: JwtService,
  ) {} validateUser(username: string, pass: string): Observable<any> {
    return this.userService.findByUsername(username).pipe(
      map(user => {
        if (user && user.password === pass) {
          const { password, ...result } = user;
          return result;
        }
        return null;
      }),
    );
  }
}
```

> *在实际应用中，我们可以使用一个加密工具来散列和比较输入的密码。我们将在以后的文章中讨论它。*

它从`UserModule`调用`UserService`中的`findByUsername`。`AuthModule`报关单中的`UserModule`。

```
@Module({
  imports: [
    UserModule,
 	...]
    ...   
})
export class AuthModule {}
```

让我们在`AppController`中创建一个方法，通过给定的用户名和密码字段实现认证。

```
@Controller()
export class AppController {
  constructor(private authService: AuthService) {} @UseGuards(LocalAuthGuard)
  @Post('auth/login')
  login(@Req() req: Request): Observable<any> {
    return this.authService.login(req.user);
  }
}
```

它只是在`AuthService`中调用另一个方法`login`。

```
@Injectable()
export class AuthService {
  constructor(
    private userService: UserService,
    private jwtService: JwtService,
  ) {}
  //...
  login(user: Partial<User>): Observable<any> {
    const payload = {
      sub: user.username,
      email: user.email,
      roles: user.roles,
    };
    return from(this.jwtService.signAsync(payload)).pipe(
      map(access_token => {
        access_token;
      }),
    );
  }
}
```

`login`方法负责根据经过认证的主体生成基于 JWT 的访问令牌。

URI 路径`auth/login`使用一个`LocalAuthGuard`来保护它。

```
@Injectable()
export class LocalAuthGuard extends AuthGuard('local') {}
```

让我们总结一下本地策略是如何工作的。

1.  当用户用`username`和`password`点击*认证/登录*时，将应用`LocalAuthGuard`。
2.  `LocalAuthGuard`将触发`LocalStrategy`，并调用其`validate`方法，并将结果存储回`request.user`。
3.  返回控制器，从`request`读取用户主体，生成 JWT 令牌并将其发送回客户端。

登录后，可以提取`access token`并将其放入新请求的 HTTP 头中，以访问受保护的资源。

让我们来看看 JWT 策略是如何运作的。

首先执行`JwtStrategy`。

```
@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor() {
    super({
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      ignoreExpiration: false,
      secretOrKey: jwtConstants.secret,
    });
  } validate(payload: any) :any{
    return { email: payload.email, sub: payload.username };
  }
}
```

在构造函数中，配置了几个选项。

`jwtFromRequest`指定了提取令牌的方法，它可以来自 HTTP cookie 或者请求头`Authorization`。

如果`ignoreExpiration`为假，当解码 JWT 令牌时，它将检查有效期。

`secretOrKey`用于签署 JWT 令牌或解码令牌。

在`validate`方法中，有效载荷是**解码的内容** JWT 声明。您可以根据声明添加自定义验证。

> *更多关于配置选项和验证方法，查看*[*passport-jwt*](http://www.passportjs.org/packages/passport-jwt/)*项目。*

在`AuthModule`的声明中，imports `JwtModule`接受一个 register 方法，为 JWT 令牌签名添加初始选项。

```
@Module({
  imports: [
     // ...
    JwtModule.register({
      secret: jwtConstants.secret,
      signOptions: { expiresIn: '60s' },
    }),
  ],
  providers: [//..., 
      LocalStrategy, JwtStrategy],
  exports: [AuthService],
})
export class AuthModule {}
```

同样创建一个`JwtAuthGuard`，并在`AuthModule`中的*提供者*中注册它。

```
@Injectable()
export class JwtAuthGuard extends AuthGuard('jwt'){}
```

创建一个方法来读取当前用户的配置文件。

```
@Controller()
export class AppController {
  constructor(private authService: AuthService) {}

  //...  
  @UseGuards(JwtAuthGuard)
  @Get('profile')
  getProfile(@Req() req: Request): any {
    return req.user;
  }
}
```

让我们回顾一下 JWT 战略的工作流程。

1.  给定一个 JWT 令牌`XXX`，访问*/带有头文件`Authorization:Bearer XXX`的简介*。
2.  `JwtAuthGuard`会触发`JwtStrategy`，调用`validate`方法，并将结果存储回`request.user`。
3.  在`getProfile`方法中，将`request.user`发送给客户端。

如果您想设置一个默认策略，将`AuthModule`声明中的`PassportModule`改为如下。

```
@Module({
  imports: [
    PassportModule.register({ defaultStrategy: 'jwt' }),
    //...
})
export class AuthModule {}
```

Nestjs 在运行时提供了几个应用程序生命周期挂钩。在您的代码中，您可以观察这些生命周期事件，并为您的应用程序执行一些特定的任务。

例如，为`Post`创建一个数据初始化器来插入样本数据。

```
@Injectable()
export class UserDataInitializerService
  implements OnModuleInit, OnModuleDestroy {
  constructor(@InjectModel('users') private userModel: Model<User>) {}
  onModuleInit(): void {
    console.log('(UserModule) is initialized...');
    this.userModel
      .create({
        username: 'hantsy',
        password: 'password',
        email: 'hantsy@example.com',
      })
      .then(data => console.log(data));
  }
  onModuleDestroy(): void {
    console.log('(UserModule) is being destroyed...');
    this.userModel
      .deleteMany({})
      .then(del => console.log(`deleted ${del.deletedCount} rows`));
  }
}
```

> *更多关于生命周期钩子的信息，查看官方文档的* [*生命周期事件*](https://docs.nestjs.com/fundamentals/lifecycle-events) *章节。*

# 运行应用程序

打开您的终端，通过执行以下命令运行应用程序。

```
npm run start
```

使用*用户名/密码*对登录。

```
>curl [http://localhost:3000/auth/login](http://localhost:3000/auth/login) -d "{\"username\":\"hantsy\", \"password\":\"password\"}" -H "Content-Type:application/json"
>{"access_token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1cG4iOiJoYW50c3kiLCJzdWIiOiI1ZjJkMGU0ODZhOTZiZTEyMDBmZWZjZWMiLCJlbWFpbCI6ImhhbnRzeUBleGFtcGxlLmNvbSIsInJvbGVzIjpbIlVTRVIiXSwiaWF0IjoxNTk2Nzg4NDg5LCJleHAiOjE1OTY3OTIwODl9.4oYpKTikoTfeeaUBoEFr9d1LPcN1pYqHjWXRuZXOfek"}
```

尝试使用此*访问令牌*访问 */profile* 端点。

```
>curl [http://localhost:3000/profile](http://localhost:3000/profile) -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1cG4iOiJoYW50c3kiLCJzdWIiOiI1ZjJkMGU0ODZhOTZiZTEyMDBmZWZjZWMiLCJlbWFpbCI6ImhhbnRzeUBleGFtcGxlLmNvbSIsInJvbGVzIjpbIlVTRVIiXSwiaWF0IjoxNTk2Nzg4NDg5LCJleHAiOjE1OTY3OTIwODl9.4oYpKTikoTfeeaUBoEFr9d1LPcN1pYqHjWXRuZXOfek"
{"username":"hantsy","email":"hantsy@example.com","id":"5f2d0e486a96be1200fefcec","roles":["USER"]}
```

从我的 github 中抓取[源代码，切换到分支](https://github.com/hantsy/nestjs-sample) [feat/auth](https://github.com/hantsy/nestjs-sample/blob/feat/auth) 。