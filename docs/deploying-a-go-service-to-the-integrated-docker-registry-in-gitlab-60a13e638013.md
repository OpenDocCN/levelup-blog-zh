# 将 Go 服务部署到 Gitlab 中的集成 Docker 注册中心

> 原文：<https://levelup.gitconnected.com/deploying-a-go-service-to-the-integrated-docker-registry-in-gitlab-60a13e638013>

![](img/e5d9ca3622c13fe76dc510d5d997cf84.png)

在 Gitlab/K8S CI 文章的第二部分中，我们使用 Gitlab CI 和我们之前建立的注册表部署了一个简单的 Go 服务器，并运行从我们自己的注册表中提取的映像构建。

# Go 服务

在 gitlab 中，在 gitlab.lightphos.com:30080 创建了一个私人项目“hi”

克隆项目(在我的例子中，用户名是 **sr** )

`git clone ssh://git@gitlab.lightphos.com:30022/sr/hi.git`

导航到它:

使用以下内容添加文件`hi.go`:

```
package main

import (
	"fmt"
	"net/http"
)

func main() {
	fmt.Println("Started on 8090")
	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
	   fmt.Fprintf(w, "Hi %s\n", r.URL.Path)
	})
	http.ListenAndServe(":8090", nil)
}
```

添加、提交和推送文件到 git。浏览您的本地 gitlab，您应该看到列出了文件 hi.go。

创建以下内容，并将其命名为 Dockerfile

```
FROM golang:1.12.0-alpine3.9 as builder 
RUN mkdir /app 
ADD hi.go /app 
WORKDIR /app 
RUN go build -o hi . FROM alpine:latest as host 
RUN apk add --no-cache ca-certificates 
COPY --from=builder /app/hi / 
WORKDIR / 
EXPOSE 8090 
CMD ["/hi"]
```

Gitlab ci 文件。gitlab-ci.yml

```
variables:
  REPO_NAME: ${CI_REGISTRY}/${CI_PROJECT_PATH}
  CONTAINER_IMAGE: ${CI_REGISTRY}/${CI_PROJECT_PATH}:${CI_BUILD_REF_NAME}_${CI_BUILD_REF}
  CONTAINER_IMAGE_LATEST: ${CI_REGISTRY}/${CI_PROJECT_PATH}:latest
  DOCKER_DRIVER: overlay2

before_script:
  - mkdir -p $GOPATH/src/$(dirname $REPO_NAME)
  - ln -svf $CI_PROJECT_DIR $GOPATH/src/$REPO_NAME
  - cd $GOPATH/src/$REPO_NAME  

stages:
  - test
  - build
  - release
  - deploy

format:
  image: golang:latest
  stage: test
  script:
    - go fmt $(go list ./... | grep -v /vendor/)
    - go vet $(go list ./... | grep -v /vendor/)
    - go test -race $(go list ./... | grep -v /vendor/)

compile:
  image: golang:latest
  stage: build
  script:
    - go build -race -ldflags "-extldflags '-static'" -o $CI_PROJECT_DIR/hi
  artifacts:
    paths:
      - hi

release:
    image: docker:19.03.1
    stage: release
    before_script:
      - echo ${CONTAINER_IMAGE}
      - echo  $CI_BUILD_TOKEN | docker login -u gitlab-ci-token --password-stdin ${CI_REGISTRY}
    script:
      - docker build -t ${CONTAINER_IMAGE} -t ${CONTAINER_IMAGE_LATEST} .
      - docker push ${CONTAINER_IMAGE}
      - docker push ${CONTAINER_IMAGE_LATEST}
```

注册一名跑步者，并将其设置为无标记跑步(参见[上一篇文章](https://medium.com/@lightphos/gitlab-installation-registry-and-runner-with-docker-700549b93803))

Git 提交这些文件并推送。

CI 应该介入，构建 go 服务并注册它。发布阶段应该如下所示:

```
Running with gitlab-runner 12.4.0 (1564076b)
  on Runner 8RydtMPh
Using Docker executor with image docker:19.03.1 ...
Pulling docker image docker:19.03.1 ...
Using docker image sha256:0cecfefe921f22fc898f7a0055358380c8870ab6f05b01999367911714fe9d00 for docker:19.03.1 ...
Running on runner-8RydtMPh-project-3-concurrent-0 via d00feb8672c0...
Fetching changes with git depth set to 50...
Reinitialized existing Git repository in /builds/sr/hi/.git/
Checking out 3538cfa1 as master...
Removing hi

Skipping Git submodules setup
Downloading artifacts for compile (124)...
Downloading artifacts from coordinator... ok        id=124 responseStatus=200 OK token=dWTn-Pwd
$ echo ${CONTAINER_IMAGE}
gitlab.lightphos.com:5555/sr/hi:master_3538cfa18843906473c0a075d39cce785d2f5eb4
$ echo  $CI_BUILD_TOKEN | docker login -u gitlab-ci-token --password-stdin ${CI_REGISTRY}
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
$ docker build -t ${CONTAINER_IMAGE} -t ${CONTAINER_IMAGE_LATEST} .
Sending build context to Docker daemon  9.755MB

Step 1/11 : FROM golang:1.12.0-alpine3.9 as builder
 ---> 2205a315f9c7
Step 2/11 : RUN mkdir /app
 ---> Using cache
 ---> 54da6752cdd5
Step 3/11 : ADD hi.go /app
 ---> d21d0d2af424
Step 4/11 : WORKDIR /app
 ---> Running in e2e87def04e3
Removing intermediate container e2e87def04e3
 ---> 4d78d2a12967
Step 5/11 : RUN go build -o hi .
 ---> Running in 60fc1b0dffe8
Removing intermediate container 60fc1b0dffe8
 ---> 5b2fa72dd518
Step 6/11 : FROM alpine:latest as host
 ---> 965ea09ff2eb
Step 7/11 : RUN apk add --no-cache ca-certificates
 ---> Using cache
 ---> ba261b495589
Step 8/11 : COPY --from=builder /app/hi /
 ---> 8d376b2c37cb
Step 9/11 : WORKDIR /
 ---> Running in 7f60e9fb9d88
Removing intermediate container 7f60e9fb9d88
 ---> 387b7422eab2
Step 10/11 : EXPOSE 8090
 ---> Running in 3aa6f0aa255b
Removing intermediate container 3aa6f0aa255b
 ---> 9f8c5dbf79b4
Step 11/11 : CMD ["/hi"]
 ---> Running in a46f92a45ab4
Removing intermediate container a46f92a45ab4
 ---> 4a72c58b9b54
Successfully built 4a72c58b9b54
Successfully tagged gitlab.lightphos.com:5555/sr/hi:master_3538cfa18843906473c0a075d39cce785d2f5eb4
Successfully tagged gitlab.lightphos.com:5555/sr/hi:latest
$ docker push ${CONTAINER_IMAGE}
The push refers to repository [gitlab.lightphos.com:5555/sr/hi]
f85f9ff79b0a: Preparing
3162ed9eaf42: Preparing
77cae8ab23bf: Preparing
3162ed9eaf42: Layer already exists
77cae8ab23bf: Layer already exists
f85f9ff79b0a: Pushed
master_3538cfa18843906473c0a075d39cce785d2f5eb4: digest: sha256:393b1ab11ab37aff4ddf9192fb8dd96561d15cab875aa67266efdef5234b49c4 size: 949
$ docker push ${CONTAINER_IMAGE_LATEST}
The push refers to repository [gitlab.lightphos.com:5555/sr/hi]
f85f9ff79b0a: Preparing
3162ed9eaf42: Preparing
77cae8ab23bf: Preparing
f85f9ff79b0a: Layer already exists
3162ed9eaf42: Layer already exists
77cae8ab23bf: Layer already exists
latest: digest: sha256:393b1ab11ab37aff4ddf9192fb8dd96561d15cab875aa67266efdef5234b49c4 size: 949
Job succeeded
```

您应该在 Packages -> Container Registry 中看到该图像。

复制最新的图像细节，并像这样运行:

`docker run --rm -it -p 8090:8090 gitlab.lightphos.com:5555/sr/hi:latest`

它应该输出

`Started on 8090`

导航至以下链接:

[http://localhost:8090/there](http://localhost:8090/there)

应该给你:

你好

在下一篇[文章中，我们将着眼于将 kubernetes 集成到 gitlab，并通过 CI 管道将映像部署到 git lab。](https://blog.ramjee.uk/minikube-and-gitlab-ci/)

*原载于 2019 年 11 月 12 日*[*https://blog . ram JEE . uk*](https://blog.ramjee.uk/deploying-a-go-service-to-the-integrated-docker-registry-in-gitlab/)*。*