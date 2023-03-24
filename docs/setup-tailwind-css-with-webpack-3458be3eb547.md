# 用 Webpack 设置顺风 CSS

> 原文：<https://levelup.gitconnected.com/setup-tailwind-css-with-webpack-3458be3eb547>

用 Webpack 设置 Tailwind CSS 花了我相当多的时间，所以我在这里记录了我的步骤，以帮助我未来的自己和他人。

![](img/f3a12739bade31ba73f72791c42462f0.png)

[Pankaj Patel](https://unsplash.com/@pankajpatel?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 装置

1.  制作一个目录并放入光盘。

`mkdir tailwind-webpack-test && cd tailwind-webpack-test`

2.创建`package.json`。

`npm init -y`

3.安装 webpack 和必要的加载程序。

```
npm install --save-dev webpack webpack-cli webpack-dev-server babel-loader @babel/core @babel/preset-env style-loader css-loader postcss postcss-loader postcss-preset-env
```

4.安装尾翼 css。

`npm install --save-dev tailwindcss`

# 设置

1.  创建 webpack 入口和输出点。

`mkdir dist src && touch dist/index.html src/index.js src/style.css`

2.将以下内容复制到您的目录根目录下的`webpack.config.js`。

```
const path = require('path');module.exports = { mode: 'production', entry: './src/index.js', output: { path: path.resolve(__dirname, 'dist'), filename: 'bundle.js', }, module: { rules: [ { test: /\.js$/i, include: path.resolve(__dirname, 'src'), use: { loader: 'babel-loader', options: { presets: ['@babel/preset-env'],
          }, }, }, { test: /\.css$/i, include: path.resolve(__dirname, 'src'), use: ['style-loader', 'css-loader', 'postcss-loader'], }, ], }, devServer: { static: 'dist',
    watchContentBase: true, },};
```

3.将以下内容复制到`src/style.css`中。

```
@tailwind base;@tailwind components;@tailwind utilities;
```

4.通过运行`npx tailwindcss init`生成`tailwind.config.js`，并将`'./dist/*.html'`添加到清除数组。Tailwind CSS 将清除所有未被 purge 数组中指定的文件使用的样式。

```
module.exports = { purge: ['./dist/*.html'], darkMode: false, // or 'media' or 'class' theme: { extend: {}, }, variants: { extend: {}, }, plugins: [],};
```

5.将以下内容复制到`postcss.config.js`中。

```
const tailwindcss = require('tailwindcss');module.exports = { plugins: [
    'postcss-preset-env',
    tailwindcss
  ],};
```

6.打开`src/index.js`并添加这一行来导入我们的 css。

```
import './style.css';
```

7.在 HTML 中链接我们的`bundle.js`,方法是在结束 body 标签之前添加这一行。

```
<script src="bundle.js"></script>
```

8.在您的`package.json`中添加以下脚本，以优化您的工作流程。我们在构建脚本中设置节点环境，因为除非环境被设置为生产环境，否则 tailwind 不会清除任何 css 文件。

```
"scripts": {
    "start": "webpack serve --mode=development --open",
    "build": "export NODE_ENV=production && webpack"
},
```

# 试验

打开您的`dist/index.html`并添加一些 hello world 代码，然后运行`npm start`在您的默认浏览器中打开您的项目。

```
<!DOCTYPE html>
<html lang="en"><head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Tailwind Webpack Test</title>
</head><body>
  <h1 class="bg-red-900 text-white">Hello world</h1>
  <script src="bundle.js"></script>
</body></html>
```

恭喜你。！如果你看到红底白字，说明你已经成功设置了顺风:)

**注意**:如果你想使用 stylelint 来 lint 你的项目，确保在你的`.stylelintrc.json`中添加以下规则。

```
"rules": {
    "at-rule-no-unknown": [
      true,
      {
        "ignoreAtRules": [
          "extends",
          "apply",
          "tailwind",
          "components",
          "utilities",
          "screen"
        ]
      }
    ]
  }
```