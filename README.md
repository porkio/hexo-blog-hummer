## 本仓库为博客整站代码（包含hexo框架和hummer主题）

npm install -g hexo-cli

git clone https://github.com/porkio/hexo-blog-hummer.git hexo-blog

cd hexo-blog/

npm install

hexo server

## 主题 hummer

主题使用jade模板 + sass（gulp环境），如您希望二次开发此主题，请往下看：

必要时可参照 [gulp中文网](https://www.gulpjs.com.cn/) 来配置您的gulp环境

非必要情况下，照搬以下步骤即可：

在此之前确保 node npm 环境已安装

node -v

npm -v

cd themes/hummer

全局安装gulp脚手架

npm install --global gulp-cli

在主题目录下再次安装gulp相关依赖

npm install --save-dev gulp

如以上步骤没有error，则运行gulp命令即可编译sass文件

如果有报错，首先尝试更换node版本，当你用到这个项目的时候，node版本可能已经较高，尝试版本回退可能会对你有所帮助。
目前本项目使用node版本为 v8.11.1

sudo n v8.11.1

如果不是版本问题，则可以根据错误提示自行查阅资料，这同样也是高效学习的一种方式。

## 如何部署

静态部署

hexo generate or hexo g

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
