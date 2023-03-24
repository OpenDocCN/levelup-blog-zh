# 音频和视频漂移校正食谱

> 原文：<https://levelup.gitconnected.com/audio-video-drift-correction-cookbook-4f3e71060ac7>

![](img/7041621f69ffad052a671cd18f193a79.png)

图片来源:作者插画。

## 当您的音频与视频不匹配时该怎么办。

# 介绍

如果您有一个涉及录制视频的项目，特别是如果它涉及录制许多视频，您可能会遇到一个称为音频漂移的问题。如果你遇到这个问题，我有好消息，坏消息，还有更多好消息给你。

## 好消息是

好消息是，YouTube 提供了大量视频，展示如何纠正这个问题(以及如何避免)。但可能为时已晚。。。

## 坏消息是…

你可能有几十个(或者几百个)视频。而且，不知何故你势如破竹。你没有注意到音频在漂移，直到为时已晚。用点击式软件做这件事要花很长时间！

![](img/42e88d419609b0eb5561d7cb0fbc2fc2.png)

图片来源:作者插画。左边是一个沮丧的视频创作者。右边是一些代码，可能有助于解决视频制作者的问题。

你的 Udemy 课程被毁了吗？你的 Udacity 课程毁了吗？你会花几个小时在织布机上记录这些吗？你会雇一个五英尺或更高的自由职业者来帮你修理吗？你会让实习生做这项工作吗？或者，你是否觉得自己在做这项工作时遇到了困难？

## 回到好消息

如果您有多个视频，它们都经历相同的漂移量，您可以在 Python 中使用 MoviePy 进行编辑。我实际上花了几周时间寻找这个问题的示例解决方案。运气不好。没找到。

为了节省他人的时间和烦恼，我分享我的代码。这些代码并不适用于每个项目。但是，当您创建一个代码块来修复视频中的音频时，它可以作为一本食谱。

![](img/9028fd6962b3352d36195718947ce8b1.png)

图片来源:作者插画《CodeFetti》。看我在那里做了什么，lol。

# 烹饪书(代码)

当然，您需要安装库。

```
pip install moviepy
```

接下来，去 Python 吧。这是为我工作的代码。

```
from moviepy.editor import *
import osdef fix_drift(file=None, drift=.4):
    if file != None:
        if os.path.isfile(file):
            vid = VideoFileClip(file, audio=False)
            audio = AudioFileClip(file)
            vid = vid.subclip(drift, vid.duration)
            audio = audio.set_end(audio.duration - drift)
            if audio.duration == vid.duration:
                vid = vid.set_audio(audio)
                vid.write_videofile('{}.FxdAudio.mp4'.format(file))
            else:
                print('Problem with audio and video durations.')
    else:
        print('Incorrect file specification')
```

def 语句让您指定(以秒为单位)音频漂移有多大(多长)。通常情况下，任何超过 0.3 到 0.4 秒的时间都是显而易见和令人讨厌的。提醒一下，这段代码不会检测到偏移量，你需要在视频播放器中确定，如果你的视频偏移量不到. 4，你可以用`drift`参数指定一个不同的长度。

前几行是标准导入。在那之后，我们就要定义一个函数，这个函数将使在一系列文件上完成这个重复的任务变得更加容易。

您会注意到输出文件名规范是粗略的。我建议你更新一下。一点字符串格式化的魔法将提高你的输出。对我来说，这就足够了。

这里的要点是 MoviePy 打开原始视频剪辑(没有音频`audio=False`)。然后 MoviePy 用`AudioFileClip`打开原音频。之后，通过用`.subclip()`将视频的一小部分移向前方来剪辑原始视频。这一小部分等于音频漂移的量(长度)。倒数第二个代码要求 MoviePy 通过用`.set_end()`在末尾剪切一小部分音频来减少音频的长度，相当于那个漂移的长度。

在快速检查以确保长度匹配后，MoviePy 会使用新名称导出新视频。

要实现，为自己编写一些额外的代码。类似于:

```
files = ['first file.mp4', 'second file.mp4', 
         'third file.mp4']for f in files:
    fix_drift(f)
```

[](https://adamrossnelson.medium.com/membership) [## 加入我的介绍链接媒体-亚当罗斯纳尔逊

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

adamrossnelson.medium.com](https://adamrossnelson.medium.com/membership) 

# 一些限制

有许多报道称，MoviePy 视频在 MacOS 上无法播放音频。一个变通方法是将你的视频上传到 Google Drive，让 Google Drive 处理视频，然后在那里观看。音频会工作。

另一个选择是不在 MacOs 上工作，至少在 MoviePy(或苹果)解决这个 bug 之前。

另一个已知的问题是，在某些计算机上，MoviePy 在编写视频时会抛出错误。类似`TypeError: must be real number, not NoneType`的东西。我无法诊断这个明显的错误，而且据我从网上论坛了解到的情况，其他人也没有。根据我的经验，该错误在一些计算机上持续存在，但在其他计算机上不存在。

所以如果你写视频的时候得到了`TypeError`，那就换一台电脑试试。

# 感谢阅读

感谢阅读。把你的想法和主意发给我。你可以写信只是为了说声嗨。我期待着尽快聊天。Twitter:[@ adamrossnelson](https://twitter.com/adamrossnelson)LinkedIn:[Adam Ross Nelson 在 Twitter](https://www.linkedin.com/in/arnelson) 和脸书: [Adam Ross Nelson 在脸书](https://www.facebook.com/adamrossnelson)。