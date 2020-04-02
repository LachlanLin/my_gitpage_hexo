---
title: start-with-hexo
date: 2020/3/31 20:21:22
tags:
---
# 使用Hexo构建本博客

主要参考[Hexo官方教程](https://hexo.io/zh-cn/docs/)

## 安装Nodejs

从[NodeSource](https://github.com/nodesource/distributions)安装Nodejs的v12版本

```shell
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
sudo apt-get install nodejs
```

然后将软件源更换为[淘宝源](https://developer.aliyun.com/mirror/NPM?from=tnpm)

```shell
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

会遇到EACCES权限错误, 此时不推荐用sudo覆盖, 可以参考官方给出的[解决方案](https://docs.npmjs.com/resolving-eacces-permissions-errors-when-installing-packages-globally)
更换完成之后, 以后用cnpm替换npm

所以现在安装hexo

```shell
cnpm install -g hexo-cli
```

## 建站

更具[官方教程](https://hexo.io/zh-cn/docs/setup)进行操作, **注意**: 在从远程仓库clone到本地之后需要运行以下命令安装依赖
>cnpm install

配置可以参考[官网教程](https://hexo.io/zh-cn/docs/configuration)

## 一键部署

hexo提供了快速方便的一键部署功能.

安装hexo-deployer-git

```shell
npm install hexo-deployer-git --save
```

修改配置如下:

```yaml
deploy:
  type: git
  repo:
    github: git@github.com:LachlanLin/LachlanLin.github.io.git
  branch: master
```

部署相关命令行

```shell
# 生成要发布的文件在public文件夹
hexo g
# 本地测试
hexo s
# 部署
hexo d
```

## 写作

开启[文章资源文件夹](https://hexo.io/zh-cn/docs/asset-folders)

```yaml
# _config.yml
post_asset_folder: true
```

### 相对路径引用的标签插件

```javascript
{% asset_path slug %}
{% asset_img slug [title] %}
{% asset_link slug [title] %}
```

比如在资源文件夹中有一个p1.png

```javascript
{% asset_img p1.png 720 480 'This is an example image'  %}
```

该引用表示引用资源文件夹中的p1.png, 宽720;高480, 说明文字是"This is an example image"

如果要链接到另一篇文章

```javascript
{% post_link how-to-use-fitcsvm 'how to use fitcsvm' %}
```

{% post_link how-to-use-fitcsvm 'how to use fitcsvm' %}
该链接链接到how-to-use-fitcsvm文章

### 使用javascript

markdown中也可以自由使用javascript
比如以下代码

```html
<script>
    console.log("hello js")
</script>
```

<script>
    console.log("hello js")
</script>

现在可以在浏览器控制台中看到"hello js"
