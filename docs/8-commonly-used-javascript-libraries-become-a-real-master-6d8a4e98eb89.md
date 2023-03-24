# 8 个常用 JavaScript 库，成为真正的高手

> 原文：<https://levelup.gitconnected.com/8-commonly-used-javascript-libraries-become-a-real-master-6d8a4e98eb89>

## 掌握这些 JavaScript 工具库，让你的项目看起来很棒。

![](img/55b7af45025b6680209c857e0c1e036b.png)

专家和普通人的重要区别在于，他们善于使用工具，把更多的时间留给规划和思考。写代码也是如此。有了合适的工具，您就有更多的时间来规划架构和克服困难。今天，我将与你分享最流行的 JavaScript 库。

## 1.qs

一个轻量级的 url 参数转换 JavaScript 库。

```
npm install qs
```

**基本用法**

```
import qs from 'qs'qs.parse('user=maxwell&age=32'); 
// return { user: "maxwell", age: "32" }qs.stringify({ user: "maxwell", age: "32" }); 
//return user=maxwell&age=32
```

官方网站:【https://www.npmjs.com/package/qs 

## 2.js-cookie

一个用于处理 cookies 的简单、轻量级的 JavaScript API。

```
npm install js-cookie
```

**基本用法**

```
import Cookies from 'js-cookie'Cookies.set('name', 'maxwell', { expires: 10 }) 
// cookies are valid for 10 days
Cookies.get('name') // return 'maxwell'
```

官网:[https://github.com/js-cookie/js-cookie](https://github.com/js-cookie/js-cookie)

## 3.天天网

一个处理时间和日期的极简 JavaScript 库，API 设计和 Moment.js 一样，但是大小只有 2KB。

```
npm install dayjs
```

**基本用法**

```
import dayjs from 'dayjs'dayjs().format('YYYY-MM-DD HH:mm')  

dayjs('2022-11-1 12:06').toDate() 
```

官方网站:[https://day.js.org/](https://day.js.org/)

## 4.Animate.css

一个跨浏览器的 css3 动画库，内置了很多典型的 css3 动画，兼容性好，简单易用。

```
npm install animate.css
```

**基本用法**

```
<h1 **class**="animate__animated animate__bounce">
   **An** animated element
</h1> **import** 'animate.css'
```

官方网站:[https://animate.style/](https://animate.style/)

## 5.animejs

一个强大的 Javascript 动画库。可以使用 CSS3 属性、SVG、DOM 元素和 JS 对象创建各种高性能、平滑过渡的动画效果。

```
npm install animejs
```

**基本用法**

```
<div class="ball" style="width: 50px; height: 50px; background: blue"></div>import anime from 'animejs/lib/anime.es.js'// After the page is rendered, execute
anime({
  targets: '.ball',
  translateX: 250,
  rotate: '1turn',
  backgroundColor: '#F00',
  duration: 800
})
```

官方网站:[https://animejs.com/](https://animejs.com/)

## 6.lodash.js

一个现代化的 JavaScript 实用程序库，提供模块化、高性能和额外功能。

```
npm install lodash
```

**基本用法**

```
import _ from 'lodash'_.max([4, 2, 8, 6]) // returns the maximum value in the array  => 8_.intersection([1, 2, 3], [2, 3, 4]) 
// returns the intersection of multiple arrays => [2, 3]
```

官方网站:[https://lodash.com/](https://lodash.com/)

## 7.vConsole

一个轻量级、可扩展的移动网页前端开发工具。如果你还在纠结如何在手机上调试代码，那就用它吧。

vConsole 是无框架的，你可以在 Vue 或 React 或任何其他框架应用中使用它。

```
npm install vconsole
```

**基本用法**

```
import VConsole from 'vconsole';

const vConsole = new VConsole();
// or init with options
const vConsole = new VConsole({ theme: 'dark' });

// call `console` methods as usual
console.log('Hello world');

// remove it when you finish debugging
vConsole.destroy();
```

官方网站:[https://github.com/Tencent/vConsole](https://github.com/Tencent/vConsole)

## 8.Chart.js

一个基于 HTML5 的简单、干净和吸引人的 JavaScript 图表库

面向设计者和开发者的简单而灵活的 JavaScript 图表

```
npm install chart.js
```

**基本用法**

```
<canvas id="myChart" width="500" height="500"></canvas>import Chart from 'chart.js/auto'// executed after page rendering is complete
const ctx = document.getElementById('myChart')
const myChart = new Chart(ctx, {
  type: 'bar',
  data: {
    labels: ['Red', 'Blue', 'Yellow', 'Green', 'Purple', 'Orange'],
    datasets: [
      {
        label: '# of Votes',
        data: [12, 19, 3, 5, 2, 3],
        backgroundColor: [
          'rgba(255, 99, 132, 0.2)',
          'rgba(54, 162, 235, 0.2)',
          'rgba(255, 206, 86, 0.2)',
          'rgba(75, 192, 192, 0.2)',
          'rgba(153, 102, 255, 0.2)',
          'rgba(255, 159, 64, 0.2)'
        ],
        borderColor: [
          'rgba(255, 99, 132, 1)',
          'rgba(54, 162, 235, 1)',
          'rgba(255, 206, 86, 1)',
          'rgba(75, 192, 192, 1)',
          'rgba(153, 102, 255, 1)',
          'rgba(255, 159, 64, 1)'
        ],
        borderWidth: 1
      }
    ]
  },
  options: {
    scales: {
      y: {
        beginAtZero: true
      }
    }
  }
})
```

以上每个库都是我自己测试的，公司的项目基本都在用。如有疑问，欢迎在评论区交流。如果你有其他好的工具库，请分享出来，一起提高工作效率。如果你对我的文章感兴趣，可以在 [Medium](https://hyhwell.medium.com/) 或者 [Twitter](https://twitter.com/Maxwell_hyh) 上关注我。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看更多内容请参见[升级编码刊物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**将像你这样的开发人员安置在顶级初创公司和科技公司**](https://jobs.levelup.dev/talent/welcome?referral=true)