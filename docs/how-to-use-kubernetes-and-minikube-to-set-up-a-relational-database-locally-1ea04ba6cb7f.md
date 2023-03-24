# 如何使用 Kubernetes 和 Minikube 在本地建立关系数据库

> 原文：<https://levelup.gitconnected.com/how-to-use-kubernetes-and-minikube-to-set-up-a-relational-database-locally-1ea04ba6cb7f>

## 一步一步的过程中，所有的例子

![](img/f4844787f4ba1b613fa29426f7f087c5.png)

由 [Kelvin Ang](https://unsplash.com/@kelvin1987?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/server?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Kubernetes 是一个开源的容器编排平台，可以自动化应用程序的部署、管理和扩展。在 2014 年开源之前，Kubernetes 最初是由谷歌的工程师开发的。许多大公司现在都采用 Kubernetes 作为他们的容器编排选项。

如果您对 Kubernetes 有一些基本的了解，那么这篇文章非常适合您。它将带您完成使用 Minikube 在本地设置 Kubernetes 集群和部署 MySQL 数据库的步骤。

# 安装 Minikube

```
brew install minikube
```

这将在您的本地机器上安装 minikube。安装后检查 minikube 版本。

```
minikube version: v1.13.1commit: 1fd1f67f338cbab4b3e5a6e4c71c551f522ca138
```

# 启动 Minikube 集群

```
minikube start 😄  minikube v1.13.1 on Darwin 10.15.7...🏄  Done! kubectl is now configured to use "minikube" by default
```

如果你得到一个与 Docker 相关的错误，首先安装 Docker 桌面。或者为 docker 增加内存和内核。这是我第一次尝试时遇到的一个相关错误。

```
⛔  Exiting due to RSRC_DOCKER_CORES: Docker Desktop has less than 2 CPUs configured, but Kubernetes requires at least 2 to be available
💡  Suggestion:    
1\. Click on "Docker for Desktop" menu icon
2\. Click "Preferences"
3\. Click "Resources"
4\. Increase "CPUs" slider bar to 2 or higher
5\. Click "Apply & Restart"
📘  Documentation: [https://docs.docker.com/docker-for-mac/#resources](https://docs.docker.com/docker-for-mac/#resources)
```

# 检查并确认当前配置和集群

```
kubectl config viewcontexts:
- context:
cluster: minikube
user: minikube
name: minikubekubectl get allNAME TYPE CLUSTER-IP EXTERNAL-IP PORT(S) AGE
service/kubernetes ClusterIP IP <none> 443/TCP 2m49s
```

您的 minikube 集群已经启动并正在运行，并且正在使用默认的名称空间。现在让我们继续部署一个 MySQL 数据库。

# 部署卷

要设置有状态的应用程序，您需要持久性卷来存储数据。因此，创建 MySQL 实例的第一步是创建这些**持久卷**。这是我们将使用的 YAML。

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv-volume
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 20Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-data
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 20Gi
```

注意事项:

→*metadata . name:mysql-PV-data*:这是实际的 MySQL 部署将如何使用卷

→*resources . requests . storage:20Gi*:这是我们请求的存储

```
kubectl apply -f pv-mysql.yamlpersistentvolume/mysql-pv-volume created
persistentvolumeclaim/mysql-pv-data created
```

# 检查卷资源

```
**kubectl get pvc**NAME            STATUS   VOLUME            CAPACITY   ACCESS MODES   STORAGECLASS   AGE
mysql-pv-data   Bound    mysql-pv-volume   **20Gi**       RWO            manual         12s
```

# 部署 MySQL

这是我们将使用的 YAML。

```
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  ports:
  - port: 3306
  selector:
    app: mysql
  clusterIP: None
---
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: mysql
spec:
  selector:
    matchLabels:
      app: mysql
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - image: mysql:5.6
        name: mysql
        env:
          # Use secret in prod use cases
        - name: MYSQL_ROOT_PASSWORD
          value: password
        ports:
        - containerPort: 3306
          name: mysql
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
      volumes:
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pv-data
```

注意事项:

→*claim name:MySQL-PV-data:*与上面定义的卷同名

→ *值:密码*:这是登录 MySQL 的密码。你以后会需要它的。

# 检查正在运行的资源

```
Cintos-Mac:MySQL_MiniKube cinto$ **kubectl get all**
NAME                         READY   STATUS    RESTARTS   AGE
pod/mysql-7d8f78bf86-hfjkg   1/1     Running   0          38sNAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP    16m
service/mysql        ClusterIP   None         <none>        3306/TCP   38sNAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/mysql   1/1     1            1           38sNAME                               DESIRED   CURRENT   READY   AGE
replicaset.apps/mysql-7d8f78bf86   1         1         1       38s
```

您可以看到我们有一个 **pod** 正在运行，一个**部署，**和一个**副本集**。这些是我们在 YAML 配置的。

# 登录 MySQL pod 并验证

```
kubectl exec -it pod/mysql-7d8f78bf86-hfjkg /bin/bash
```

这将登录到 MySQL 正在运行的 pod。

```
mysql -ppassword
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.6.50 MySQL Community Server (GPL)
...
Type 'help;' or '\h' for help. Type '\c' to clear the current input statementmysql>
```

我们现在已经登录到 MySQL 数据库。

# 创建示例表和数据

```
use mysql
CREATE TABLE test_kube(namespace VARCHAR(100),cluster VARCHAR(50), create_date DATE);
Query OK, 0 rows affected (0.01 sec)insert into test_kube values('default','minikube','2020-10-10');
Query OK, 1 row affected, 1 warning (0.00 sec)select * from test_kube;
+-----------+----------+-------------+
| namespace | cluster  | create_date |
+-----------+----------+-------------+
| default   | minikube | 2020-10-10  |
+-----------+----------+-------------+
1 row in set (0.00 sec)
```

您的设置已经就绪。享受吧。

# 删除和清理

```
minikube stop
```

如果你有任何问题，请给我留言。

如果您想学习 Unix 高级命令，请查看以下内容:

[](/advanced-unix-commands-to-boost-your-productivity-4af6e9086c04) [## 提高生产力的高级 Unix 命令

### 你学得越多，你就变得越好

levelup.gitconnected.com](/advanced-unix-commands-to-boost-your-productivity-4af6e9086c04)