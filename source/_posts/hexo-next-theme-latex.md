---
title: Hexo中使用markdown的latex渲染失败
date: 2020-04-25 21:52:25
tags:
- hexo
---
# Hexo中使用markdown的latex渲染失败

在Hexo中使用markdown写数学公式时使用latex进行渲染, 但是渲染失败.

## 原因

Hexo默认使用hexo-renderer-marked引擎渲染latex公式, 该引擎不支持latex的渲染.

## 解决方法

既然原本的应勤不能使用, 那我们就更换引擎.
在我所使用的主题next的_config.yml文件中, 我们可以看到如下片段

```yml
math:
  # Default (true) will load mathjax / katex script on demand.
  # That is it only render those page which has `mathjax: true` in Front-matter.
  # If you set it to false, it will load mathjax / katex srcipt EVERY PAGE.
  per_page: true

  # hexo-renderer-pandoc (or hexo-renderer-kramed) required for full MathJax support.
  mathjax:
    enable: false
    # See: https://mhchem.github.io/MathJax-mhchem/
    mhchem: false
```

说明我们可以安装别的引擎来实现我们的latex公式渲染, 我选择了hexo-renderer-kramed.

```shell
npm uninstall hexo-renderer-marked --save
npm install hexo-renderer-kramed --save
```

安装之后, 再把上面文件中的enable改成true, 块公式就可以正常渲染了, 但是行内公式还是无法正确渲染, 这是因为这个引擎本身也跟hexo有冲突, 我们可以把博客根目录下的node_modules\kramed\lib\rules\inline.js 中的第11行和21行做出如下修改:

```json
//11行
//escape: /^\\([\\`*{}\[\]()#$+\-.!_>])/,
escape: /^\\([`*\[\]()#$+\-.!_>])/,
//21行
//em: /^\b_((?:__|[\s\S])+?)_\b|^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
em: /^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
```

现在一切就都正常了, 如果想要在博客文章中使用latex渲染, 那就在文章的Front-matter中加入

```yml
mathjax: true
```
