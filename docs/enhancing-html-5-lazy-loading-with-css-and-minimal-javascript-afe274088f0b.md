# 用 CSS 和最小 JavaScript 增强 HTML 5 的延迟加载。

> 原文：<https://levelup.gitconnected.com/enhancing-html-5-lazy-loading-with-css-and-minimal-javascript-afe274088f0b>

![](img/055b6d376269b54583136e92ca2a658b.png)

你问的“懒装”是什么？这是一种技术，通过这种技术，网站上的图像或其他内容只在需要时才加载，而不是在你第一次访问页面时一次性加载。这可能会导致网站在所有内容加载完毕之前很久就对用户有用了。为什么要浪费带宽和时间来加载屏幕底部以外的图像呢？只有当你滚动到它们的时候才加载它们，这样可以节省每个人的时间。

很长一段时间以来，JavaScript 中有很多不完整的、非语义的、不可访问的诡计来实现图像的“延迟加载”。大多数系统需要臃肿的标记，不能很好地降级，经常用`<noscript>`复制图像信息两次，还有一大堆其他的诡计。

这种欺骗和对脚本的过度使用已经让我很长一段时间回避这些类型的效果，把它们作为有缺陷的/坏的垃圾来拒绝。当有人问我“不…你最好不要那样做”时，我的下意识反应就是简单的“不…你最好不要那样做。”

但是网络技术的进步使得人们使用的大量多余的脚本变得过时，因此我不得不重新评估这个立场。我试了一下，以确保我没有过时，并且对这个话题充满了****。*有时候你真的需要在…* 之前检查一下自己

我发现 HTML 5 以`loading=”lazy”`属性的形式为我们提供了一种机制。如果您对此不熟悉，我建议您阅读 MDN 上关于以下主题的文章:

[](https://developer.mozilla.org/en-US/docs/Web/Performance/Lazy_loading#Images_and_iframes) [## 惰性装载

### 惰性加载是一种将资源识别为非阻塞(非关键)的策略，只在需要时加载这些资源。这是一个…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/Performance/Lazy_loading#Images_and_iframes) 

不要再浪费时间制作图像代码的多个副本，添加各种 JavaScript 克隆/元素创建等等。只需将图像放入，设置属性，然后嘣，工作懒惰加载。*在支持的浏览器中。*

虽然这确实给了我们完全优雅降级的基本机制，但它并没有真正提供很多“控制”图像加载后如何显示的方式。如果有任何 CSS 挂钩，可以很容易地添加一个简单的淡入、放大或其他类似的动画，因为有些东西正在“加载”或“已加载”，但这些目前还不存在…所以我们回到 JavaScript 上来。

但是如果我们让 CSS 来做我们所有的重活，并保持 HTML 的理智和理性，我们可以用最少的代码开销来完成它，将 JavaScript 在这个过程中的角色降到只不过是类交换。

*注意，这里所有的 CSS 都假设正在使用复位。*

# 步骤 1:基本实现

让我们从简单开始，使用简单的不透明度和缩放动画来显示它们，而不是浏览器默认的“它们在那里”

## HTML:简洁明了

```
<span class="lazyImage" style="padding-bottom:75%;">
  <img
    src="images/test.jpg"
    alt="Describe this image, ALT is not optional"
    loading="lazy"
  >
</span>
```

比我见过的大多数实现少得多的 HTML。

我选择了语义中立的 SPAN，上面有一个类作为包装器，因为我非常不喜欢随意创建自己的标签。我知道它现在在 HTML 5 中是有效的，就像很多 HTML 5 一样，我认为这是一个错误。HTML 标签对用户代理来说应该意味着一些东西**！**用户代理除了 SPAN 之外，不知道任何虚构的标签，这完全违背了允许它们的理由。

*🎵讨厌是个很强烈的词，但是我真的真的真的真的不喜欢你…🎵*

在所述跨度上，我放置`style="padding-top:75%"`来设置纵横比。这似乎是处理这个问题的最快和最方便的方法，尽管它违反了关注点的分离。**大多数时候**我反对使用样式属性，我说:

> 你 100%的时间看到标记中的`<style>`，99%的时间看到标记中的`style=””`，你看到的是无知的垃圾。

但是，正如我不介意 style 标签，如果它是从 JS 中为“JS 中的 CSS”添加的，在少数情况下是有保证的……嗯……欢迎使用那 1%。就像 HTML/CSS 生成的图表的宽度/高度，或者标签云的字体大小一样，在这种情况下，我愿意换一种方式来看。

任何好的规则都不应该毫无例外地一概适用。甚至是我的。

无论如何，要计算出合适的“长宽比”,只需将高度除以宽度，然后插入一个百分比。

`<span>`里面的图像只是一个普通的图像，上面有“新的”`loading=”lazy”`。

## JavaScript:保持优雅

```
(function() { if ('loading' in HTMLImageElement.prototype) { var lazyImages = document.querySelectorAll('.lazyImage img');

    for (var img of lazyImages) {
      if (!img.complete) {
        img.parentNode.classList.add('lazyImageWaiting');
        img.addEventListener('load', lazyImageLoad, false);
        img.addEventListener('error', lazyImageError, false);
      }
    }

    function lazyImageLoad(e) {
      e.currentTarget.parentNode.classList.remove('lazyImageWaiting');
    }

    function lazyImageError(e) {
      var parent = e.currentTarget.parentNode;
      parent.classList.remove('lazyImageWaiting');
      parent.classList.add('lazyImageError');
      setTimeout(function() {
        parent.classList.add('lazyImageErrorShow');
      }, 60);
    }

  } // if 'loading' supported

})();
```

我把整个事情放在生活中，以便隔离范围。然后我们测试是否支持“加载”……这样不支持的浏览器只能得到正常的图像加载。然后我们抓取所有存在于里面的图像。lazyImage，并遍历它们。

Chrome 有可能在这个脚本运行之前设法“加载”缓存的图像，不管你把它放在哪里或者如何加载。因此，我们需要测试图像是否“完整”,如果是这样，就不要在加载动画时使用它。

如果它不完整，我们添加类“lazyImageWaiting ”,这样 CSS 就可以定义它是正在加载还是等待加载。

然后我们添加事件侦听器。一个是加载时用的，一个是加载有问题时用的。当它完成时，事件处理程序只是拉 lazyImageWaiting 类，而 error one 拉那个类并添加“lazyImageError”来替换它。错误处理程序中的超时是为了给我们的“有一个错误”类应用时间，所以我们可以应用第二个类来开始动画。如果我们一次全部加载，就不会出现错误动画。

基本上，JavaScript 需要做的就是在适当的时候交换类。

## CSS:沉重的负担。

我们从外部容器开始:

```
.lazyImage {
  position:relative;
  display:inline-block;
  width:100%;
  height:0;
  overflow:hidden;
  vertical-align:bottom;
}
```

它是内联块的，因此标记中的填充将被遵守。记住，display:inline 元素被*假定*忽略顶部/底部填充和高度声明，以及忽略它们自己的边界来计算大小。

100%宽度是为了让父级可以确定大小。在某些布局中，这可能需要一个额外的元素来控制宽度，但是如果没有 100%的宽度，那么高度填充就会变得不稳定。*不要尝试使用最大宽度。lazyImage，它螺丝纵横比保存。*

我这样做是为了让它的宽度是动态的，而不是固定的，因为在图像被真正加载之前，CSS 不知道它有多大。你也可以在 CSS 中或者从 style= " "中将它设置为固定大小，但是我更喜欢让父元素在 flow 中控制我的图像…正如你将在演示中看到的。

隐藏的溢出修复了 FF 某些版本中的一个奇怪的错误，其中`height:0;`以与行高相同的最小高度对接头部……并且垂直对齐防止了下面的任何间隙，就像你经常在内联元素中看到的那样。

图像:

```
.lazyImage img {
  position:absolute;
  top:0;
  left:0;
  width:100%;
  height:100%;
  opacity:1;
  transform:scale(1);
  transition:background 0.5s, opacity 0.5s, transform 0.5s;
}
```

在我们的容器中是绝对定位的，这样其他东西——比如加载动画、占位符背景等等——也可以围绕它定位。容器的高度/宽度也是 100%,这将保持我们在标记中声明的纵横比。当等待加载时，不透明度/缩放/过渡都处于“已加载”状态:

```
.lazyImageError img,
.lazyImageWaiting img {
  opacity:0;
  transform:scale(0);
  transition:none;
}
```

因为它在装载时是隐藏的。现在，你们中的一些人可能会想“为什么不在加载的时候添加一个类呢”。我在这里的理由是，我们仍然希望一个类说脚本是活动的，因为脚本关闭/阻止的优雅降级也是我们应该计划的。为什么有四个类——一个用于“始终”,一个用于“脚本加载和等待”,一个用于“脚本完成”,还有一个用于错误处理，而我们可以只用三个？

变换和不透明度给了我们一个很好的动画。因为它是由 CSS 控制的，所以你可以随心所欲的制作各种类型的动画。

我在这里保持简单的错误处理程序，你可以用它做更多的事情。

```
.lazyImageError:after {
  content:"Image Not Found";
  box-sizing:border-box;
  position:absolute;
  top:0;
  left:0;
  width:100%;
  height:100%;
  overflow:auto;
  display:flex;
  align-items:center;
  justify-content:center;
  padding:1em;
  background:#FF4;
  color:#F00;
  border:0.5em dashed #F00;
  transform:scale(0);
  transition:transform 0.5s;
}.lazyImageErrorShow:after {
  transform:scale(1);
}
```

图像加载失败的错误处理是我见过的许多现有实现似乎都没有想到的。

## 步骤 1 的现场演示

我已经把它放到了我的标准演示模板中，这样您就可以看到它的实际效果了:

[](https://cutcodedown.com/for_others/medium_articles/fancyLazyImages/fancyLazyImages_step1_basicFunctionality.html) [## 奇特的“惰性图像加载”演示

### 下面的图片在现代浏览器中是使用“new”-ish loading = " lazy " HTML 5 属性加载的。脚本已经…

cutcodedown.com](https://cutcodedown.com/for_others/medium_articles/fancyLazyImages/fancyLazyImages_step1_basicFunctionality.html) 

# 步骤 2:加载动画

给这个添加一个加载动画相对简单。不需要修改标记或脚本。在这种情况下，我将只使用生成的内容添加一个具有四种不同颜色边框的圆形框，并使用关键帧使其在加载活动时旋转。

```
.lazyImage:before {
  content:"";
  position:absolute;
  width:30%;
  height:0;
  padding-top:30%;
  top:50%;
  left:50%;
  opacity:0;
  transform:translate(-50%,-50%);
  transition:transform 0.5s, opacity 0.5s;
  border:1em solid;
  border-color:#0484 #0488;
  border-radius:50%;
}.lazyImageWaiting:before {
  animation:spin 1s linear infinite;
  opacity:1;
}[@keyframes](http://twitter.com/keyframes) spin {
  0% {
    transform:translate(-50%,-50%) rotate(0deg) scale(1);
  }
  50% {
    transform:translate(-50%,-50%) rotate(180deg) scale(0.7);
  }
  100% {
    transform:translate(-50%,-50%) rotate(360deg) scale(1);
  }
}
```

典型的顶部/左侧 50%平移-50%居中，加载完成时淡出动画，没有什么太不寻常的。

## 现场演示

在这个演示中，我添加了一个额外的部分来显示加载动画。
[https://cutcodedown . com/for _ others/medium _ articles/fancylayimages/fancylayimages _ step 2 _ loading animation . html](https://cutcodedown.com/for_others/medium_articles/fancyLazyImages/fancyLazyImages_step2_loadingAnimation.html)

# 第三步:占位符背景

不是每个人都喜欢加载动画，相反，可能需要一个背景图片作为占位符。事实上，这篇文章的灵感来自于[CodingForum.net](https://www.codingforum.net/forum/client-side-development/html-css/2425709-how-load-placeholder-img-before-all)上的一个问题，用户“Akbooko”正是想这么做。

问题是，如果我们只是试图加载一张图片或者使用背景图片，那张图片也必须加载，这意味着那张图片也不能马上显示。正如你们中的一些人可能已经猜到的，这个问题的答案是直接在 CSS 中使用所需图像的 base64 编码，这样它与布局同时加载，所以在开始时没有图像的“闪烁”。

因此，我们不需要加载动画代码，只需:

```
.lazyImage:before {
 content:"";
 position:absolute;
 top:0;
 left:0;
 width:100%;
 height:100%;
 background:url(
  'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACgAAAAoCAMAAAC7IEhfAAAAulBMVEUBAAD/AAAIAAD/CgoOAAAZAAAUAAD/BQUeAAAuAAA9AAA4AAAoAAAjAADmAABCAAA0AAD/IiLhAAD/HR3ZAACcAAD/GBj6AADSAAB8AABMAAD/MDD/EhKxAACYAABRAAD/KSm5AACTAAB2AABHAAD1AADGAACBAADxAACkAACJAADqAACrAABqAABmAADJAADBAACgAACFAABhAACNAAC+AABdAABVAADeAADuAACPAABwAADNAAD/OTmppkF+AAAEnklEQVQ4yxSSWbKiQBREb3rVaocuLYrRAkQQZFIBh6c+3f+22v4/kZGRechNbJQFlCxMAz/UCgmcxc2PplGfUus/17GM6A1ixYbxNh4nyN0QbJKk4vGiW23XfUl3JaY3a9ZXpLv8C3o5bChlBAD2crP7gRevljwc5uF8PkOwpBwGENoqFbzE9wXsHAfndR0v7Ivj8IGGArMsdQjQBRpICJw/D0hPQDMdCntU7FoK4mB6Kfbjm0MKsJIzyqeCAD4MYyMclnF7GeVytXH4OlsVp1NFpuNQPjnJAeWVLBl2aHmH/T5ajTa7YVqb6wjRMKazr6VWLPEFlGA0XmlPAj8YZWKgtP3Zds2J6tAmGGbz0AkERIKQ4XVWvBnO0aSUVeZYv3Q439M5k4IE+N2EGq5i92kJuF22bVHPf1s/iKLXMVCX8Z3gGm1KIfOQhftN+3RAn8pqWw2LALtDulvM7hwQicIufcl4iI/+svwwrMax9ibZrtzc7Drz385o3d8o6TQAu9M+5wY6tAyzM+mbFcVDRQGL+WZop45FFnOBhxuqEMyWZ5Xyq8mJdvpwOe7qbfq4/Vm3Ed1IMIuELSMA8WQoDe0u85jix2tq8y8dq/h0FacRcXNOGI1A8u0HlUt8MGvDLA2e6ThtuI34ulxFR2IhE9dK4AowXKiQDV++Mx+nM3tNO29Wbbmjy4NcV8NjwPw/uysB9jT29CrmVJ8D6hGsaxnse7JYg23gLUOPn57yuFHBs15ucFz99tmktn+WDn4mpBIIZpYAc6k/sLSLReYG9z33yxj239p+kVPu/7VAbstpA0EQbcazy17ZNbGwUFlSdAGZi0QAAQ74/78ruCozD/3S1ae60WWP2bfdv3Z5dah8Nhb6vjLwUrA0FkAEICIIG+N3SYH1e8rnlwZfv26uEMozNMkAAUQjYgZCmtz7MDm779SKzecEf+Z7a6Ad2HtuFQzARkZG9lisOF0V4u3bJZd5GPvF8EQDUEYwe7awkF7gFm7vOdl89Nm7Dvnp2NsNNMhLTSyUAbyQrSK89uYz7Xi9+zMM60I1Se1L0k4qBCtF6ZWDFnDAZr/vj3X5SNdreZ1n1PxdNrE0IBUzR8/DzdmoCPpldkZXDQiHZFkv0qap6owUZwrOEgKJoDRZgdE3023sdje1qe7YFwMvLxw9iEoQ8FPcsmwFft9MUdX2a1d3b2NANt25B6CZACfV06MiGSEJ6T3gre6w/FWKyUeHR7KFVWQIlkxQpIRUioigq4WVp7+nWzmJy6ZKaD6pQoRgAy0oMHEU0nmgl4fLJSabkI3znK650fnIgrQiKp10gpSFgDb4sLS6X4/Dep5tpydznZ3p3Skd9Q+dgPD8H0Uzv+abw+F8mlt8TmvXX7ajapm11ELBe20ZpAIwJHmESY+yfPGqeSlj330Tac9MQGuRUZSsgoQXyeEo0sXZZdUXZ/MW5ZYGeND/kRhCkG8xOdvL683luzsdZrV/24xZ6lmXTy4JVuRaGDA59OtlO6z25fWjeGwXZyoO/UAeAEmwN2z0c56oJGQx7a6zWaRkNru2K0/baWjxzIJjQEoARND8D7+tgCt2HKJNAAAAAElFTkSuQmCC'
 ) center center repeat;
 opacity:0;
 transition:opacity 0.5s;
}.lazyImageWaiting:before {
 opacity:1;
 transition:opacity 0s;
}
```

正如你所看到的，它和我们的动画版本没什么不同，我们只是加载了一个背景图片。我也淡出这个占位符，因为你可能会使用类似阿尔法透明的东西。当加载完成后，你不希望占位符显示出来。

## 现场演示

你瞧，它起作用了。与动画一样，我有一个额外的部分来显示背景图像。
[https://cutcodedown . com/for _ others/medium _ articles/fancylayimages/fancylayimages _ step 3 _ placeholder _ image . html](https://cutcodedown.com/for_others/medium_articles/fancyLazyImages/fancyLazyImages_step3_placeholder_image.html)

# 演示存储库

和我所有的例子一样，目录:
[https://cutcodedown . com/for _ others/medium _ articles/fancyLazyImages](https://cutcodedown.com/for_others/medium_articles/fancyLazyImages)

…是完全开放的，可以方便地访问那些令人伤感的片段，以及所有其他演示。里面还有一份整个事件的剪报。

这个演示展示了一系列标准的页面格式，这样我们就可以看到它的实际应用。我将加载器的 CSS 与演示分开，以便您更容易理解/复制到您自己的代码中。

# 更进一步

对于最小的努力，这可以扩展到使缓存/已经加载的图像有一个额外的滚动钩应用，使动画无论如何火，这是目前它不做的事情。现在缓存的图像已经显示出来了。“waiting”类可以简单地添加到所有元素中，如果它不是基于“loading”的，则改为应用 scroll。脚本检查的地方！img.complete，如果已经完成，只需勾上/标记该文件以进行滚动检查。

正如我提到的，由于动画是 CSS 驱动的，你可以很容易地把它换成任何其他标准的 CSS 动画。玩它，玩得开心。令人惊讶的是，CSS 现在使所有不同类型的动画变得非常简单。

# 问题和关切

有些事情需要注意；有些是问题，有些不是。

## 浏览器/系统负载

虽然在我自己的测试中，从我的媒体中心的锐龙 5 3600 + GTX 1070 到我的工作站的赛扬 J1900 和 GT 740 都运行流畅，但我收到了一些动画断断续续/不平稳的报告。我怀疑这要么是操作系统/浏览器组合的问题，要么是较低的显卡(如英特尔集成显卡)跟不上我选择的动画。

这可能是 CSS3 动画不时出现的问题，为此，当涉及到这些效果时，你有时必须记住“把它放在你的裤子里”。我怀疑如果你将动画/元素的数量保持在最低限度，这是很好的…但是请记住，你在一个页面中填充的动画/元素越多，你给用户带来的 CPU/GPU 负载就越多。你放了 30 多个这样的图标，让它们一次显示在屏幕上，如果系统死机了，不要惊讶。

## 微软公司出品的 web 浏览器

它也有一个问题，Interdebt Exploder 根本无法处理许多现代布局和效果。我的解决方案——正如你在演示标记中看到的——是将 X-UA-Compatible 设置为 IE9，恢复条件注释功能，将所有的样式和脚本打包！IE 注释，这样它们就不会被发送到 IE。同样，对于 IE 浏览器，可以显示一条关于更新浏览器的警告消息。

如果你有适当的语义标记和优雅的降级，这将为遗留 IE 提供一个可用的页面——尽管既无聊又难看。在这一点上，这确实应该是 IE 支持的最终目标。我们不能一直竭尽全力让一个已经过时十年的浏览器继续显示我们所有的花哨布局和效果。这是没有成效的，不可行的，而且这是完全不切实际的期望。或者至少，除非客户愿意额外付费！

现在我并不是说把它们完全排除在外，但是如果你有适当的语义标记，可以优雅地降级为非视觉用户——屏幕阅读器、盲文阅读器、搜索引擎——向所有 IE 用户提供相同的普通标记会让他们有所作为。*就是不好看；哦，不，万岁！！！*

*告诉 IE 以这种方式滚蛋可能是另一篇文章的好主题……*

## 易接近

这种编码方式——*通过“渐进式增强”*——如果脚本或样式被阻止/不可用/不相关，页面将优雅地降级。因此，它应该对可访问性、搜索或其他类似问题没有影响。

这比大多数旧的实现更好。这并不完全是其他方法的开发者的错，因为在“加载”属性之前，没有这方面的内部机制，所以他们不得不这样做。

但即便如此，还是很少有开发者——尤其是 JavaScript 和前端框架粉丝——关注可访问性。鉴于自美国最高法院拒绝听取达美乐的上诉，支持下级法院对他们的裁决以来，所有企业都处于开放期，这确实令人不安。

# 结论

HTML 5 和 CSS3 的许多新特性让 JavaScript 不再做“繁重的工作”。以“渐进增强”的方式使用这些技术可以消除任何可访问性问题，使对 JavaScript 框架的“需求”更像神话而非事实。虽然在传统浏览器支持方面存在缺点，但这已经越来越不值得关注了。