# 记录在案的🐳烧瓶芹菜兔 Redis 的应用

> 原文：<https://levelup.gitconnected.com/dockerized-flask-celery-rabbitmq-redis-application-df74c837a0a1>

![](img/186575edcf88fea90fb5a7ea958d79b8.png)

这解释了如何配置 Flask、Celery、RabbitMQ 和 Redis 以及 Docker 来构建一个 web 服务，该服务动态地上传内容并在准备好显示内容时加载该内容。我们将主要关注芹菜和围绕它的服务。Docker 更简单一点。

视频解释

# 项目结构

完成的项目结构如下:

```
├── Dockerfile
├── docker-compose.yml
├── README.md
├── app
│ ├── app.py
│ ├── tasks.py
│ └── templates
│ ├── download.html
│ └── index.html
├── scripts
│ ├── run_celery.sh
│ └── run_web.sh
└── requirements.txt
```

# 创建 Flask 应用程序🌶

首先，我们为我们的应用程序创建一个文件夹。对于本例，我们的文件夹名为“app”。在这个文件夹中，创建一个“app.py”文件和一个名为“templates”的空文件夹，我们的 HTML 模板将存储在这个文件夹中。

对于我们的应用程序，我们首先包括一些基本的 Flask 库，并创建应用程序的一个实例:

```
from io import BytesIO
from flask import Flask, request
from flask import render_template, make_response
APP = Flask(__name__)
```

我们为 Flask 定义了三个实现路径:一个登录页面，一个嵌入图片的二级页面，以及一个图片本身的路径。我们的图像路径动态地裁剪图像。对于这个例子，它使用“枕头”裁剪图像，并且还包括一些延迟，以便创建图像所花费的时间更明显。

```
[@APP](http://twitter.com/APP).route(‘/’)
def index():
   return render_template(‘index.html’)
[@APP](http://twitter.com/APP).route(‘/image_page’)
def image_page():
   job = tasks.get_data_from_strava.delay()
   return render_template(‘home.html’)
[@APP](http://twitter.com/APP).route('/result.png')
def result():
   '''
   Pull our generated .png binary from redis and return it
   '''
   jobid = request.values.get('jobid')
   if jobid:
      job = tasks.get_job(jobid)
      png_output = job.get()
      png_output="../"+png_output
      return png_output
   else:
      return 404
```

接下来，我们需要打开“templates”文件夹，创建以下两个模板:

**index.html**

```
<div id=”imgpl”><img src=”result.png?{{JOBID}}” /></div>
```

如果我们添加以下代码，然后运行脚本，我们可以加载我们的网页，并测试图像生成。

```
if __name__ == ‘__main__’:
   APP.run(host=’0.0.0.0')
```

我们看到我们的页面加载需要一段时间才能完成，因为对“result.png”的请求直到图像生成完成后才返回。

# 扩展我们的网络应用程序，使用芹菜🥬

在我们的“app”目录中，创建包含我们的芹菜任务的“tasks.py”文件。我们添加的必要芹菜包括:

```
from celery import Celery, current_task
from celery.result import AsyncResult
```

![](img/b619cd06e4c21c352621cccc5872c72d.png)

假设我们的 RabbitMQ 服务在一个我们可以通过“rabbit”引用的主机上，我们的 Redis 服务在一个通过“Redis”引用的主机上，我们可以使用下面的代码创建一个 Celery 的实例:

```
REDIS_URL = ‘redis://redis:6379/0’
BROKER_URL = ‘amqp://admin:mypass@rabbit//’CELERY = Celery(‘tasks’,backend=REDIS_URL,broker=BROKER_URL)
```

![](img/78f16736b7c4beb4935a0b8524e209d0.png)

然后，我们需要更改结果的默认序列化程序。芹菜 4.0 及以上版本使用 JSON 作为序列化器，不支持二进制数据的序列化。我们可以切换回旧的默认序列化器(pickle ),或者使用支持二进制数据并且非常高效的新 MessagePack。

因为我们正在更改序列化程序，所以我们还需要告诉 Celery 接受来自非默认序列化程序的结果(以及仍然接受来自 JSON 的结果)。

```
CELERY.conf.accept_content = [‘json’, ‘msgpack’]
CELERY.conf.result_serializer = ‘msgpack’
```

首先，我们将实现一个返回给定 ID 的作业的函数。这使得我们的应用程序和芹菜任务可以相互交流:

```
def get_job(job_id):
 return AsyncResult(job_id, app=CELERY)
```

接下来，我们定义异步函数，将图像生成代码从“app.py”中移出，并添加允许该方法排队等待执行的函数装饰器:

```
[@CELERY](http://twitter.com/CELERY).task()
def image_demension(img):
   time.sleep(2)
   im = Image.open(img)
   width, height = im.size
   left = 4
   top = height / 5
   right = 154
   bottom = 3 * height / 5
   # Cropped image of above dimension \
   im1 = im.crop((left, top, right, bottom))
   newsize = (300, 300)
   im1 = im1.resize(newsize)
   width, height = im1.size
   location=os.path.join(‘static/worker-img’,’cropped_img.’+im.format.lower())
    im1.save(os.path.join(‘static/worker-img’,’cropped_img.’+im.format.lower()))
   print(width,height)
   print(“pass”)
   return location
```

我们没有构建响应，而是返回存储在 Redis 上的二进制图像。我们还用进度指示器在不同点更新任务，进度指示器可以从 Flask 应用程序中查询。

我们向“app.py”添加了一个新的路由，它检查进度并以 JSON 对象的形式返回状态，这样我们就可以编写一个 ajax 函数，当最终图像准备就绪时，我们的客户端可以在加载它之前进行查询。

```
[@APP](http://twitter.com/APP).route(‘/progress’)
def progress():
   jobid = request.values.get(‘jobid’)
   if jobid:
   job = tasks.get_job(jobid)
   if job.state == ‘PROGRESS’:
   return json.dumps(dict(
      state=job.state,
      progress=job.result[‘current’],
   ))
   elif job.state == ‘SUCCESS’:
   return json.dumps(dict( 
      state=job.state,
      progress=1.0,
   ))
return ‘{}’
```

使用以下 Javascript 代码扩展我们的“templates/download.html ”:

```
<script src=”//code.jquery.com/jquery-2.1.1.min.js”></script>
<script>
function poll() {
   $.ajax(“{{url_for(‘.progress’, jobid=JOBID)}}”, {
      dataType: “json”
     , success: function(resp) {
     if(resp.progress >= 0.99) {
         $(“#wrapper”).html(‘’);
         $.get(“result.png?jobid={{JOBID}}”, function(data, status){
            end_file=data;  
            $(“#imgpl”).html(‘<img src=’+end_file+’>’);
            console.log(“success”)
          });
      return;
     }
     else {
        setTimeout(poll, 500.0);  
     }
   } 
  });
}
$(function() {
   var JOBID = “{{ JOBID }}”;
   poll();
});
</script>
```

“投票”功能反复要求我们的 web 应用程序的“进度”路径。当它报告图像已经生成时，它用图像的 URL 替换占位符中的 HTML 代码，然后从我们修改的`/result.png '路径动态加载图像:

```
[@APP](http://twitter.com/APP).route(‘/result.png’)
def result():
‘’’
Pull our generated .png and return it
‘’’
   jobid = request.values.get(‘jobid’)
   if jobid:
   job = tasks.get_job(jobid)
   png_output = job.get()
   png_output=”../”+png_output
   return png_output
   else:
   return 404
```

在这个阶段，我们有了一个带有异步图像生成的工作 web 应用程序。

使用 Docker 打包我们的应用程序🐳

我们的应用程序需要 4 个独立的容器来容纳我们的每项服务:

烧瓶

芹菜

-兔子 q🐇

-雷迪斯

Docker 为[rabbit MQ](https://hub . docker . com/_/rabbit MQ/)和[Redis](https://hub . docker . com/_/Redis/)提供了预构建的容器。这两个都工作得很好，我们将使用它们。

![](img/d339893f0a1d7826cde19c6db4b231ed.png)

对于 Flask 和 Celery，我们将从一个简单的“Dockerfile”构建两个相同的容器。

Dockerfile 文件

```
# Pull the latest version of the Python container.
FROM python:latest
# Add the requirements.txt file to the image.
ADD requirements.txt /app/requirements.txt
# Set the working directory to /app/.
WORKDIR /app/
# Install Python dependencies.
RUN pip install -r requirements.txt
# Create an unprivileged user for running our Python code.
RUN adduser — disabled-password — gecos ‘’ app
```

我们用 Docker 组合文件“docker-sdfcompose.yml”将所有这些整合在一起。虽然早期版本的组合文件需要为每个服务提供端口，但我们可以使用“links”关键字将服务链接在一起。“depends”关键字确保我们的所有服务以正确的顺序启动。

docker-compose.yaml

```
version: '3'
services:
  redis:
    image: redis:latest
    hostname: redis
  rabbit:
    hostname: rabbit
    image: rabbitmq:latest
    environment:
      - RABBITMQ_DEFAULT_USER=admin
      - RABBITMQ_DEFAULT_PASS=mypass
  web:
    build:
      context: .
      dockerfile: Dockerfile
    hostname: web
    command: ./scripts/run_web.sh
    volumes:
      - .:/app
    ports:
      - "5000:5000"
    links:
      - rabbit
      - redis
  worker:
    build:
      context: .
      dockerfile: Dockerfile
    command: ./scripts/run_celery.sh
    volumes:
      - .:/app
    links:
      - rabbit
      - redis
    depends_on:
      - rabbit
```

要创建和运行容器，请使用:

```
docker-compose builddocker-compose up
```

![](img/123f7db3be6ea41a368933aa43bf611e.png)

Docker 的主要好处之一是，如果需要，我们可以运行一个容器的多个实例。要运行芹菜消费者的多个实例，请执行以下操作:

码头工人-撰写规模工人=N

其中 N 是所需的后端工作节点数。请访问 http://localhost:5000 查看我们的完整申请。🥳

![](img/269b7df5214746120870051f06637fa6.png)![](img/9eb5c85e9948d23604b171d9d6a5eb18.png)![](img/e88593173cd1cd1d12cbd1d28187a34b.png)

# github

[](https://github.com/alvisf/Dockerized-Flask-Celery-RabbitMQ-Redis) [## alvisf/dockered-Flask-芹菜-RabbitMQ-Redis

### 这解释了如何配置 Flask、Celery、RabbitMQ 和 Redis 以及 Docker 来构建一个 web 服务…

github.com](https://github.com/alvisf/Dockerized-Flask-Celery-RabbitMQ-Redis) 

# 结论🤝

这是一个简单的概念验证，说明如何将所有东西结合在一起使用，并启动和运行异步功能。