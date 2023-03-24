# 使用更漂亮的、TypeScript 和 Visual Studio 代码设置 ESLint

> 原文：<https://levelup.gitconnected.com/setting-up-eslint-with-prettier-typescript-and-visual-studio-code-d113bbec9857>

![](img/6a6f9236ac752e7f837e62b97aaebaad.png)

照片由[潘卡杰·帕特尔](https://unsplash.com/@pankajpatel?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

经过适当配置后，ESLint 和 appearlier 可以在 Visual Studio 代码中优化您的 TypeScript 开发工作流。正确设置可能有点困难，但希望本指南会有所帮助。下面的示例将使用流行的 Airbnb 预设，并对其进行修改，以使其与 TypeScript 和 pretty 一起工作。

## 安装所需的依赖项

```
yarn add -D [@typescript](http://twitter.com/typescript)-eslint/eslint-plugin [@typescript](http://twitter.com/typescript)-eslint/parser eslint eslint-config-airbnb eslint-config-prettier eslint-import-resolver-typescript eslint-plugin-import eslint-plugin-json eslint-plugin-jsx-a11y eslint-plugin-prettier eslint-plugin-react prettier
```

## 配置您的`.eslintrc.js`

```
module.exports = {
  extends: ['airbnb', 'plugin:@typescript-eslint/recommended'],
  parser: '@typescript-eslint/parser',
  plugins: ['@typescript-eslint', 'prettier'],
  settings: {
    'import/parsers': {
      '@typescript-eslint/parser': ['.ts', '.tsx'],
    },
    'import/resolver': {
      typescript: {},
    },
  },
  rules: {
    'react/jsx-filename-extension': [2, { extensions: ['.js', '.jsx', '.ts', '.tsx'] }],
    'import/no-extraneous-dependencies': [2, { devDependencies: ['**/test.tsx', '**/test.ts'] }],
    '@typescript-eslint/indent': [2, 2],
  },
};
```

## 将以下内容添加到您的`package.json`

```
"scripts": {
  "lint": "eslint . --ext .js,.jsx,.ts,.tsx"
}
```

此时，ESLint 将与 TypeScript 一起正常工作。您可以通过运行`yarn lint`来 lint 您项目的 JavaScript 和 TypeScript 文件。虽然这很有帮助，但要获得与 Visual Studio 代码的完全集成，只需再做一些配置更改。

## 安装以下 Visual Studio 代码扩展

```
dbaeumer.vscode-eslint
esbenp.prettier-vscode
```

## 将以下代码添加到您的 Visual Studio 代码中`settings.json`

```
"[javascript]": {
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": false
},
"[javascriptreact]": {
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": false
},
"[typescript]": {
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": false
},
"[typescriptreact]": {
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": false
},
"eslint.autoFixOnSave": true,
"eslint.validate": [
  "javascript",
  "javascriptreact",
  {
    "autoFix": true,
    "language": "typescript"
  },
  {
    "autoFix": true,
    "language": "typescriptreact"
  }
],
"prettier.eslintIntegration": true,
```

就是这样！现在，您应该已经完成了 Visual Studio 代码集成。当你违反林挺规则时，你会收到视觉警告，当你保存文件时，ESLint 会尝试使用 Prettier 解决任何问题。这对 JavaScript 和 TypeScript 都适用。