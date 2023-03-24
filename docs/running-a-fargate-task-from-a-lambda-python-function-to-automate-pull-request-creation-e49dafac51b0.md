# 从 Lambda Python 函数运行 Fargate 任务来自动创建拉请求

> 原文：<https://levelup.gitconnected.com/running-a-fargate-task-from-a-lambda-python-function-to-automate-pull-request-creation-e49dafac51b0>

![](img/884b1b31467a72c94d25f1a10e07625b.png)

在 Reaction Commerce，我们遵循 GitOps 部署工作流程。我们在 Kubernetes 集群中运行我们的服务，并使用 [kustomize](https://github.com/kubernetes-sigs/kustomize) 来定义每个服务的清单。给定服务的部署最常见的是通过针对托管 Kubernetes 清单的 GitHub 存储库(我们称之为“gitops”repo)的 Pull 请求来更改 kustomization.yaml 文件中的 Docker 图像标记。

一旦 PR 被批准和合并，在我们的 Kubernetes 集群中运行的一个 [flux](https://fluxcd.io/) pod 将从 GitHub 中提取变更，并将其应用到正在运行的集群中，从而导致所述服务的部署。

我们找到了一种方法，通过调用 CircleCI 管道中的脚本来自动创建这些 Pull 请求，circle ci 管道与给定服务的 GitHub 存储库相关联。该脚本使用 [hub](https://hub.github.com/) CLI 创建一个针对 gitops repo 的 PR，并将其分配给审阅者。

我最近收到的一个 DevOps 请求是找到一种方法来使用一个集中的组织范围的 webhook，这样当 PR 合并时，只有一段代码在运行，它知道在适当的环境(开发、集成等)中根据 gitops repo 中适当的 kustomization.yaml 文件创建 PR。这将减少许多管道中的样板文件和重复测试。

我找到的解决方案是创建一个用 Python 编写的 Lambda 函数，它作为 webhook 从 CircleCI 调用，并通过 [boto](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html) 运行 Fargate 任务。Fargate 任务执行实际的 PR 创建。

以下是关于这个流程的各种元素的一些细节。

## 从 CircleCI 调用 webhook

我发现一个方便的 CircleCI Orb[https://circleci.com/orbs/registry/orb/eddiewebb/webhook](https://circleci.com/orbs/registry/orb/eddiewebb/webhook)已经写好了，它做了我想做的事情:通过 JSON 有效负载将当前 circle ci 作业的信息发布到我选择的 webhook。

为了在 CircleCI 管道中调用 webhook，我使用了一个这样的代码片段，其中为了调用 Orb 功能，我需要做的唯一定制就是将`webhook/notify` `endpoint`值设置为我的 Lambda 函数的 REST 端点。

```
version: 2.1orbs:
  webhook: eddiewebb/webhook@volatilejobs:
  docker-build-tag-push:
    <<: *defaults
    steps:
      - checkout
      - setup_remote_docker:
          docker_layer_caching: true
      - run:
          name: 'docker: myservice'
          command: |
            ${CI_SCRIPTS} docker-labels >> Dockerfile
            ${CI_SCRIPTS} build-metadata
            ${CI_SCRIPTS} docker-build-tag-push . myservice
      - webhook/notify:
          endpoint: '[https://SOME-ID.execute-api.us-east-1.amazonaws.com/dev/pullrequests'](https://w603b5hwp1.execute-api.us-east-1.amazonaws.com/dev/pullrequests')
```

## 使用无服务器平台创建并运行 Lambda webhook

我取了无服务器的例子[https://github . com/server less/examples/tree/master/AWS-python-rest-API-with-dynamo db](https://github.com/serverless/examples/tree/master/aws-python-rest-api-with-dynamodb)，把它变成了我需要的。

特别是，我修改了`create.py`模块，以便通过`webhook` Orb 正确地检索 CircleCI 发布的有效载荷。一旦有效载荷信息被解析成单独的变量，我感兴趣的变量将通过`boto3.client.run_task`方法的`overrides`参数作为环境变量传递给 Fargate 任务。

下面是`pullrequests/create.py`模块的样子:

```
import json
import logging
import os
import time
import uuid
from datetime import datetime
import boto3REGION = os.environ['REGION']
DYNAMODB_TABLE = os.environ['DYNAMODB_TABLE']
FARGATE_CLUSTER = os.environ['FARGATE_CLUSTER']
FARGATE_TASK_DEF_NAME = os.environ['FARGATE_TASK_DEF_NAME']
FARGATE_SUBNET_ID = os.environ['FARGATE_SUBNET_ID']def run_fargate_task(dockerhub_repo, rc_service, docker_tag):
    client = boto3.client('ecs', region_name=REGION)
    response = client.run_task(
        cluster=FARGATE_CLUSTER,
        launchType = 'FARGATE',
        taskDefinition=FARGATE_TASK_DEF_NAME,
        count = 1,
        platformVersion='LATEST',
        networkConfiguration={
            'awsvpcConfiguration': {
                'subnets': [
                    FARGATE_SUBNET_ID,
                ],
                'assignPublicIp': 'ENABLED'
            }
        },
        overrides={
            'containerOverrides': [
                {
                    'name': 'automated-gitops-pr',
                    'environment': [
                        {
                            'name': 'DOCKER_REPOSITORY',
                            'value': dockerhub_repo
                        },
                        {
                            'name': 'SERVICE',
                            'value': service
                        },
                        {
                            'name': 'CIRCLE_SHA1',
                            'value': docker_tag
                        },
                    ],
                },
            ],
        },
    )return str(response)def create(event, context):
    data = json.loads(event['body']) build_num = data.get('build_num', "")
    branch = data.get('branch', "")
    username = data.get('username', "")
    job = data.get('job', "")
    build_url = data.get('build_url', "")
    vcs_revision = data.get('vcs_revision', "")
    reponame = data.get('reponame', "")
    workflow_id = data.get('workflow_id', "")
    workflow_url = data.get('workflow_url', "")
    pull_request = data.get('pull_request', "")
    user = data.get('user', "")
    api_link = data.get('api_link', "")
    status = data.get('status', "") if reponame == "":
        response = {
            "statusCode": 400,
            "body": "Did not find reponame in the request payload"
        }
        return response if job == "":
        response = {
            "statusCode": 400,
            "body": "Did not find job in the request payload"
        }
        return response if vcs_revision == "":
        response = {
            "statusCode": 400,
            "body": "Did not find vcs_revision in the request payload"
        }
        return response dynamodb = boto3.resource('dynamodb', region_name=REGION)
    table = dynamodb.Table(DYNAMODB_TABLE) timestamp = str(datetime.utcnow().timestamp())
    item = {
        'id': str(uuid.uuid1()),
        'build_num': build_num,
        'branch': branch,
        'username': username,
        'job': job,
        'build_url': build_url,
        'vcs_revision': vcs_revision,
        'reponame': reponame,
        'workflow_id': workflow_id,
        'workflow_url': workflow_url,
        'pull_request': pull_request,
        'user': user,
        'api_link': api_link,
        'status': status,
        'createdAt': timestamp,
    } # write the pullrequest to the database
    table.put_item(Item=item) # run Fargate task which creates the PR against the gitops repo
    dockerhub_repo = "myorgname/" + reponame
    service = reponame
    docker_tag = vcs_revision resp = run_fargate_task(dockerhub_repo, service, docker_tag)
    response = {
        "statusCode": 200,
        "body": resp
    }
    return response
```

无服务器平台的配置在`serverless.yaml`文件中，如下所示:

```
service: serverless-gitops-prframeworkVersion: ">=1.1.0 <2.0.0"plugins:
  - serverless-pseudo-parameterscustom:
  execRoleArn: { "Fn::Join" : ["", [ "arn:aws:iam::", { "Ref" : "AWS::AccountId" }, ":role/ecsTaskExecutionRole" ] ] }provider:
  name: aws
  runtime: python3.7
  environment:
    DYNAMODB_TABLE: ${self:service}-${opt:stage, self:provider.stage}
    REGION: us-east-1
    FARGATE_CLUSTER: utility
    FARGATE_TASK_DEF_NAME: automated-gitops
    FARGATE_SUBNET_ID: subnet-some-id
  iamRoleStatements:
    - Effect: Allow
      Action:
        - dynamodb:Query
        - dynamodb:Scan
        - dynamodb:GetItem
        - dynamodb:PutItem
        - dynamodb:UpdateItem
        - dynamodb:DeleteItem
      Resource: "arn:aws:dynamodb:${opt:region, self:provider.region}:*:table/${self:provider.environment.DYNAMODB_TABLE}"
    - Effect: Allow
      Action:
        - ecs:RunTask
      Resource: "*"
    - Effect: Allow
      Action:
        - iam:PassRole
      Resource: ${self:custom.execRoleArn}functions:
  create:
    handler: pullrequests/create.create
    events:
      - http:
          path: pullrequests
          method: post
          cors: truelist:
    handler: pullrequests/list.list
    events:
      - http:
          path: pullrequests
          method: get
          cors: trueget:
    handler: pullrequests/get.get
    events:
      - http:
          path: pullrequests/{id}
          method: get
          cors: truedelete:
    handler: pullrequests/delete.delete
    events:
      - http:
          path: pullrequests/{id}
          method: delete
          cors: trueresources:
  Resources:
    TodosDynamoDbTable:
      Type: 'AWS::DynamoDB::Table'
      DeletionPolicy: Retain
      Properties:
        AttributeDefinitions:
          -
            AttributeName: id
            AttributeType: S
        KeySchema:
          -
            AttributeName: id
            KeyType: HASH
        ProvisionedThroughput:
          ReadCapacityUnits: 1
          WriteCapacityUnits: 1
        TableName: ${self:provider.environment.DYNAMODB_TABLE}
```

一些需要注意的事项:

*   我按照[https://server less . com/blog/server less-application-for-long-running-process-fargate-lambda/](https://serverless.com/blog/serverless-application-for-long-running-process-fargate-lambda/)上的精彩文章安装了 serverless-pseudo-parameters 插件(通过“NPM I server less-pseudo-parameters-save-dev ”,这样我就能够定义变量了

```
execRoleArn: { “Fn::Join” : [“”, [ “arn:aws:iam::”, { “Ref” : “AWS::AccountId” }, “:role/ecsTaskExecutionRole” ] ] }
```

*   我在 iamRoleStatements 部分使用了这个变量，以便允许 Lambda 函数调用 PassRole API:

```
- Effect: Allow
      Action:
        - iam:PassRole
      Resource: ${self:custom.execRoleArn}
```

*   我还必须允许 Lambda 函数调用`RunTask` API:

```
- Effect: Allow
      Action:
        - ecs:RunTask
```

为了部署 Lambda 函数，我运行了:

```
serverless deploy
```

## 为自动拉式请求创建创建 ECS Fargate 集群、容器定义和任务定义

难题的最后一部分是为容器定义 Dockerfile，该容器将作为 Fargate 任务运行，并创建针对 gitops 存储库的 Pull 请求。

以下是我使用的 Dockerfile 文件:

```
FROM circleci/node:12-stretchENV CIRCLE_SHA1=""
ENV DOCKER_REPOSITORY=""
ENV GITHUB_TOKEN=""
ENV GH_EMAIL=""
ENV GH_USERNAME=""
ENV HUB_VERSION="2.12.8"
ENV KUSTOMIZE_VERSION="3.2.1"
ENV ENVIRONMENT="dev"
ENV SERVICE=""
ENV GITOPS_REVIEWERS="github-id-of-reviewer"WORKDIR /tmp# Download kustomize
RUN wget -q "[https://github.com/kubernetes-sigs/kustomize/releases/download/kustomize/v${KUSTOMIZE_VERSION}/kustomize_kustomize.v${KUSTOMIZE_VERSION}_linux_amd64](https://github.com/kubernetes-sigs/kustomize/releases/download/kustomize/v${KUSTOMIZE_VERSION}/kustomize_kustomize.v${KUSTOMIZE_VERSION}_linux_amd64)"
RUN sudo install --mode 755 kustomize_kustomize.v"${KUSTOMIZE_VERSION}"_linux_amd64 /usr/local/bin/kustomize# Download hub
RUN wget -q "[https://github.com/github/hub/releases/download/v${HUB_VERSION}/hub-linux-amd64-${HUB_VERSION}.tgz](https://github.com/github/hub/releases/download/v${HUB_VERSION}/hub-linux-amd64-${HUB_VERSION}.tgz)"
RUN tar xfz hub-linux-amd64-"${HUB_VERSION}".tgz
RUN sudo install --mode 755 hub-linux-amd64-"${HUB_VERSION}"/bin/hub /usr/local/bin/hubCOPY . .CMD ["./automate-gitops-pull-request.sh"]
```

注意环境变量`CIRCLE_SHA1`、`DOCKER_REPOSITORY`和`SERVICE`，它们是从`overrides`参数中的 Lambda 函数传递过来的。

实际的 PR 创建是在`automate-gitops-pull-request.sh` shell 脚本中完成的。这是它最重要的部分:

```
# Clone gitops repository and configure username and email for signing off commits
cd "$(mktemp -d "/tmp/gitops-XXX")"
hub clone "https://${GITHUB_TOKEN}[@github](http://twitter.com/github).com/org/gitops.git"
cd gitops
hub config user.name "${GH_USERNAME}"
hub config user.email "${GH_EMAIL}"
cd kustomize/"${SERVICE}"/overlays/"${ENVIRONMENT:-dev}"# Create new branch
hub checkout -b "update-image-${SERVICE}-${CIRCLE_SHA1}"# Modify image tag in kustomization.yaml by calling 'kustomize edit set image'
kustomize edit set image docker.io/"${DOCKER_REPOSITORY}":"${CIRCLE_SHA1}"
hub add kustomization.yaml# Commit with sign-off
hub commit -s -m "changed ${SERVICE} image tag to ${CIRCLE_SHA1}"# Push branch to origin
hub push --set-upstream origin "update-image-${RC_SERVICE}-${CIRCLE_SHA1}"# Create PR
hub pull-request --no-edit -r "${GITOPS_REVIEWERS}"
```

我在本地构建并标记了一个 Docker 映像，然后将它推送到 AWS ECR。我通过 [AWS ECS“首次运行”向导](https://console.aws.amazon.com/ecs/home?region=us-east-1#/firstRun)为上面定义的 Docker 映像创建了一个 ECS 集群和一个 Fargate 任务定义和容器定义。下面是一个任务定义 JSON 文件，它对应于我通过 GUI 创建的文件:

```
$ cat ecs-task-definition.json 
{
  "executionRoleArn": "arn:aws:iam::ACCOUNTID:role/ecsTaskExecutionRole",
  "containerDefinitions": [
    {
      "logConfiguration": {
        "logDriver": "awslogs",
        "options": {
          "awslogs-group": "/ecs/automated-gitops",
          "awslogs-region": "us-east-1",
          "awslogs-stream-prefix": "ecs"
        }
      },
      "entryPoint": [],
      "portMappings": [],
      "command": [],
      "cpu": 1,
      "environment": [
        {
          "name": "CIRCLE_SHA1",
          "value": ""
        },
        {
          "name": "DOCKER_REPOSITORY",
          "value": ""
        },
        {
          "name": "GH_EMAIL",
          "value": "my-github-email"
        },
        {
          "name": "GH_USERNAME",
          "value": "my-github-username"
        },
        {
          "name": "HUB_VERSION",
          "value": "2.12.8"
        },
        {
          "name": "KUSTOMIZE_VERSION",
          "value": "3.2.1"
        },
        {
          "name": "ENVIRONMENT",
          "value": "dev"
        },
        {
          "name": "SERVICE",
          "value": ""
        },
        {
          "name": "GITOPS_REVIEWERS",
          "value": "github-id-of-reviewer"
        }
      ],
      "secrets": [
        {
          "valueFrom": "arn:aws:ssm:us-east-1:ACCOUNTID:parameter/fargate/automated-gitops-pr/github-token",
          "name": "GITHUB_TOKEN"
        }
      ],"memoryReservation": 1024,
      "volumesFrom": [],
      "image": "ACCOUNTID.dkr.ecr.us-east-1.amazonaws.com/automated-gitops-pr",
      "essential": true,
      "links": [],
      "name": "automated-gitops-pr"
    }
  ],
  "placementConstraints": [],
  "memory": "1024",
  "taskRoleArn": "arn:aws:iam::773713188930:role/ecsTaskExecutionRole",
  "family": "automated-gitops",
  "requiresCompatibilities": [
    "FARGATE"
  ],
  "networkMode": "awsvpc",
  "cpu": "512",
  "volumes": []
}
```

注意，`GITHUB_TOKEN`环境变量使用了 SSM 参数存储秘密，`automate-gitops-pull-request.sh`脚本使用它来以写入模式克隆 gitops repo。我手动创建了一个名为`/fargate/automated-gitops-pr/github-token`的 SSM 变量，并将其值设置为 gitops repo 的适当 Github 令牌。

为了注册上面创建的任务定义，我使用了`aws`命令行:

```
#!/bin/bashREGION=us-east-1aws ecs register-task-definition --region $REGION --cli-input-json file://ecs-task-definition.json
```

为了测试它，我使用了这个 shell 脚本:

```
#!/bin/bashREGION=us-east-1
CLUSTER=utility
TASK_DEF_NAME=automated-gitops
SUBNET_ID=subnet-some-idaws ecs run-task --region $REGION --cluster $CLUSTER --launch-type FARGATE --task-definition $TASK_DEF_NAME \
--network-configuration "awsvpcConfiguration={subnets=[$SUBNET_ID],assignPublicIp=ENABLED}" --overrides file://overrides.json
```

其中文件`overrides.json`包含从一个任务调用到另一个任务调用的变量:

```
{
    "containerOverrides": [{
        "name": "automated-gitops-pr",
        "environment": [
            {
            "name": "DOCKER_REPOSITORY",
            "value": "reactioncommerce/example-storefront"
            },
            {
            "name": "SERVICE",
            "value": "myservice"
            },
            {
            "name": "CIRCLE_SHA1",
            "value": "some-sha-obtained-from-circleci"
            }]
    }]
}
```

现在你知道了。现在，当 CircleCI 为与给定服务相关联的 GitHub repo 运行管道时，Lambda webhook 将在该服务的 Docker 映像被推送到 DockerHub 后被调用。webhook 将决定为哪个服务和哪个环境创建 Pull 请求，并将适当的变量传递给 Fargate 任务，该任务将创建 PR。