---
title: Hexo官方文档学习笔记
date: 2020-06-04 13:17:56
categories:
 - Hexo
tags: 
 - Hexo 
---

## 一. hexo 开始使用

[官方文档](https://hexo.io/zh-cn/docs/)

### 1.1 概述

> 环境必须包括 `nodejs` 与 `git` ，从对应的官网进行对应的安装；

### 1.2 建站（测试生成静态文件并本地运行）

> 执行下面的代码：



```bash
$ cd D:hexo/    #换成你的目录
$ npm install -g hexo-cli  #安装hexo脚手架
$ hexo init     #Hexo自动在当前文件夹下下载搭建网站所需的所有文件
$ npm install   #安装依赖包

$ hexo g        #完整命令为hexo generate，生成静态文件
$ hexo s        #完整命令为hexo server，启动服务器，用来本地预览
```

> 生成的项目的目录如下，下面对各个目录功能介绍：



```m
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```

- _config.yml：网站的 配置 信息，您可以在此配置大部分的参数；
- scaffolds：模版 文件夹。当您新建文章时，Hexo 会根据 scaffold 来建立文件：`hexo new title.md`
- source：资源文件夹是存放用户资源的地方； _posts 文件夹指的是发布文章的文件夹；_drafts为文章的草稿文件夹，除 _posts 文件夹之，开头命名为 _ (下划线)的文件 / 文件夹和隐藏的文件将会被忽略。
- themes：主题 文件夹。Hexo 会根据主题来生成静态页面。

### 1.3 配置（_config.yml 中修改网站配置）

> _config.yml 文件中的 `# Home page setting` 在后面介绍;

#### 1.3.1 网站（# Site）

参数 | 描述
------------- | ------------------------------------------- |------------------
title | 网站标题
subtitle | 网站副标题
description | 网站描述
author | 您的名字
language | 网站使用的语言
timezone | 网站时区。Hexo 默认使用您电脑的时区。时区列表。比如说：America/New_York, Japan, 和 UTC 。

> description主要用于SEO，告诉搜索引擎一个关于您站点的简单描述，通常建议在其中包含您网站的关键词。author参数用于主题显示文章的作者。

#### 1.3.2 网址（# URL）

| 参数               | 描述                     | 默认值                    |
| ------------------ | ------------------------ | ------------------------- |
| url                | 网址                     |                           |
| root               | 网站根目录               |                           |
| permalink          | 文章的 永久链接 格式     | :year/:month/:day/:title/ |
| permalink_defaults | 永久链接中各部分的默认值 |                           |

> 如果您的网站存放在子目录中，例如 `http://yoursite.com/blog`，则请将您的 `url` 设为 `http://yoursite.com/blog` 并把 `root` 设为 `/blog/`。

#### 1.3.3 目录（# Directory）——建议刚学hexo时不改动

| 参数         | 描述                                                 | 默认值         |
| ------------ | ---------------------------------------------------- | -------------- |
| source_dir   | 资源文件夹，这个文件夹用来存放内容。                 | source         |
| public_dir   | 公共文件夹，这个文件夹用于存放生成的站点文件。       | public         |
| tag_dir      | 标签文件夹                                           | tags           |
| archive_dir  | 归档文件夹                                           | archives       |
| category_dir | 分类文件夹                                           | categories     |
| code_dir     | Include code 文件夹                                  | downloads/code |
| i18n_dir     | 国际化（i18n）文件夹                                 | :lang          |
| skip_render  | 跳过指定文件的渲染，您可使用 glob 表达式来匹配路径。 |                |

#### 1.3.4 文章（# Writing）

| 参数              | 描述                                 | 默认值    |
| ----------------- | ------------------------------------ | --------- |
| new_post_name     | 新文章的文件名称                     | :title.md |
| default_layout    | 预设布局                             | post      |
| auto_spacing      | 在中文和英文之间加入空格             | false     |
| titlecase         | 把标题转换为 title case              | false     |
| external_link     | 在新标签中打开链接                   | true      |
| filename_case     | 把文件名称转换为 (1) 小写或 (2) 大写 | 0         |
| render_drafts     | 显示草稿                             | false     |
| post_asset_folder | 启动 Asset 文件夹                    | false     |
| relative_link     | 把链接改为与根目录的相对位址         | false     |
| future            | 显示未来的文章                       | true      |
| highlight         | 代码块的设置                         |           |

#### 1.3.5 分类 & 标签（# Category & Tag）

| 参数             | 描述     | 默认值        |
| ---------------- | -------- | ------------- |
| default_category | 默认分类 | uncategorized |
| category_map     | 分类别名 |               |
| tag_map          | 标签别名 |               |

#### 1.3.6 日期 / 时间格式(# Date / Time format)

| 参数        | 描述     | 默认值     |
| ----------- | -------- | ---------- |
| date_format | 日期格式 | YYYY-MM-DD |
| time_format | 时间格式 | H:mm:ss    |

> Hexo 使用 Moment.js 来解析和显示时间。

#### 1.3.6 分页（# Pagination）

| 参数           | 描述                                | 默认值 |
| -------------- | ----------------------------------- | ------ |
| per_page       | 每页显示的文章量 (0 = 关闭分页功能) | 10     |
| pagination_dir | 分页目录                            | page   |

#### 1.3.6 扩展（# Extensions）

参数 | 描述
------------- | ------------------------------------------- |------------------
theme | 当前主题名称。值为false时禁用主题
deploy | 部署部分的设置

### 1.4 命令

#### 1.4.1 指令



```bash
# init : hexo初始化新建一个网站，没有folder默认当前文件夹建立网站项目，一般是在网站建立文件夹下使用gitBash直接输入 hexo init 进行初始化；
$ hexo init [folder]

# new : 新建一篇文章，若没有设置 layout 的话，默认使用 _config.yml 中的 default_layout 参数post代替，如果标题包含空格的话，请使用引号括起来；
$ hexo new [layout] <title>

# generate : 生成静态文件；可选参数：-d, --deploy   文件生成后立即部署网站 ；-w, --watch    监视文件变动
$ hexo generate 
$ hexo g  #简写

# publish : 发表草稿
$ hexo publish [layout] <filename>

# server : 启动服务器，默认网址： http://localhost:4000/。可选参数：-i  重设IP地址;  -p, --port  重设端口；-s, --static   只使用静态文件；-l, --log   启动日记记录，使用覆盖记录格式
$ hexo server

# deploy : 部署网站。可选参数：-g, --generate 部署之前预先生成静态文件
$ hexo deploy
$ hexo d  # 简写

# render : 渲染文件。可选参数：-o, --output   设置输出路径
$ hexo render <file1> [file2] ...

# migrate : 从其他博客系统 迁移内容。
$ hexo migrate <type>

# clean : 清除缓存文件 (db.json) 和已生成的静态文件 (public)，发布新的网站前应执行该命令。
$ hexo clean

# list : 列出网站资料。
$ hexo list <type>

# version : 显示 Hexo 版本。
$ hexo version
```

#### 1.4.2 选项



```bash
# 安全模式 : 在安全模式下，不会载入插件和脚本。当您在安装新插件遭遇问题时，可以尝试以安全模式重新执行。
$ hexo --safe

# 调试模式 : 在终端中显示调试信息并记录到 debug.log。当您碰到问题时，可以尝试用调试模式重新执行一次，并 提交调试信息到 GitHub。
$ hexo --debug

# 简洁模式 : 隐藏终端信息。
$ hexo --silent

# 自定义配置文件的路径 : 自定义配置文件的路径，执行后将不再使用 _config.yml。
$ hexo --config custom.yml

# 显示草稿 : 显示 source/_drafts 文件夹中的草稿文章。
$ hexo --draft

# 自定义 CWD : 自定义当前工作目录（Current working directory）的路径。
$ hexo --cwd /path/to/cwd
```

## 二. 基本操作

### 2.1 写作

创建一篇新文章的命令：



```bash
# layout 表示文章的布局，默认是post，可修改 _config.yml 中的 default_layout 参数来指定默认布局
$ hexo new [layout] <title>
```

#### 2.1.1 布局（Layout）

Hexo 有三种默认布局：post、page 和 draft，也可以自定义其他布局，都将储存到 source/_posts 文件夹；

【若不想你的文章被处理，你可以将 Front-Matter 中的layout: 设为 false 。】

#### 2.1.2 文件名称

Hexo 默认以标题做为文件名称，但您可编辑 new_post_name 参数来改变默认的文件名称，举例来说，设为 :year-:month-:day-:title.md 可让您更方便的通过日期来管理文章。

| 变量     | 描述                               |
| -------- | ---------------------------------- |
| :title   | 标题（小写，空格将会被替换为短杠） |
| :year    | 建立的年份，比如， 2015            |
| :month   | 建立的月份（有前导零），比如， 04  |
| :i_month | 建立的月份（无前导零），比如， 4   |
| :day     | 建立的日期（有前导零），比如， 07  |
| :i_day   | 建立的日期（无前导零），比如， 7   |

#### 2.1.3 草稿

草稿存放在 `source/_drafts` 目录下，通过 `hexo new draft :title` 建立，再通过 `hexo publish [layout] ` 来指定布局并发布文章；

【草稿默认不会显示在页面中，您可在执行时加上 --draft 参数，或是把 render_drafts 参数设为 true 来预览草稿。】

#### 2.1.4 模版（Scaffold）

新建文章时，Hexo 会根据 scaffolds 文件夹内相对应的文件来建立文件；
例如 ： `$ hexo new photo "My Gallery"`，表示Hexo 会尝试在 scaffolds 文件夹中寻找 photo.md，并根据其内容建立文章，以下是您可以在模版中使用的变量：

| 变量   | 描述         |
| ------ | ------------ |
| layout | 布局         |
| title  | 标题         |
| date   | 文件建立日期 |

### 2.2 Front-matter

Front-matter 是文件最上方以 --- 分隔的区域，用于指定个别文件的变量；



```m
title: Hello World
date: 2013/7/13 20:46:25
---
```

以下是预先定义的参数，您可在模板中使用这些参数值并加以利用。

| 参数       | 描述                 | 默认值       |
| ---------- | -------------------- | ------------ |
| layout     | 布局                 |              |
| title      | 标题                 |              |
| date       | 建立日期             | 文件建立日期 |
| updated    | 更新日期             | 文件更新日期 |
| comments   | 开启文章的评论功能   | true         |
| tags       | 标签（不适用于分页） |              |
| categories | 分类（不适用于分页） |              |
| permalink  | 覆盖文章网址         |              |

#### 2.2.1 分类和标签

只有文章支持分类和标签，您可以在 Front-matter 中设置。Hexo 中的分类具有顺序性和层次性，而标签没有顺序和层次，区别于其他系统；



```m
categories:
- Diary
tags:
- PS3
- Games
```

#### 2.2.2 JSON Front-matter

除了 YAML 外，你也可以使用 JSON 来编写 Front-matter，只要将 --- 代换成 ;;; 即可



```m
"title": "Hello World",
"date": "2013/7/13 20:46:25"
;;;
```

### 2.3 标签插件（Tag Plugins）

标签插件和 Front-matter 中的标签不同，它们是用于在文章中快速插入特定内容的插件。

#### 2.3.1 引用块

在文章中插入引言，可包含作者、来源和标题。

**别号： quote**



```m
方式1：提供参数， 这些参数将会被显示出来
{% blockquote [author[, source]] [link] [source_link_title] %}
content
{% endblockquote %}

方式2：没有提供参数，则只输出普通的 blockquote

方式3：引用书上的句子
{% blockquote David Levithan, Wide Awake %}
Do not just seek happiness for yourself. Seek happiness for all. Through kindness. Through mercy.
{% endblockquote %}

方式4：引用 Twitter
{% blockquote @DevDocs https://twitter.com/devdocs/status/356095192085962752 %}
NEW: DevDocs now comes with syntax highlighting. http://devdocs.io
{% endblockquote %}

方式5：引用网络上的文章
{% blockquote Seth Godin http://sethgodin.typepad.com/seths_blog/2009/07/welcome-to-island-marketing.html Welcome to Island Marketing %}
Every interaction is both precious and an opportunity to delight.
{% endblockquote %}
```

#### 2.3.2 代码块

在文章中插入代码。

**别名： code**



```m
方式1：提供参数， 这些参数将会被显示出来
{% codeblock [title] [lang:language] [url] [link text] %}
code snippet
{% endcodeblock %}

方式2：普通的代码块
{% codeblock %}
alert('Hello World!');
{% endcodeblock %}

方式3：指定语言
{% codeblock lang:objc %}
[rectangle setX: 10 y: 10 width: 20 height: 20];
{% endcodeblock %}

方式4：附加说明
{% codeblock Array.map %}
array.map(callback[, thisArg])
{% endcodeblock %}

方式5：附加说明和网址
{% codeblock _.compact http://underscorejs.org/#compact Underscore.js %}
_.compact([0, 1, false, 2, '', 3]);
=> [1, 2, 3]
{% endcodeblock %}
```

#### 2.3.3 反引号代码块(类似md中的代码块格式)

它使用三个反引号来包裹;
`[language] [title] [url] [link text] code snippet`

#### 2.3.4 Pull Quote

在文章中插入 Pull quote。



```m
{% pullquote [class] %}
content
{% endpullquote %}
```

#### 2.3.5 jsFiddle

在文章中嵌入 jsFiddle。



```m
{% jsfiddle shorttag [tabs] [skin] [width] [height] %}
```

#### 2.3.6 Gist

在文章中嵌入 Gist。



```m
{% gist gist_id [filename] %}
```

#### 2.3.7 iframe

在文章中插入 iframe。



```m
{% iframe url [width] [height] %}
```

#### 2.3.8 Image

在文章中插入指定大小的图片。



```m
{% img [class names] /path/to/image [width] [height] [title text [alt text]] %}
```

#### 2.3.9 Link

在文章中插入链接，并自动给外部链接添加 target="_blank" 属性。



```m
{% link text url [external] [title] %}
```

#### 2.3.10 Include Code

插入 source 文件夹内的代码文件。



```m
{% include_code [title] [lang:language] path/to/file %}
```

#### 2.3.11 Youtube

在文章中插入 Youtube 视频。



```m
{% youtube video_id %}
```

#### 2.3.12 Vimeo

在文章中插入 Vimeo 视频。



```m
{% vimeo video_id %}
```

#### 2.3.13 引用文章

引用其他文章的链接。



```m
{% post_path slug %}
{% post_link slug [title] %}
```

#### 2.3.14 引用资源

引用文章的资源。



```m
{% asset_path slug %}
{% asset_img slug [title] %}
{% asset_link slug [title] %}
```

#### 2.3.15 Raw

如果您想在文章中插入 Swig 标签，可以尝试使用 Raw 标签，以免发生解析异常。



```m
{% raw %}
content
{% endraw %}
```

### 2.4 资源文件夹

资源（Asset）代表 source 文件夹中除了文章以外的所有文件，例如图片、CSS、JS 文件等。

#### 2.4.1 文章资源文件夹

将 config.yml 文件中的 `post_asset_folder` 选项设为 true 来打开，有规律地提供图片和其他资源以及想要将他们的资源分布在各个文章上；当资源文件管理功能打开后，Hexo将会在你每一次通过 `hexo new [layout] `命令创建新文章时自动创建一个文件夹。这个资源文件夹将会有与这个 `markdown 文件一样的名字`。将所有与你的文章有关的资源放在这个关联文件夹中之后，你可以通过相对路径来引用它们，这样你就得到了一个更简单而且方便得多的工作流。

#### 2.4.2 相对路径引用的标签插件

通过常规的 markdown 语法和相对路径来引用图片和其它资源可能会导致它们在存档页或者主页上显示不正确。
Hexo 3 的发布，许多新的标签插件被加入到了核心代码中。这使得你可以更简单地在文章中引用你的资源。
正确的引用图片方式是使用下列的标签插件而不是 markdown 语法 `[图片上传失败...(image-3fd250-1576230489118)]`：



```m
{% asset_path slug %}
{% asset_img slug [title] %}
{% asset_link slug [title] %}

通过下面这种方式，图片将会同时出现在文章和主页以及归档页中。
{% asset_img example.jpg This is an example image %}
```

### 2.5 数据文件夹

有时候可能需要在主题中使用某些资料，而这些资料并不在文章内，并且是需要重复使用的，那么您可以考虑使用 Hexo 3.0 新增的「数据文件」功能。此功能会载入 `source/_data` 内的 `YAML 或 JSON` 文件，如此一来您便能在网站中复用这些文件；
例子：在 source/_data 文件夹中新建 menu.yml 文件：



```undefined
Home: /
Gallery: /gallery/
Archives: /archives/
```

就能在模板中使用这些资料：



```js
<% for (var link in site.data.menu) { %>
  <a href="<%= site.data.menu[link] %>"> <%= link %> </a>
<% } %>
```

渲染结果如下 :



```js
<a href="/"> Home </a>
<a href="/gallery/"> Gallery </a>
<a href="/archives/"> Archives </a>
```

### 2.6 服务器（本地预览效果）

Hexo 3.0 把服务器独立成了个别模块，安装 hexo-server 才能使用：`npm install hexo-server --save`；安装完后执行 `hexo server`启动服务器，也可以改变一些参数，参考`命令->指令`的知识；

#### 2.6.1 静态模式 && 自定义 IP



```bash
# 在静态模式下，服务器只处理 public 文件夹内的文件，而不会处理文件变动，在执行时，您应该先自行执行 hexo generate;
$ hexo server -s
# 自定义 IP地址，对于一台使用无线网络的笔记本电脑，除了指向本机的127.0.0.1外，通常还有一个192.168.*.*的局域网IP； hexo server -i 127.0.0.1 -p 5000
$ hexo server -i 192.168.1.1
```

#### 2.6.2 Pow（略）

Pow 是一个 Mac 系统上的零配置 Rack 服务器，它也可以作为一个简单易用的静态文件服务器来使用。

### 2.7 生成器

Hexo 生成静态文件指令：`$ hexo generate`，参考`命令->指令`的知识；

#### 监视文件变动 && 完成后部署



```bash
# 监视文件变动并立即重新生成静态文件，在生成时会比对文件的 SHA1 checksum，只有变动的文件才会写入；
$ hexo generate --watch
# 在生成完毕后自动部署网站，下面两个命令的作用是相同的。
$ hexo generate --deploy
$ hexo deploy --generate
# 上面命令的简介形式
$ hexo g -d
$ hexo d -g
```

### 2.8 部署

Hexo 通过指令 `$ hexo deploy` 实现网站部署到指定的服务器中，在执行此命令前要对网站根目录下的 `_config.yml` 中修改参数（**注意yml文件格式**），见下方实现 `gitee pages` 部署：



```bash
deploy:
  type: git
  repository: git@gitee.com:geekleng/hexoblog.git     #发布到gitee,还可以加上github Page 页面
  branch: master
```

部署多个 deployer，Hexo 会依照顺序执行每个 deployer:



```bash
deploy:
- type: git
  repo:
- type: heroku
  repo:
```

【YAML依靠缩进来确定元素间的从属关系。因此，请确保每个deployer的缩进长度相同，并且使用空格缩进。】

#### 2.8.1 Git

- 先安装 `hexo-deployer-git` 包：`$ npm install hexo-deployer-git --save`
- 修改`_config.yml` 中参数；



```bash
deploy:
  type: git
  repo: <repository url>
  branch: [branch]
  message: [message]
```

| 参数    | 描述                                                         |
| ------- | ------------------------------------------------------------ |
| repo    | 库（Repository）地址                                         |
| branch  | 分支名称。如果您使用的是 GitHub 或 GitCafe 的话，程序会尝试自动检测。 |
| message | 自定义提交信息(默认为 Site updated: &#123;&#123; 当前时间('YYYY-MM-DD HH:mm:ss')&#125;&#125;)|

## 三. 自定义

### 3.1 永久链接

可以在HEXO网站配置文件 `_config.yml` 配置中调整网站的永久链接或者在每篇文章的 `Front-matter` 中指定。

#### 3.1.1 变量

使用下面的变量或是使用 `Front-matter` 中的所有属性；

| 变量        | 描述                                                     |
| ----------- | -------------------------------------------------------- |
| :year       | 文章的发表年份（4 位数）                                 |
| :month      | 文章的发表月份（2 位数）                                 |
| :i_month    | 文章的发表月份（去掉开头的零）                           |
| :day        | 文章的发表日期 (2 位数)                                  |
| :i_day      | 文章的发表日期（去掉开头的零）                           |
| :title      | 文件名称                                                 |
| :post_title | 文章标题                                                 |
| :id         | 文章 ID                                                  |
| :category   | 分类。如果文章没有分类，则是 default_category 配置信息。 |

或在 `permalink_defaults` 参数下调整永久链接中各变量的默认值：



```bash
permalink_defaults:
  lang: en
```

#### 3.1.2 多语种支持

若要建立一个多语种的网站，您可修改 new_post_name 和 permalink 参数，如下：



```bash
new_post_name: :lang/:title.md  # 新的博文的文件名
permalink: :lang/:title/  # 生成的链接的格式。带井号的是默认的格式；规则也比较简单，标签前面要加英文冒号。
```

当您建立新文章时，文章会被储存到：



```bash
$ hexo new "Hello World" --lang tw
# => source/_posts/tw/Hello-World.md
```

### 3.2 主题

在HEXO的官网上找到合适的主题，下载压缩包解压到 `themes` 文件夹中或者是由git直接下载对应主题的包，再修改HEXO网站配置文件 `_config.yml` 配置中 `theme` 字段即可；主题的文件架构如下：



```m
.
├── _config.yml
├── languages
├── layout
├── scripts
└── source
```

#### 3.2.1 _config.yml

主题的配置文件。修改时会自动更新，无需重启服务器。

#### 3.2.2 languages

语言文件夹；[HEXO国际化-i18n](https://links.jianshu.com/go?to=https%3A%2F%2Fhexo.io%2Fzh-cn%2Fdocs%2Finternationalization)

#### 3.2.3 layout

布局文件夹。用于存放主题的模板文件，决定了网站内容的呈现方式，Hexo 内建 Swig 模板引擎，可以另外安装插件来获得 EJS、Haml 或 Jade 支持；[HEXO模板](https://links.jianshu.com/go?to=https%3A%2F%2Fhexo.io%2Fzh-cn%2Fdocs%2Ftemplates)

#### 3.2.4 scripts

脚本文件夹。在启动时，Hexo 会载入此文件夹内的 JavaScript 文件；[HEXO插件](https://links.jianshu.com/go?to=https%3A%2F%2Fhexo.io%2Fzh-cn%2Fdocs%2Fplugins)

#### 3.2.5 source

资源文件夹，除了模板以外的 `Asset`，例如 `CSS、JavaScript` 文件等，都应该放在这个文件夹中。文件或文件夹开头名称为 `_（下划线线）` 或隐藏的文件会被忽略。

### 3.3 模版

模板决定了网站内容的呈现方式，以下是各页面相对应的模板名称：

| 模板     | 用途     | 回退    |
| -------- | -------- | ------- |
| index    | 首页     |         |
| post     | 文章     | index   |
| page     | 分页     | index   |
| archive  | 归档     | index   |
| category | 分类归档 | archive |
| tag      | 标签归档 | archive |

#### 3.3.1 布局（Layout）

若页面结构类似，可以通过「布局」让两个模板共享相同的结构。一个布局文件必须要能显示 body 变量的内容，如此一来模板的内容才会被显示，举例来说：



```html
<!-->index.ejs 文件<-->
index

<!-->layout.ejs 文件<-->
<!DOCTYPE html>
<html>
  <body><%- body %></body>
</html>

<!-->生成文件<-->
<!DOCTYPE html>
<html>
  <body>index</body>
</html>
```

每个模板都默认使用 layout 布局，您可在 `front-matter` 指定其他布局，或是设为 `false` 来关闭布局功能，甚至可在布局中再使用其他布局来建立嵌套布局。

#### 3.3.2 局部模版（Partial）

局部模板让您在不同模板之间共享相同的组件,可利用局部模板功能分割为个别文件，让维护更加便利。



```html
<!-->partial/header.ejs文件<-->
<h1 id="logo"><%= config.title %></h1>

<!-->index.ejs文件<-->
<%- partial('partial/header') %>
<div id="content">Home page</div>

<!-->生成文件<-->
<h1 id="logo">My Site</h1>
<div id="content">Home page</div>
```

**局部变量**：局部模板中指定局部变量并使用；



```html
<!-->partial/header.ejs文件<-->
<h1 id="logo"><%= title></h1>

<!-->index.ejs文件<-->
<%- partial('partial/header', {title: 'Hello World'}) %>
<div id="content">Home page</div>

<!-->生成文件<-->
<h1 id="logo">Hello World</h1>
<div id="content">Home page</div>
```

#### 3.3.3 优化

考虑使用 Hexo 2.7 新增的局部缓存（Fragment Caching） 功能，功能借鉴于 Ruby on Rails，它储存局部内容，下次便能直接使用缓存内容，可以减少文件夹查询并使生成速度更快。可用于页首、页脚、侧边栏等文件不常变动的位置；



```html
<%- fragment_cache('header', function(){
  return '<header></header>';
});
```

如果您使用局部模板的话，可以更简单：

```
<%- partial('header', {}, {cache: true});
```

【注意：若开启了 `relative_link` 参数(把链接改为与根目录的相对位址)，请勿使用局部缓存功能，因为相对链接在每个页面可能不同】

### 3.4 变量

#### 3.4.1 全局变量

| 变量         | 描述                                             |
| ------------ | ------------------------------------------------ |
| site         | 网站变量                                         |
| page         | 针对该页面的内容以及 front-matter 所设定的变量。 |
| config       | 网站配置                                         |
| theme        | 主题配置。继承自网站配置。                       |
| _ (单下划线) | Lodash 函数库                                    |
| path         | 当前页面的路径（不含根路径）                     |
| url          | 当前页面的完整网址                               |
| env          | 环境变量                                         |

#### 3.4.2 网站变量

| 变量            | 描述     |
| --------------- | -------- |
| site.posts      | 所有文章 |
| site.pages      | 所有分页 |
| site.categories | 所有分类 |
| site.tags       | 所有标签 |

#### 3.4.3 页面变量

**页面（page）**

| 变量             | 描述                                                         |
| ---------------- | ------------------------------------------------------------ |
| page.title       | 页面标题                                                     |
| page.date        | 页面建立日期（Moment.js 对象）                               |
| page.updated     | 页面更新日期（Moment.js 对象）                               |
| page.comments    | 留言是否开启                                                 |
| page.layout      | 布局名称                                                     |
| page.content     | 页面的完整内容                                               |
| page.excerpt     | 页面摘要                                                     |
| page.more        | 除了页面摘要的其余内容                                       |
| page.source      | 页面原始路径                                                 |
| page.full_source | 页面的完整原始路径                                           |
| page.path        | 页面网址（不含根路径）。我们通常在主题中使用 url_for(page.path)。 |
| page.permalink   | 页面的完整网址                                               |
| page.prev        | 上一个页面。如果此为第一个页面则为 null。                    |
| page.next        | 下一个页面。如果此为最后一个页面则为 null。                  |
| page.raw         | 文章的原始内容                                               |
| page.photos      | 文章的照片（用于相簿）                                       |
| page.link        | 文章的外部链接（用于链接文章）                               |

**文章 (post)**: 和 page 布局类似，但是添加了下列变量。

| 变量            | 描述                     |
| --------------- | ------------------------ |
| page.published  | 如果该文章已发布则为True |
| page.categories | 该文章的所有分类         |
| page.tags       | 该文章的所有标签         |

**首页（index）**

| 变量             | 描述                                                         |
| ---------------- | ------------------------------------------------------------ |
| page.per_page    | 每页显示的文章数量                                           |
| page.total       | 总文章数                                                     |
| page.current     | 目前页数                                                     |
| page.current_url | 目前分页的网址                                               |
| page.posts       | 本页文章                                                     |
| page.prev        | 上一页的页数。如果此页是第一页的话则为 0。                   |
| page.prev_link   | 上一页的网址。如果此页是第一页的话则为 ''。                  |
| page.next        | 下一页的页数。如果此页是最后一页的话则为 0。                 |
| page.next_link   | 下一页的网址。如果此页是最后一页的话则为 ''。                |
| page.path        | 当前页面的路径（不含根目录）。我们通常在主题中使用 url_for(page.path)。 |

**归档 (archive)**：与 index 布局相同，但新增以下变量。

| 变量         | 描述                         |
| ------------ | ---------------------------- |
| page.archive | 等于 true                    |
| page.year    | 年份归档 (4位)               |
| page.month   | 月份归档 (没有前导零的2位数) |

**分类 (category)**：与 index 布局相同，但新增以下变量。

| 变量          | 描述     |
| ------------- | -------- |
| page.category | 分类名称 |

**标签 (tag)**：与 index 布局相同，但新增以下变量。

| 变量     | 描述     |
| -------- | -------- |
| page.tag | 标签名称 |

### 3.5 辅助函数——重难点

[HEXO辅助函数官方文档](https://links.jianshu.com/go?to=https%3A%2F%2Fhexo.io%2Fzh-cn%2Fdocs%2Fhelpers)

辅助函数帮助您在模版中快速插入内容。

### 3.6 国际化 (i18n)

HEXO的网站配置文件 `_config.yml` 中调整 `language` 字段值的设定，可设定多个语言来调整预设语言的顺位。



```bash
language: 
- zh-tw
- en
```

#### 3.6.1 语言文件

语言文件可以使用 YAML 或 JSON 编写，并放在主题文件夹中的 languages 文件夹。

#### 3.6. 模板

在模板中，透过 __ 或 _p 辅助函数，即可取得翻译后的字符串，前者用于一般使用；而后者用于复数字符串。

#### 3.6.3 路径

可在 `front-matter` 中指定该页面的语言，也可在 `_config.yml` 中修改 `i18n_dir` 设定，让 Hexo 自动侦测。

i18n_dir 的预设值是 :lang：`i18n_dir: :lang`，Hexo 会捕获网址中的第一段以检测语言，举例来说：



```m
/index.html => en
/archives/index.html => en
/zh-tw/index.html => zh-tw
```

### 3.7 插件

#### 3.7.1 脚本（Scripts）

若代码很简单，建议您编写脚本，您只需要把 JavaScript 文件放到 scripts 文件夹，在启动时就会自动载入。

#### 3.7.2 插件（Packages）

编写自己的插件；首先，在 `node_modules` 文件夹中建立文件夹，文件夹名称开头必须为 `hexo-`，如此一来 `Hexo`才会在启动时载入否则 Hexo 将会忽略它。

文件夹内至少要包含 2 个文件：一个是主程序 `index.js` ，另一个是 `package.json` （至少包含name, version, main 属性），描述插件的用途和所依赖的插件。

#### 3.7.3 工具

Hexo 提供的官方工具插件来加速开发：(可在npm上看各自包的用法)

- hexo-fs：文件 IO
- hexo-util：工具程式
- hexo-i18n：本地化（i18n）
- hexo-pagination：生成分页资料

#### 3.7.4 发布

发布你的插件到 [HEXO插件列表](https://links.jianshu.com/go?to=https%3A%2F%2Fhexo.io%2Fplugins%2F)

1. Fork [hexojs/site](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fhexojs%2Fsite)
2. 把库（repository）复制到电脑上，并安装所依赖的插件。



```bash
$ git clone https://github.com/<username>/site.git
$ cd site
$ npm install
```

1. 编辑 source/_data/plugins.yml，在档案中新增您的插件，例如：



```bash
- name: hexo-server
  description: Server module for Hexo.
  link: https://github.com/hexojs/hexo-server
  tags:
    - official
    - server
    - console
```

1. 推送（push）分支。
2. 建立一个新的合并申请（pull request）并描述改动

