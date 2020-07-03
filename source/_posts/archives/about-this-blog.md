---
title: 关于本博客系统如何安装使用及后期更新
date: 2020-07-03 21:32:36
categories:
    - articles
tags:
    - 博客系统
---

## 关于本博客系统基本构建信息

博客框架：[Hexo](https://hexo.io/) 快速、简洁且高效的博客框架

简介：
    Hexo是一款基于 Node.js 的博客框架。所带来的超快生成速度，让上百个页面在几秒内瞬间完成渲染。
    Hexo 支持 GitHub Flavored Markdown 的所有功能，甚至可以整合 Octopress 的大多数插件。
    只需一条指令即可部署到 GitHub Pages, Heroku 或其他平台。
    强大的 API 带来无限的可能，与数种模板引擎（EJS，Pug，Nunjucks）和工具（Babel，PostCSS，Less/Sass）轻易集成。
    可使用任何兼容的模板引擎创建自己的主题。

## 只需以下几条简单的命令就可以看到一个功能丰富的博客系统

{% codeblock lang:bash %}
npm install hexo-cli -g
hexo init blog
cd blog
npm install
hexo server
{% endcodeblock %}

## 如您希望使用此博客，请往下看：

github项目地址：
https://github.com/porkio/hexo-blog-hummer


## 本仓库为博客整站代码（包含hexo框架和hummer主题）

{% codeblock lang:bash %}
npm install -g hexo-cli

git clone https://github.com/porkio/hexo-blog-hummer.git hexo-blog

cd hexo-blog/

npm install

hexo server
{% endcodeblock %}

## 主题 hummer

主题使用jade模板 + sass（gulp环境），如您希望二次开发此主题，请往下看：

必要时可参照 [gulp中文网](https://www.gulpjs.com.cn/) 来配置您的gulp环境

非必要情况下，照搬以下步骤即可：

在此之前确保 node npm 环境已安装

{% codeblock lang:bash %}
node -v
npm -v
{% endcodeblock %}

全局安装gulp脚手架

{% codeblock lang:bash %}
npm install --global gulp-cli
{% endcodeblock %}

在主题目录下再次安装gulp相关依赖

{% codeblock lang:bash %}
cd themes/hummer
npm install --save-dev gulp
{% endcodeblock %}

如以上步骤没有error，则运行gulp命令即可编译sass文件

如果有报错，首先尝试更换node版本，当你用到这个项目的时候，node版本可能已经较高，尝试版本回退可能会对你有所帮助。
目前本项目使用node版本为 v8.11.1

{% codeblock lang:bash %}
sudo n v8.11.1
{% endcodeblock %}

如果不是版本问题，则可以根据错误提示自行查阅资料，这同样也是高效学习的一种方式。

## 如何部署

静态部署

{% codeblock lang:bash %}
hexo generate or hexo g
{% endcodeblock %}

此时会在项目public文件夹下生成全站静态html文件，按照部署静态html站点的方法打包上传public文件夹内所有文件至服务器即可

关于自动部署

由于过程相对繁杂，在此就不做介绍了，如您需要，可自行查阅资料！

## 未来更新目标

1. 实现移动端自适应以及PC端视觉层面的合理化改进

2. 侧边栏功能增加：
    - 推荐阅读
    - 锚链快速跳转（文章目录）
    - 云标签

3. 首页文章列表中除文章标题外展示文章摘要信息

4. 文章页脚的上一篇文章和下一篇文章显示文章名

5. 美化代码块并增加一键copy功能
