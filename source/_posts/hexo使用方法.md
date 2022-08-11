---
title: hexo使用方法
date: 2022-08-10 10:05:35
tags: hexo
---

## 安装hexo

在vscode建立一个文件夹(例如0808prac)，右键打开终端，输入以下终端命令：
全局安装hexo：**npm install -g hexo-cli** （或者npm i -g hexo-cli）
查看是否安装成功：**hexo -v**

安装成功显示如下图：![image-20220810101118691](hexo使用方法/20200808170347577.png)


## 创建hexo博客

- 创建项目：**hexo init 文件名** （例如hexo init my-blog）
- 进入创建好的项目：**cd 文件名** （例如cd my-blog）
- 安装依赖：**npm inistall** (或者npm i)
  生成的文件如下：关于文件的描述说明在Hexo官网有详细说明，在这里就不赘述了

![image-20220810101118691](hexo使用方法/image-20220810101118691.png)

## 写博客

创建一篇新文章或者新的页面：**hexo new 名字** (例如hexo new hexo使用方法)
生成一个hexo使用方法.md文档和一个文件夹

![image-20220810101327738](hexo使用方法/image-20220810101327738.png)

## 发布博客网站

详细文档参考：[vercel 部署](https://blog.17lai.site/posts/5311b619/#vercel-%E9%83%A8%E7%BD%B2)

## hexo配置文件

这篇文章主要是介绍一下站点配置文件中各配置项。

```yaml
# Hexo Configuration
## Docs: https://hexo.io/docs/configuration.html
## Source: https://github.com/hexojs/hexo/

# Site 
title: Hexo       # 网站的标题
subtitle: ''      # 网站子标题
description: ''   # 网站的描述，主要用于SEO，告诉搜索引擎站点的描述
keywords:		  # 网站的关键词，支持多个
author: John Doe  # 网站的作者
language: en	  # 网站采用的语言，中文可以设置为zh-CN，这里需要参考主题配置的languages目录来配置
timezone: ''	  # 网站的时区，默认使用电脑的时区

# URL
## Set your site url here. For example, if you use GitHub Page, set url as 'https://username.github.io/project'
url: http://example.com     # 网站的网址，必须以http或https开头
permalink: :year/:month/:day/:title/  # 文章永久链接的形成模版。每一篇文章都有唯一的url
permalink_defaults:    #文章永久链接中，各部分的默认值。
pretty_urls:   # 改写 permalink 的值来美化 URL
  # 是否在永久链接中保留尾部的 index.html，设置为 false 时去除
  trailing_index: true # Set to false to remove trailing 'index.html' from permalinks
  # 是否在永久链接中保留尾部的 .html, 设置为 false 时去除 (对尾部的 index.html无效)
  trailing_html: true # Set to false to remove trailing '.html' from permalinks

# Directory
source_dir: source          # 资源文件夹，这个文件夹用来存放写的博客内容。
public_dir: public          # 公共文件夹，这个文件夹用于存放生成的站点文件。
tag_dir: tags               # 标签文件夹
archive_dir: archives       # 归档文件夹
category_dir: categories    # 分类文件夹
code_dir: downloads/code    # Include code 文件夹，source_dir 下的子目录
i18n_dir: :lang             # 国际化（i18n）文件夹
skip_render:                # 跳过指定文件的渲染。匹配到的文件将会被不做改动地复制到 public 目录中。

# Writing
new_post_name: :title.md # 默认新建的文件名
default_layout: post     # 新建文档的默认布局
titlecase: false 		 # 是否要把标题中的首字符大写
external_link:           
  enable: true           # 是否要在新开tab中打开链接
  field: site		     # 对整个网站（site）生效或仅对文章（post）生效
  exclude: ''            # 需要排除的域名。主域名和子域名如 www 需分别配置
filename_case: 0         # 把文件名称转换为 (1) 小写或 (2) 大写
render_drafts: false     # 显示草稿
post_asset_folder: false # 是否启用资源文件夹。如启用，新建文档同时建立同名的资源文件夹
relative_link: false     # 是否把站内资源的链接改为站内相对链接。建议关闭。
future: true             # 文档中指定为未来时间创建
highlight:               
  enable: true           # 是否开启代码高亮功能
  line_number: true      # 代码块中是否在前面加上行号
  auto_detect: false     # 是否自动检测代码块的语言（比如xml、JavaScript、mermaid等）
  tab_replace: ''        # 用什么字符来代替tab(`\t`)字符。
  wrap: true
  hljs: false
prismjs:                 
  enable: false
  preprocess: true
  line_number: true
  tab_replace: ''

# Home page setting
# path: Root path for your blogs index page. (default = '')
# per_page: Posts displayed per page. (0 = disable pagination)
# order_by: Posts order. (Order by date descending by default)
index_generator:
  path: ''           # 主页所在路径，默认为''
  per_page: 10       # 主页的索引页包含文章数量，如未定义，则采用根目录下的`per_page`值
  order_by: -date    # 文章（Post类型）排序属性，`-`为降序  

# Category & Tag  # 分类及标签
default_category: uncategorized   # 对文档的默认分类
category_map:                     # 分类别名
tag_map:						  # 标签别名

# Metadata elements
## https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meta
meta_generator: true

# Date / Time format
## Hexo uses Moment.js to parse and display date
## You can customize the date format as defined in
## http://momentjs.com/docs/#/displaying/format/
date_format: YYYY-MM-DD      # 日期格式
time_format: HH:mm:ss        # 时间格式
## updated_option supports 'mtime', 'date', 'empty'
updated_option: 'mtime'

# Pagination
## Set per_page to 0 to disable pagination
per_page: 10            # 每页显示的文章量 (0 = 关闭分页功能)
pagination_dir: page    # 分页目录

# 处理或不处理目录或文件
# Include / Exclude file(s)
## include:/exclude: options only apply to the 'source/' folder
include:
exclude:
ignore:


# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: next   # 主题配置，参考上一篇博客

# Deployment
## Docs: https://hexo.io/docs/one-command-deployment
deploy:          # 部署时的配置，参考上一篇博客
  type: ''
```
