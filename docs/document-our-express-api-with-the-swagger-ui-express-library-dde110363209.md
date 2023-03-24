# 用 Swagger UI Express 库记录我们的 Express API

> 原文：<https://levelup.gitconnected.com/document-our-express-api-with-the-swagger-ui-express-library-dde110363209>

![](img/3b644bc384aaec53335df31834e690b7.png)

照片由[卡斯滕·怀恩吉尔特](https://unsplash.com/@karsten116?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

编写 API 文档既痛苦又乏味。然而，如果我们使用 Express，我们可以通过 Swagger UI Express 库使我们的生活变得更容易。在本文中，我们将看看如何使用这个库来记录我们的 Express API。

# 装置

我们可以通过运行以下命令来安装该软件包:

```
npm i swagger-ui-express
```

# 记录我们的终点

我们可以通过编写一些代码来记录我们的端点。

首先，我们通过编写以下内容来添加我们的`swagger.json`文件:

```
{  
  "swagger": "2.0",
  "info": {
    "title": "some-app",
    "version": "Unknown"
  },
  "consumes": [
    "application/json"
  ],
  "produces": [
    "application/json"
  ],
  "paths": {
    "/{id}": {
      "parameters": [
        {
          "name": "id",
          "required": true,
          "in": "path",
          "type": "string",
          "description": "some id"
        }
      ],
      "get": {
        "operationId": "routeWithId",
        "summary": "some route",
        "description": "some route",
        "produces": [
          "application/json"
        ],
        "responses": {
          "200": {
            "description": "200 response",
            "examples": {
              "application/json": "{ foo: 1 }"
            }
          }
        }
      }
    },
    "/": {
      "parameters": [],
      "get": {
        "operationId": "anotherRoute",
        "summary": "another route",
        "description": "another route",
        "produces": [
          "application/json"
        ],
        "responses": {
          "202": {
            "description": "202 response",
            "examples": {
              "application/json": "{ foo: 'bar' }"
            }
          }
        }
      }      
    }
  }
}
```

它包含了我们应用程序中端点的所有信息。数据包括所需的参数和响应。`parameters`具有参数，`responses`具有来自端点的可能响应。

然后在我们的应用程序文件中，我们写道:

`index.js`

```
const express = require('express');
const bodyParser = require('body-parser');
const swaggerUi = require('swagger-ui-express');
const swaggerDocument = require('./swagger.json');
const swaggerOptions = {
  swaggerOptions: {
    validatorUrl: null
  }
};const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(swaggerDocument, swaggerOptions));app.get('/', (req, res) => {
  res.json({ foo: 'bar' });
});app.get('/:id', (req, res) => {
  res.json({ foo: req.params.id });
});app.listen(3000, () => console.log('server started'));
```

我们需要我们的`swagger.json`文件并添加`api-docs`端点来显示文档。

有 Swagger 的验证器的 URL，用于验证我们的文档。

我们只是补充:

```
app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(swaggerDocument, swaggerOptions));
```

添加端点并传入显示文档的选项。

`swaggerUi.setup`方法将文档和选项作为参数。

当我们转到`/api-docs`端点时，我们应该看到 Swagger 接口，并用我们的应用程序尝试请求。

我们可以输入`/{id}`端点的 ID，并在单击 Execute 后查看响应。

# 自定义 CSS 样式

我们可以用`customCss`属性添加自定义 CSS:

```
const express = require('express');
const bodyParser = require('body-parser');
const swaggerUi = require('swagger-ui-express');
const swaggerDocument = require('./swagger.json');
const swaggerOptions = {
  swaggerOptions: {
    validatorUrl: null,  
  },
  customCss: '.swagger-ui .topbar { display: none }'
};const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(swaggerDocument, swaggerOptions));app.get('/', (req, res) => {
  res.json({ foo: 'bar' });
});app.get('/:id', (req, res) => {
  res.json({ foo: req.params.id });
});app.listen(3000, () => console.log('server started'));
```

我们把顶栏藏了起来:

```
customCss: '.swagger-ui .topbar { display: none }'
```

# 从 URL 加载 Swagger

我们可以使用`url`选项从 URL 加载 Swagger 文件:

```
const express = require('express');
const app = express();
const swaggerUi = require('swagger-ui-express');

const options = {
  swaggerOptions: {
    url: 'http://petstore.swagger.io/v2/swagger.json'
  }
}

app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(null, options));
```

我们可以加载多个文件:

```
const express = require('express');
const app = express();
const swaggerUi = require('swagger-ui-express');

const options = {
  explorer: true,
  swaggerOptions: {
    urls: [
      {
        url: 'http://petstore.swagger.io/v2/swagger.json',
        name: 'Spec1'
      },
      {
        url: 'http://petstore.swagger.io/v2/swagger.json',
        name: 'Spec2'
      }
    ]
  }
}

app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(null, options));
```

# 结论

我们可以用 Swagger UI Express 库轻松地记录我们的 Express API。