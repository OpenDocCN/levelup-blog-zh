# 构建大型生产烧瓶应用程序

> 原文：<https://levelup.gitconnected.com/structuring-a-large-production-flask-application-7a0066a65447>

![](img/7de3970311d6e6e5dcafd3376967bc20.png)

烧瓶蟒

在 Python web 框架的世界里， [Flask](https://flask.palletsprojects.com/en/1.1.x/) 和 [Django](https://www.djangoproject.com/) 被认为是两个主要选项。Flask 是一个微框架，通常更容易开始构建应用程序。Django 是一个包含电池的框架，预装了几乎所有你需要的东西。使用过这两个工具之后，我是 Flask 的忠实粉丝，在过去的 6 年里，我们一直用它来构建我们的 web 应用程序。我不会在这里讨论 Flask vs Django 的细节，但是你可以在这里了解更多关于它们和其他 Python 框架的信息。

尽管 Flask 是一个很棒的框架，但是它的一个缺点是它没有提供项目结构或者设计模式。如果您想快速开始，但最终在一个较大的应用程序中成为一个问题，这是非常好的。当构建应该由当前和未来的开发人员维护的大型生产应用程序时，拥有内置结构并遵循清晰的设计模式至关重要。在这里，我将讨论我在设计 Flask 应用程序时学到的东西，希望可以帮助其他人。作为免责声明，我们的 Flask 应用程序主要是基于 api 的，尽管我们也提供一些途径，如通过 Flask 模板登录。

下面是 Flask 应用程序结构的概要:

```
~/YourApplication             
    |-- requirements.txt                                                  
    |-- config.py
    |-- wsgi.py                # wsgi server
    |__ .env                   # Virtual Environment
    |__ /app                   # Our Application package
         |-- __init__.py       # **Application factory method** 
         |-- errors.py         # Custom error classes 
         |-- db.py             # Database connection 
         |-- utils.py          # Utility helper functions 
         |-- services.py       # Business logic
         |-- daos.py           # Database access object
         |-- models.py         # Database models
         |-- security.py       # Security access checks
         |__ /routes           # **Routes package (Blueprints)**
             |-- __init__.py
             |-- users.py           
         |__ /templates
         |__ /static
    |__ ..
    |__ .
```

在根目录中，我将虚拟环境目录作为一个隐藏的`.env`文件夹。我更喜欢将虚拟 env 放在应用程序文件夹中，而不是放在一个公共目录中。这为所有项目保持了一个通用的`.env`命名约定，我不需要找到虚拟 env 文件夹的位置。我还在我的全局 git 配置中放置了一个对`.env`的全局忽略，以使其不受版本控制。

**应用工厂**:开始使用 Flask 最简单的方法是创建 Flask 类的一个实例，并在该实例上设置配置。然后，应用程序实例可用于路由和注册中间件，如官方文档所示:

```
**from** flask **import** Flask
app = Flask**(**__name__**)**

@app.route**(**'/'**)**
**def** hello_world**():**
    **return** 'Hello, World!'
```

但是这种模式在较大的 Flask 应用程序中很快就失效了，因为会有一些环境，比如 staging、dev 和 testing。您需要对应用程序实例进行不同的设置和配置，这种方法很难管理。

相反，更好的方法是使用应用程序工厂，它是一个返回应用程序实例的函数。有了这个函数，你就可以传入不同的参数来设置你的应用。这将应用程序的配置和设置与应用程序本身的创建分离开来。现在，在您的开发或测试环境中，您可以传入不同的配置设置。

```
*app/__init__.py***def** create_app**(**config_filename**):**
    app = Flask**(**__name__**)**
    app.config.from_pyfile**(**config_filename**)**

    **return** app
```

在生产环境中运行时，您将使用不同的文件来导入工厂函数，并创建您的应用程序实例，然后传递给 gunicorn 或您选择的 wsgi web 服务器。应用程序工厂方法也适用于 Blueprint，我们将在下面介绍它。

```
*wsgi.py*from app import create_app
config = "config.Production"application = create_app(config) # call from wsgi server
```

**蓝图** : Flask 路由是通过使用应用程序实例作为 route 函数的装饰器来设置的。这迫使您在与应用程序实例相同的文件中声明所有路由。这在有很多路径的大 flask 应用程序中是不可维护的。

将路线分解成单独文件的方法是使用烧瓶[蓝图](http://flask.palletsprojects.com/en/1.1.x/blueprints/#blueprints)。如果你的 Flask 应用是基于 api 的，那么你可以将文件名和资源匹配起来。因此，在 routes 目录中会有一个 *user.py* 文件，其中包含与用户相关的所有路由，这可以扩展到其他资源。蓝图是组织路线的好方法，也是大型应用程序的必备工具。

服务/dao 单例设计模式:我发现一个在 Flask 应用中非常有效的模式是服务/DAO(数据库访问对象)单例模式。这种模式的总结如下:

*   业务逻辑放在服务方法中。
*   数据库访问(ORM)在使用模型的 dao 方法中。
*   路由是轻量级的，要么使用服务，要么使用 dao 方法。

为了演示这种模式，我将使用一个获取所有用户的示例。

声明您的用户模型(这是基于 sqlalchemy ORM 的)。

```
*app/models.py*class User:
    id = Column**(**Integer**,** primary_key=**True)**
    username = Column**(**String**(**80**),** unique=**True,** nullable=**False)**
    email = Column**(**String**(**120**),** unique=**True,** nullable=**False)**
```

然后，使用与数据库中的用户模型交互的方法来声明 UserDao 类。我们为可以在整个应用程序中使用的 dao 实例创建一个 singleton 对象。

```
*app/daos.py*from app.models import User
from app.db import sessionclass UserDAO:
    def __init__(self, model):
        self.model = model    

    def get_all(self):
        return session.query(self.model).all() def get_by_username(self, username -> str):
        return (
            session.query(self.model)
            .filter_by(get_by_username=username)
            .first()
         ) def get_by_email(self, email -> str):
        return (
            session.query(self.model)
            .filter_by(email=email)
            .first()
         )user_dao = UserDAO(User)
```

最后，在您的蓝图路由中定义 get all 用户路由。

```
*app/routes/users.py*from flask import Blueprint, jsonfiy
from app.daos import user_daouser_bp = Blueprint**(**'user_bp'**,** __name__**)**@user_bp.route("/api/users", methods=["GET])
def get_users():
    return jsonify(user_dao.get_all())
```

在任何情况下，如果您需要用户的业务逻辑，那么它将在服务中创建，如图所示。比方说，当更新用户模型时，您需要某种逻辑。像在 dao 模块中一样，单例对象将在整个应用程序中声明和使用

```
*app/services.py*from app.daos import user_daoclass UserService:
    def update_user(self, user_id, data -> dict):
        user = user_dao.get(user_id)
        # Update user with data        
        # check user for business logic
        # example: lowercase all emails
        return useruser_service = UserService()
```

然后，您可以在您的蓝图路线中使用此服务，如下所示。

```
*app/routes/users.py*from flask import Blueprint, jsonfiy, request
from app.services import user_serviceuser_bp = Blueprint**(**'user_bp'**,** __name__**)**@user_bp.route("/api/users", methods=["GET])
def get_users():
    return jsonify(user_dao.get_all())**@user_bp.route("/api/users/<int:user_id>", methods=["POST"])
def update_user(user_id):** **data = request.json** **user = user_service.update_user(user_id, data)
    return jsonify(user)**
```

如果您的 models.py、services.py 或 daos.py 文件变得太大，您可以将它们转换成具有较小模块的包。如何定义较小的模块将取决于应用程序的类型和您自己的用例。

在过去的 6 年里，我们在生产应用程序中使用了这种设计模式，它为我们提供了很好的服务。我计划更详细地介绍运行生产 Flask 应用程序的其他方面，如配置管理、第三方 api 集成、后台任务、docker 设置等等。如果你想得到所有的文章，一定要跟随。