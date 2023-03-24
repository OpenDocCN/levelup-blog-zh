# 使用 CDK8s 部署简单的 NodeJS 微服务

> 原文：<https://levelup.gitconnected.com/deploying-a-simple-nodejs-microservice-using-cdk8s-4da8c3b7a32d>

![](img/e4b688490641107f27527676cc7de62f.png)

对于一个 DevOps 工程师来说，仅仅为了在 Kubernetes 上部署一个微服务而编写 200 多行 YAML 代码是一件非常痛苦的事情。

[CDK8s](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwjLtOHq8O7uAhVIeX0KHfFqCUYQFjAAegQIAxAC&url=https%3A%2F%2Fgithub.com%2Fawslabs%2Fcdk8s&usg=AOvVaw2y3ffai280pM2lKZMp5tmj) 可用于删除所有重复的代码，使部署您的微服务变得更容易，而不必编写大量的 YAML 清单。

我们还可以使用我们最喜欢的面向对象语言编写代码。

我们将使用 TypeScript 和开源 CDK8s 结构编写一个示例 CDK8s 图表。

**使用的结构:**

*   [@ open CDK 8s/CDK 8s-redis-STS](https://github.com/opencdk8s/cdk8s-redis-sts)* v 0 . 0 . 7
*   cdk8s-plus-17 * v 1 . 0 . 0-beta 8

1.  初始化 CDK8s 项目

```
mkdir cdk8s-node-app && cd cdk8s-node-app
cdk8s init typescript-app
```

2.安装构造库

```
npm install @opencdk8s/cdk8s-redis-sts 
```

3.编辑`main.ts`以包含我们的 Redis db 和 NodeJS 应用程序

```
import { Construct } from 'constructs';
import { App, Chart, ChartProps } from 'cdk8s';
import { MyRedis } from '[@opencdk8s/cdk8s-redis-sts](http://twitter.com/opencdk8s/cdk8s-redis-sts)';
import { Deployment } from 'cdk8s-plus-17';export class MyChart extends Chart {
  constructor(scope: Construct, id: string, props: ChartProps = { }) {
    super(scope, id, props);const redis = new MyRedis(this, 'redis', {
      image: 'redis',
      namespace: 'default',
      volumeSize: '10Gi',
      replicas: 3,
      createStorageClass: false,
    });new Deployment(this, "deployment", {
      metadata: {
        namespace: 'default',
      },
      containers: [{
        image: "hunterthompson/node-redis-counter:latest",
        name: "hello",
        env: {
          'REDIS_URI': {
            value: redis.name
          }
        }
      }]
    }) }
}const app = new App();
new MyChart(app, 'microservice');
app.synth();
```

4.运行编译和合成

```
npm run compile && npm run synth
```

**`**npm run synth**`**命令的最终结果是您可以使用** `**kubectl**`应用的 YAML 清单**

```
apiVersion: v1
data:
  master.conf: |-

    bind 0.0.0.0
    daemonize no
    port 6379
    tcp-backlog 511
    timeout 0
    tcp-keepalive 300
    supervised no
  slave.conf: |-

    slaveof redis 6379
kind: ConfigMap
metadata:
  name: redis-redis-conf
  namespace: default
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: redis
  name: redis
  namespace: default
spec:
  ports:
    - port: 6379
      targetPort: 6379
  selector:
    app: redis
  type: ClusterIP
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  labels:
    app: redis
  name: redis
  namespace: default
spec:
  replicas: 3
  selector:
    matchLabels:
      app: redis
  serviceName: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
        - command:
            - bash
            - -c
            - |-
              [[ `hostname` =~ -([0-9]+)$ ]] || exit 1
              ordinal=${BASH_REMATCH[1]}
              if [[ $ordinal -eq 0 ]]; then
              redis-server /mnt/redis/master.conf
              else
              redis-server /mnt/redis/slave.conf
              fi
          env: []
          image: redis
          name: redis
          ports:
            - containerPort: 6379
          resources:
            limits:
              cpu: 400m
              memory: 512Mi
            requests:
              cpu: 200m
              memory: 256Mi
          volumeMounts:
            - mountPath: /data
              name: redis
            - mountPath: /mnt/redis/
              name: redis-redis-conf
      terminationGracePeriodSeconds: 10
      volumes:
        - configMap:
            name: redis-redis-conf
          name: redis-redis-conf
  volumeClaimTemplates:
    - metadata:
        name: redis
        namespace: default
      spec:
        accessModes:
          - ReadWriteOnce
        resources:
          requests:
            storage: 10Gi
        storageClassName: gp2
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: microservice-deployment-c89e8760
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      cdk8s.deployment: microservice-deployment-c87f6fea
  template:
    metadata:
      labels:
        cdk8s.deployment: microservice-deployment-c87f6fea
    spec:
      containers:
        - env:
            - name: REDIS_URI
              value: redis
          image: hunterthompson/node-redis-counter:latest
          imagePullPolicy: Always
          name: hello
          ports: []
          volumeMounts: []
      volumes: []
```

**我们刚刚通过编写 33 行 CDK8s 代码合成了一个 130 行的 YAML 清单。**