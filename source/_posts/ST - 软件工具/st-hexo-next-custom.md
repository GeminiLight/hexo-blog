---
title: 【Blog】深度美化和定制Hexo和NexT方法
author: Gemini向光性
date: 2020-08-16 03:21:59
categories:
  - ST - 软件工具
tags:
  - Blog
  - Hexo
---

虽然我们可以搜索到许多关于Hexo博客及NexT 8主题配置和美化的教程，但是，很多文章大都聚焦于某一些常用页面的优化和定制。随着版本更迭等情况导致框架可能出现调整时，这些聚焦于某一场景的方法可能会不再适用。本文将尽可能详细描述多个场景下的hexo及nexT定制方法并且提供通用的高度自定义方案。

<!-- more -->

## 简介

Hexo的NexT主题采用njk来作为HTML预处理器，使用styl来扩展css，所以可以简单的理解成 $html\subset njk$ ，$css \subset styl$。它们扩充了相应的功能和语法支持来更加高效的架构网页，当然，我们也完全可以使用html和css的语法来美化我们的网页。

## 自定义CSS

如[NexT 8 文档](https://theme-next.js.org/docs/theme-settings/custom-files.html)中所说，

> As with Data Files, you can place all custom layouts or styles in a specific location (for example: hexo/source/_data). Add the custom file to hexo/source/_data and uncomment the content under the custom_file_path section in the theme config file.

我们可以在`hexo/source/_data`文件夹中自定义CSS/JS文件。如果想让hexo在渲染时自动引入这些文件，我们只需在`next/_config.yml`，将相应文件的注释取消。  
有个有趣的事情是在next主题目录文件夹下也有一个_data文件夹：`hexo/theme/next/source/_data`，官方没有给出具体介绍，如果我们将`next/_config.yml`的`custom_file_path`前加上`theme/next`也基本与`hexo/source/_data`目录等效，故这些不再赘述。

```yaml
custom_file_path:
  #head: source/_data/head.njk
  #header: source/_data/header.njk
  #sidebar: source/_data/sidebar.njk
  #postMeta: source/_data/post-meta.njk
  #postBodyEnd: source/_data/post-body-end.njk
  #footer: source/_data/footer.njk
  #bodyEnd: source/_data/body-end.njk
  #variable: source/_data/variables.styl
  #mixin: source/_data/mixins.styl
  #style: source/_data/styles.styl

  # 定位到 hexo/theme/next/source/_data
  #style: theme/nextsource/_data/styles.styl
```

此时，hexo再进行渲染时，也会引入这些文件，当其中的样式与默认样式冲突时，自定义样式优先级高，便会覆盖默认样式。但我们会发现，这种方式只能自动引入与上述文件重名的自定义文件，所以，我们可以将所有修改或者新添的样式写入`styles.styl`等文件。但是，这样的方法有些确定就是不方便管理。比如，我们打算自定义一个`custom-about.styl`文件来专门优化“关于”页面，这时我们就要手动的把该文件引入。有以下几种方法可供参考：

1. 在`styles.styl`文件中引入
  
  ```css
  @import "custom-about.styl"
  ```

2. 在`about.md`文件中引入

  ```markdown
  <!-- 注意文件路径是否正确 -->
  <link rel="stylesheet" type="text/css" href="_data/custom-about.styl">
  ```

3. 在`layout.njk`等其他会被自动渲染的文件中引入

  ```html
  <link rel="stylesheet" type="text/css" href="_data/custom-about.styl">
  ```

### 覆盖默认样式

我们可以在`styles.styl`等文件中来定义class新的样式，最简单的方式就是通过`F12`来查看网页资源，然后选择想要修改的元素，查看元素所带的class，然后在`styles.styl`中重写即可。

### 自定义图标

NexT的默认图标库是Font Awesome，它并不包括很多国内主流网站的图标，比如Bilibili、知乎等。但我们可以通过自定义的方式来增加对这些图标的支持。下面以Bilibili为例

1. 下载`bilibili.svg`，保存到`theme/next/source/images/bilibili.svg`
2. 在`theme/next/source/_data/styles.styl`添加样式

  ```css
  .fab.fa-bilibili {
    background: url(/images/bilibili.svg);
    background-position: 50% 75%;
    background-repeat: no-repeat;
    height: 1rem;
    width: 1rem;
  }
  ```

3. 确保已经在`next/_config.xml`中开启了自定义文件路径

```yaml
custom_file_path:
  style: source/_data/styles.styl
```

4. 在`next/_config.xml`配置相应图标

```next/_config.yml
social:
  Bilibili: https://space.bilibili.com/userid/ || fab fa-bilibili
```

## 自定义JS

类比CSS。

## 自定义Markdown

### Markdown内嵌HTML

作为标记语言，Markdown在某些场景下支持对HTML、CSS和JS的扩展，即我们可以在Markdown中写入HTML语法，甚至可以内嵌CSS和JS。以下为我的博客404页面的示例：

```markdown
---
title: 404
date: 2020-01-09 13:25:01
layout: false
commit: false
permalink: /404
---
<html lang="zh">
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>404</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="/404/style.css">
  </head>

  <body style="margin: 0px">
  <div class="container"><div class="row"><div class="col-md-6 align-self-center"><img src="/404/404.svg"></div>
  <div class="col-md-6 align-self-center">
  <h1>404</h1><h2>UH OH! 页面丢失</h2><p>您所寻找的页面不存在。你可以点击下面的按钮，返回主页。</p>
  <a href="https://www.geminilight.cn/"><button class="btn blue">返回首页</button></a>
  </div>
  </div>
  </div>
  </body>
</html>
```

需要注意的是，在hexo渲染时，对markdown的缩进有较高的要求。至少在我实际操作过程中，如果HTML标签前有超过4个空格的缩进，便会被渲染为全文字。

### Markdown渲染外部html文件

我们也可以在外部新建一个完整的html页面，然后在md文件中引入，并加入相应标签来引入渲染

```markdown
<span style="width:100%; height:260;border:none;text-align:center"> 
	<iframe allowtransparency="yes" frameborder="0" width="100%" height="88" src="url">
	</iframe>
</span>
```

url 指向的是独立的 HTML 文件的路径，可以直接放在我们的md文件下。  
此外，为了避免html文件被编译而被嵌套主题样式，我们配置主题的`_config.xml`来跳过对该文件的渲染：

```yaml
# 跳过渲染
skip-render:
  - README.md
  - xxx.html  # 禁止渲染xxx.html文件
  - *.html    # 禁止渲染所有html文件
```

## 自定义页面

### 不含博客框架的页面

这种类型的页面指完全自定义的，即整个页面不会出现博客的header、sidebar、footer等。比如，我们可以定制一个小游戏的404界面。这里以完全自定义的xxx页面为例，创建流程如下：

1. 在hexo主目录文件夹下，输入命令：

```powershell
hexo new page xxx
```

该命令会自动在`hexo/source`下创建`xxx/xxx.md`文件夹和md文件。

2. 编辑`xxx.md`，添加`layout: false`属性

```markdown
title: xxx
type: xxx
layout: false
```

设置`layout: false`会使hexo在渲染页面时，不自动为页面渲染基本的导航栏、侧边栏等，所以该页面需要完全自定义css和js。我们可以既可以完全在md中写js和css（并不推荐），也可以单独创建相应的JS和CSS文件再引入到md文件中，下面只对后者进行介绍。

3. 在`hexo/_data`文件夹中新建js和css文件

```powershell
|
|-- xxx.js
|-- xxx.css
```

4. 在`xxx.md`中引入

```markdown
<link rel="stylesheet" type="text/css" href="_data/xxx.css">

<!-- content -->

<script type="text/javascript" src="xxx.js">
```

### 含博客框架的子页面

这种类型的页面指依赖于hexo生成的博客页面主框架，比如我们的Tags标签页、Categories分类页等。这种页面并非完全定制的，这也会带来一个优点：我们可以利用Hexo和NexT主题附带的样式和JS来制作的页面。

1. 在hexo主目录文件夹下，输入命令：

```powershell
hexo new page xxx
```

2. 同自定义css方法，可在`hexo/_data`编辑相应文件并引入渲染

```powershell
|
|-- xxx.js
|-- xxx.css
```

## 总结
