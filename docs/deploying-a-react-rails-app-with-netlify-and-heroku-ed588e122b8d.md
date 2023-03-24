# 使用 Netlify 和 Heroku 部署 React/Rails 应用程序

> 原文：<https://levelup.gitconnected.com/deploying-a-react-rails-app-with-netlify-and-heroku-ed588e122b8d>

![](img/3df6860fc789881ea3aac39640ea0f54.png)

在我开始之前，让我给你介绍一下"[宠物轮盘赌的背景知识！](https://pet-roulette.com/)“成立即将进入部署。首先，我有两个独立的存储库。其次，我从一开始就用 Postgres 数据库创建了我的 Rails API，如果您计划部署您的应用程序，这是非常可取的。最后，我使用`create-react-app`来启动我的 React 应用程序。

关于我为什么选择使用 Netlify 而不是 Heroku 的几点说明。第一——向 Heroku 部署双重回购要复杂得多。第二，Heroku 是一个很棒的免费的 Rails 应用程序托管解决方案，但是它在 30 分钟不活动后会进入“睡眠”，重启需要整整 30 秒。加载一个网页的 30 秒等待感觉像是几十年。

Netlify 只能用于静态站点，没有“睡眠”或停机时间。网站会立即加载，这是一种更好的用户体验。如果要从 Rails API 获取数据并显示在应用程序中，最好在`componentDidMount`生命周期方法中有获取请求，并将其放在`App.js`中。这样，一旦页面被访问，API 就会从空闲状态被唤醒。您还可以利用`loading`状态来显示一个灰色框或某种短暂延迟的消息，而您的 SPA 的其余部分已经被渲染。

**第一部分——将 React 应用程序部署到 Netlify**

*   创建一个 Netlify 帐户，并选择“新站点来自”。按照提示链接您的 GitHub 帐户，并选择您希望部署的 repo。您将有机会选择哪个分支，指定构建命令`npm run build`(如果您使用 npm)和发布目录，默认为“构建”。
*   我还建议安装 [Netlify CLI](https://www.netlify.com/docs/cli/) 。使用 CLI，您可以创建新的构建版本`npm run build`，然后进行部署并查看预览`netlify deploy`，然后再部署到生产环境`netlify deploy --prod`。
*   Netlify 还使转发到自定义域变得非常容易——它会提示您输入您的域，要求您验证您是否是所有者，然后给您四台域名服务器与您的域提供商共享，就这样！

如果您在 SPA 中使用 React Router 进行客户端路由，有几个额外的步骤来防止您的生产应用程序中出现错误，因为如果用户试图从根目录以外的任何位置加载页面，Netlify 将显示“Page Not Found”错误。要解决这个问题:

*   在公共文件夹或 React 应用程序中，创建一个名为`_redirects`的新文件，并添加以下代码:

```
/*    /index.html   200
```

**第二部分—将 Rails API 部署到 Heroku**

*   将`gem 'rails_12factor'`和`gem 'foreman'`添加到您的 gemfile 并运行`bundle`。
*   在应用程序的根目录中创建一个 Procfile，并包含以下代码:

```
web: bundle exec rails s
release: bundle exec bin/rake db:migrate
```

*   更新 CORS 设置，将您的网络 URL 列入白名单。如果您还没有，将`gem 'rack-cors'`添加到您的 gemfile 并运行`bundle`。如果你已经在开发过程中使用了 CORS，你只需要添加 Netlify url 和/或你的自定义域到`cors.rb`。如果不是，在`cors.rb`中，取消注释或添加以下内容:

```
Rails.application.config.middleware.insert_before 0, Rack::Cors do
  allow do
    origins 'YOUR-NETLIFY_URL', 'YOUR-CUSTOM-DOMAIN' resource '*',
      headers: :any,
      methods: [:get, :post, :put, :patch, :delete, :options, :head]
  end
end
```

这将允许您的 React 前端向您的 API 发送和获取数据。

*   提交所有更改，并推送到 GitHub。
*   接下来，创建一个 Heroku 账号，并安装 [Heroku 工具带和 CLI](https://devcenter.heroku.com/articles/heroku-cli) 。
*   在命令行中输入`heroku create YOUR-APP-NAME`。如果您以前从未使用过 Heroku CLI，它会提示您使用用户名和密码登录。
*   如果使用主分支，输入`$ git push heroku`，如果使用其他分支，输入`git push heroku BRANCH-NAME`。

在这一点上，你应该准备好了——任何更新都可以通过重复最后一步来推送。但是，在我最近的两次部署中，我遇到了以下错误:

```
remote:        Removing bundler (2.0.1)
remote:        Bundle completed (47.21s)
remote:        Cleaning up the bundler cache.
remote: -----> Installing node-v10.14.1-linux-x64
remote:        Detected manifest file, assuming assets were compiled locally
remote: -----> Detecting rails configuration
remote: -----> Detecting rake tasks
remote: 
remote:  !
remote:  !     Could not detect rake tasks
remote:  !     ensure you can run `$ bundle exec rake -P` against your app
remote:  !     and using the production group of your Gemfile.
remote:  !     Activating bundler (2.0.1) failed:
remote:  !     Could not find 'bundler' (2.0.1) required by your /tmp/build_94d6a4f5d4fbb862672998d5d06d2506/Gemfile.lock.
remote:  !     To update to the latest version installed on your system, run `bundle update --bundler`.
remote:  !     To install the missing version, run `gem install bundler:2.0.1`
remote:  !     Checked in 'GEM_PATH=/tmp/build_94d6a4f5d4fbb862672998d5d06d2506/vendor/bundle/ruby/2.7.0', execute `gem env` for more information
remote:  !     
remote:  !     To install the version of bundler this project requires, run `gem install bundler -v '2.0.1'`
remote:  !
remote: /app/tmp/buildpacks/b7af5642714be4eddaa5f35e2b4c36176b839b4abcd9bfe57ee71c358d71152b4fd2cf925c5b6e6816adee359c4f0f966b663a7f8649b0729509d510091abc07/lib/language_pack/helpers/rake_runner.rb:106:in `load_rake_tasks!': Could not detect rake tasks (LanguagePack::Helpers::RakeRunner::CannotLoadRakefileError)
```

作为回应，我跑了`bundle exec rake -P`，还好。我更新了 bundler，移除了 bundler，然后用指定的版本重新安装。我谷歌了一下这个错误，看了几十个公开的罚单和 Stackoverflow 帖子。最终，我解决了这个问题，删除了`Gemfile.lock`中的最后两行。

```
BUNDLED WITH
   2.0.1
```

Heroku 开发中心有大量关于托管 rails 应用程序的文档，如果您遇到其他错误的话！