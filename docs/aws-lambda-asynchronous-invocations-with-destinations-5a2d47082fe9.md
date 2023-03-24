# 带有目的地的 AWS Lambda 异步调用

> 原文：<https://levelup.gitconnected.com/aws-lambda-asynchronous-invocations-with-destinations-5a2d47082fe9>

## 一种在代码中分离业务逻辑的技术。

![](img/c7a7ce829fa70d1047f8f090adc04d07.png)

Ludemeula Fernandes 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Lambda 函数一次只能做一件事。从长远来看，混合业务逻辑从来都不是好事:测试和可维护性可能会成为一种痛苦。

将业务逻辑分成多个 Lambda 函数的一种方法是使用 AWS 提供的许多[事件源](https://docs.aws.amazon.com/lambda/latest/dg/invocation-eventsourcemapping.html)来启动 Lambda 函数调用。

💁*我之前用 AWS 和 Terraform 写过一点* [*事件驱动架构简介*](/event-driven-architecture-for-dummies-222c7b930e5f) *。*

当 AWS Lambda 被异步调用时，它有一个很好的特性:将目的地附加到 Lambda 调用中。Lambda 函数完成执行后，会将响应发送到已配置的目的地。这些目的地可以是 SNS、SQS、事件桥或另一个 Lambda 函数。该配置可以有失败和成功执行的目的地。

# 我如何设置它？

如果您正在使用 Terraform，则很容易添加:

```
resource "aws_lambda_function" "test" {
  filename         = "test.zip"
  function_name    = "test"
  role             = aws_iam_role.test.arn
  handler          = "test.handler"
  source_code_hash = filebase64sha256("test.zip")
  runtime          = "nodejs14.x"
}resource "aws_lambda_function_event_invoke_config" "test" {
  function_name = aws_lambda_function.test.function_name

  destination_config {
    on_failure {
      destination = aws_sqs_queue.test.arn
    }

    on_success {
      destination = aws_sns_topic.test.arn
    }
  }
}
```

在上面的代码中，我们告诉 Lambda 函数，如果执行失败，就将执行响应发送到 SQS 队列，如果成功，就发送到 SNS 主题。

这对于记录失败的执行和调试我们的代码或者分离业务逻辑非常有用。

需要记住的一点是**添加权限给λ函数** [**执行角色**](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html) 视目的地而定:

*   **SQS—**[SQS:SendMessage](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessage.html)
*   **SNS—**[SNS:Publis](https://docs.aws.amazon.com/sns/latest/api/API_Publish.html)h
*   **λ**—[λ:invoke function](https://docs.aws.amazon.com/lambda/latest/dg/API_Invoke.html)
*   **事件桥** — [事件:PutEvents](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_PutEvents.html)

# 有什么例子？

使用 Javascript 的 Lambda 函数，我们将编写如何向 SNS 主题(或 SQS/Lambda/EventBridge)发送成功的响应。

💁*为什么不处决那些失败者？我们的 Lambda 函数已经通过在响应中发送完整的错误消息和执行细节为我们处理了这个问题。*

假设我们正在 AWS 上构建一个转录服务。我们使用 AWS 转录来为我们做转录。这个过程是异步的，所以我们需要听取来自 AWS 转录的更新。为此，我们需要使用 [CloudWatch 事件](https://docs.aws.amazon.com/transcribe/latest/dg/cloud-watch-events.html)。

转录作业完成后，我们希望将作业的状态和输出保存到 DB (1)。我们还想给自己发送一封电子邮件通知，告诉我们工作已经完成(2)。

# 我该如何编码？

Terraform 中监听 AWS 转录更新的基本设置可能如下所示:

```
resource "aws_lambda_function" "job" {
  filename         = "job.zip"
  function_name    = "job"
  role             = aws_iam_role.job.arn
  handler          = "job.handler"
  source_code_hash = filebase64sha256("job.zip")
  runtime          = "nodejs14.x"
}resource "aws_cloudwatch_event_rule" "job_status" {
  name          = "job-status"
  description   = "Transcription job status change"
  event_pattern = <<EOF
{
  "source": [
    "aws.transcribe"
  ],
  "detail-type": [
    "Transcribe Job State Change"
  ],
  "detail": {
    "TranscriptionJobStatus": [
      "COMPLETED",
      "FAILED"
    ]
  }
}
EOF
}resource "aws_lambda_permission" "allow_cloudwatch" {
  statement_id  = "AllowExecutionFromCloudWatchEventRule"
  action        = "lambda:InvokeFunction"
  function_name = aws_lambda_function.job.function_name
  principal     = "events.amazonaws.com"
  source_arn    = aws_cloudwatch_event_rule.job_status.arn
}resource "aws_cloudwatch_event_target" "target" {
  target_id = "AllowExecutionFromCloudWatchEventRule"
  rule      = aws_cloudwatch_event_rule.job_status.name
  arn       = aws_lambda_function.job.arn
}
```

我们的 Lambda 函数监听来自 AWS 的更新，并相应地采取行动，例如，将输出保存到数据库中。Lambda 函数的执行结束(1)，那么我们如何通知自己转录工作已经完成？

因为 Lambda 函数一次只能做一件事，所以添加发送电子邮件通知的功能是没有意义的(2)。因此，让我们像这样在 Lambda 函数中使用目的地的强大功能:

```
resource "aws_sns_topic" "job_email" {
  name = "job_email"
}resource "aws_lambda_function" "job_email" {
  filename         = "job_email.zip"
  function_name    = "job_email"
  role             = aws_iam_role.job_email.arn
  handler          = "job_email.handler"
  source_code_hash = filebase64sha256("job_email.zip")
  runtime          = "nodejs14.x"
}resource "aws_lambda_permission" "allow_sns" {
  statement_id  = "AllowExecutionFromSns"
  action        = "lambda:InvokeFunction"
  function_name = aws_lambda_function.job_email.function_name
  principal     = "sns.amazonaws.com"
  source_arn    = aws_sns_topic.job_email.arn
}resource "aws_sns_topic_subscription" "subscription" {
  topic_arn = aws_sns_topic.job_email.arn
  protocol = "lambda"
  endpoint = aws_lambda_function.job_email.arn
}resource "aws_lambda_function_event_invoke_config" "job" {
  function_name = aws_lambda_function.job.function_name

  destination_config {
    on_failure {
      destination = aws_sqs_queue.job.arn
    }

    on_success {
      destination = aws_sns_topic.job_email.arn
    }
  }
}
```

我们创建一个 SNS 主题和一个 Lambda 函数。Lambda 函数订阅了 SNS 主题，因此当 SNS 主题被我们的成功目标配置调用时，它会自动执行我们的 Lambda 函数。

剩下唯一要做的就是正确设置我们想要发送给 SNS topic / Lambda 函数的响应。

我们的第一个 Lambda 函数(1)从 CloudWatch 事件获取更新，因此代码可能如下所示:

```
export const handler = async (event, context) => {
  try {
    const status = event.detail.TranscriptionJobStatus
    // Do business logic, get output from job and save to DB
    return { text: `Job finish with status ${status}` }
  } catch(err) {
    throw err
  }
}
```

如果执行成功，我们的 Lambda 函数会自动将我们在 return 语句中定义的响应发送到 SNS 主题。你可以定义任何你想要的东西。当 SNS 主题执行我们的第二个 Lambda 函数(2)时，有效负载将是前一个 Lambda 函数的响应:

```
export const handler = async (event, context) => {
  try {
    const { text } = JSON.parse(event.Records[0].Sns.Message)
    // Send email with SES and use text as body
    return { statusCode: 200 }
  } catch(err) {
    throw err
  }
}
```

代码实现可以是您想要的任何东西，在本例中是发送电子邮件通知。

我们现在已经将业务逻辑分成两个 Lambda 函数，这使得我们的测试和可维护性更容易，因为每个 Lambda 函数只做一件事🎉

[](https://blog.jagonzalr.com/membership) [## 加入我的介绍链接媒体-何塞安东尼奥冈萨雷斯罗德里格斯

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

blog.jagonzalr.com](https://blog.jagonzalr.com/membership)