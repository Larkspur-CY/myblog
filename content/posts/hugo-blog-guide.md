---
title: "Hugo搭建博客入坑"
date: 2025-08-19T10:00:00+09:00
draft: false
categories:
  - "随笔"
  - "技术"
tags:
  - "Hugo"
  - "博客"
  - "Congo"
---

## 前言

Hugo 是一个强大的静态网站生成器，使用 Go 语言编写，它以速度快、灵活性强、易于扩展而著称，非常适合用来搭建个人博客。相较于传统的动态博客系统（如 WordPress、halo、Typecho），Hugo 无需数据库，生成静态页面即可上线，加载迅速、维护简单。

可是 Hugo 的上手过程对初学者来说往往有些门槛：安装环境、配置主题、内容组织、评论系统、自动化部署……环环相扣，若缺乏系统化指引，很容易在查看教程文档的时候如堕烟海、罔知所措！

## 参考资料
[焕昭君的博客](https://blog.huan666.de/posts/hugo-blog-setup-guide/)
[官方文档](https://gohugo.io/documentation/) [开始教程](https://gohugo.io/getting-started/) [快速开始](https://gohugo.io/getting-started/quick-start/)

## 前置条件

### 环境

- git：用于版本管理和后续上传GitHub。git -v：检测安装

- Go：使用 Hugo 模块功能所必须。go version ：检测安装

- Hugo：[官方安装文档](https://gohugo.io/getting-started/installing/)对不同系统提供了不同的安装方法可以看一下；其中最简单是直接下载 GitHub 提供的最新预编译二进制包，解压文件夹中有可执行文件，移动至目标目录并把该目录添加到 PATH 环境变量。hugo version：检测安装

- AI：（强烈推荐），用于 vibe coding ，对于 Hugo 刚开始使用的新手小白很有用，不仅能解释 Hugo 相关的概念和设置，还能快速帮你设置和修改。推荐使用 Claude code / Gemini CLI （命令行形式）、或 augment / cursor （IDE插件形式）。以及搭配 Context7 MCP 工具查询 hugo 文档。

### 编辑器

- Typora：markdown 编辑器，特点：随用随开，简洁高效、专注与 markdown 写作

- Obsidian：markdown 编辑器，特点：批量高效处理 md文档，适合结构化、层次化的项目

- VSCode：项目文件管理&编辑器，宇宙第一编辑器的含金量不用多说了吧~

## 开始安装

首先是要选择一个主题，这里我使用 [Congo](https://github.com/jpanther/congo) 为例。其他主题可以看[官方主题列表](https://themes.gohugo.io/)和 GitHub （ 这里我也总结和罗列了一些 Hugo 主题，你可以挑选一下）。使用其他主题开始也是没问题的，安装看对于的主题文档，主要概念大差不差。

### 初始化

#### 安装 Hugo 并初始化博客

```bash
# 检查hugo 是否正常安装
hugo version
# 创建目录`myblog`作为一个新的hugo站点
hugo new site myblog
cd myblog
```

#### 初始化 Hugo 模块并添加主题

根据 [Congo 的安装文档](https://jpanther.github.io/congo/docs/installation/)，使用 GO 所支持的 Hugo 模块是最方便的安装方式

```bash
# 本地初始化模块
hugo mod init myblog
```

通过创建一个新文件 config/_default/module.toml 并添加以下内容，将主题添加到配置中：

```toml
[[imports]]
path = "github.com/jpanther/congo/v2"
```

使用 hugo server 启动hugo服务器，自动下载主题，并且打开 http://localhost:1313/ 就能看到博客网页了

### 主题配置文件

config/_default/ 这作为 hugo 主题配置的文件夹，所有的设置配置都在这里。

首先下载 Congo 主题配置示例文件作为参考 下载副本
其次删除根目录的配置文件hugo.toml或者config.toml，使用config/_default/hugo.toml配置网站基础配置。复制示例文件到config/_default/这个目录里(不要覆盖module.toml！)，最终如下：

```
config/_default/
├─ hugo.toml			# 站点基础配置
├─ languages.en.toml	# 语言特定配置
├─ markup.toml			# 标记语言设置
├─ menus.toml			# 菜单配置
├─ module.toml			# 模块导入配置
└─ params.toml			# 主题参数
```

接下来就可以开始入门

### 后续更新

通过 hugo 模块安装的主题想要更新很简单，直接在项目路径下执行以下命令：

```bash
hugo mod get -u
```

## 入门

使用 Hugo 搭建博客，我们需要了解研究的总体流程路线图：
基础配置»内容组织»Front Matter»评论系统»自动构建和部署»无头CMS(后台内容管理)»进阶内容

先来个树状图展示和了解一下 hugo 项目的目录结构：

```
myblog/				# 项目根目录
├── archetypes/		# 内容模板目录
├── assets/			# 资源管道目录（编译时处理）
├── config/			# 配置文件目录
├── content/		# 内容目录（网站内容）
├── data/			# 数据文件目录
├── i18n/			# 国际化文件目录
├── layouts/		# 布局模板目录
├── static/			# 静态文件目录
├── themes/			# 主题目录（为空代表使用模块导入）
├── public/			# 构建输出目录
├── resources/		# 资源缓存目录（构建时生成）
└── go.mod			# Go模块文件
```

我们一开始重点关注config/和content/这两个目录，前者是配置目录后者是内容目录

配置目录的结构大致如此：

```
config/_default/    	# 默认环境配置
├── hugo.toml     		# 站点基础配置
├── languages.*.toml 	# 语言特定配置
├── menus.*.toml    	# 菜单配置
├── params.toml     	# 主题参数
├── markup.toml     	# 标记语言设置
└── module.toml     	# 模块导入配置
```

## 基础配置

### 站点配置

config/_default/hugo.toml：站点配置文件，设置整个站点的基础配置

baseURL设置博客网站，/结尾，然后填入语言代码设置语言和默认语言 Congo 语言代码参考

```toml
baseURL = "https://你的域名/"

languageCode = "zh-Hans"
defaultContentLanguage = "zh-Hans"
defaultContentLanguageInSubdir = false
timeZone = "Asia/Shanghai"
```

完整可参考配置文件：

```toml
baseURL = "https://blog.huan666.de/"

languageCode = "zh-Hans"
defaultContentLanguage = "zh-Hans"
defaultContentLanguageInSubdir = false
timeZone = "Asia/Shanghai"

enableRobotsTXT = true
summaryLength = 0

[pagination]
pagerSize = 10

[frontmatter]
format = "yaml"
# 自动日期填充配置
# date: 文章发布时间 - 优先使用front matter中的date，否则使用文件修改时间
date = ["date", ":fileModTime"]
# lastmod: 最后修改时间 - 优先使用front matter设置，否则使用文件修改时间
lastmod = ["lastmod", ":fileModTime"]

[[cascade]]
isCJKLanguage = true
_target.lang = "zh-Hans"

[outputs]
home = ["HTML", "RSS", "JSON", "articles"]

[privacy]
[privacy.vimeo]
enableDNT = true
[privacy.x]
enableDNT = true
[privacy.youTube]
privacyEnhanced = true

[services]
[services.x]
disableInlineCSS = true
```

### 多语言配置

接下来就是配置对应语言设置

languages.*.toml：是对应语言的配置，如果站点有简体中文和英文就需要两个文件languages.zh-Hans.toml和languages.en.toml，即**languages.[语言代码].toml**的格式命名文件。

languages.zh-Hans.toml示例配置，Congo [更多配置选项参考](https://jpanther.github.io/congo/docs/configuration/)

```toml
languageCode = "zh-Hans"
languageName = "简体中文"
weight = 1
contentDir = "content"

title = "焕昭君的博客"

[params.author]
name = "焕昭君"
image = "img/author.webp"
headline = "知行合一，日拱一卒"
bio = "知行合一，日拱一卒"
links = [
    { email = "mailto:huan@huan666.de" },
    { github = "https://github.com/Huan-zhaojun" },
    { linuxdo = "https://linux.do/u/huan/summary" }
]
```

### 头像设置

image = "img/author.webp"：image设置作者的头像，路径使用assets/下的相对路径。在你的博客项目找到这个文件夹📁，把你的头像图片复制拖拽进去。

> 💡 提示
> 图片文件你最好压缩成 WebP 减少体积

### 菜单配置

menus.*.toml：菜单导航配置，*替换为对应的语言代码如menus.en.toml和menus.zh-Hans.toml。
两种菜单导航可以配置，main在顶栏，footer在页脚。

配置参数：

- name：菜单导航名
- weight：权重，决定排序
- pageRef：菜单路径，用于引用内容页面或分类法。

> ⚠️ 警告
> ⚠️引用的页面要实际存在才能生效！下文内容组织章节将会完整讲解到这一切~

- url：直接链接URL
- params：额外参数设置，可以添加icon图标、showName、target等

参考配置文件：

```toml
[[main]]
name = "文章"
pageRef = "posts"
weight = 10

[[main]]
name = "分类"
pageRef = "categories"
weight = 20

[[main]]
name = "标签"
pageRef = "tags"
weight = 30

[[main]]
name = "友链"
pageRef = "friends"
weight = 97

[[main]]
name = "关于"
pageRef = "about"
weight = 98

[[main]]
identifier = "search"
weight = 99
[main.params]
action = "search"

[[main]]
identifier = "locale"
weight = 100
[main.params]
action = "locale"

# -- Footer Menu --
# The footer menu is displayed at the bottom of the page, just before
# the copyright notice. Configure as per the main menu above.

[[footer]]
name = "随便逛逛"
pageRef = "random"
weight = 10
[footer.params]
icon = "lightbulb"

[[footer]]
name = "RSS订阅"
url = "/index.xml"
weight = 20
[footer.params]
icon = "rss"

[[footer]]
name = "站点地图"
url = "/sitemap.xml"
weight = 30
[footer.params]
icon = "globe"
```

### 主题参数配置

config/_default/params.toml：Hugo 主题参数配置。不同的主题提供了大量的配置选项可供设置，具体根据对应主题的文档查看不同配置选项的作用。本文以 Congo主题配置参数 为例，着重一些需要修改的选项。

| 名称 | 推荐值 | 说明 |
| --- | --- | --- |
| colorScheme | | 设置主题配色方案，参阅配色方案 |
| autoSwitchAppearance | true | 根据访客系统自动切换主题外观 |
| enableSearch | true | 站点搜索功能 |
| enableCodeCopy | true | 代码块复制 |
| header.layout | hybrid | 顶部栏&菜单的布局样式<br>设置成混合让移动设备宽度不足自动折叠顶栏 |
| footer.showAppearanceSwitcher | true | 网站页脚显示外观切换器 |
| homepage.layout | | 主页的布局模式，参阅主页布局 |
| homepage.showRecent | true | 是否在首页显示近期文章列表。 |
| homepage.recentLimit | | 显示近期文章的最大数量 |
| article.showDateUpdated | true | 是否显示文章的更新日期 |
| article.showAuthor | | 是否在文章页脚显示作者信息框 |
| article.showEdit | | 是否显示编辑文章内容的链接 |
| article.showTableOfContents | true | 是否在文章中显示目录<br>markup.toml修改tableOfContents-目录层级显示 |
| article.showTaxonomies | | 是否显示与本文相关的分类法（即分类和标签） |
| article.showWordCount | true | 是否显示文章字数统计 |
| article.showComments | true | 是否在文章页脚后包含评论部分 |
| list.showTaxonomies | | 是否在列表页显示与本文相关的分类标签 |

更多请参阅 [Congo主题配置参数](https://jpanther.github.io/congo/docs/configuration/#parameter-reference) 文档

## 内容组织

Hugo 把网站内容看作一个树状结构，就像文件系统一样。每个文件夹都可以成为一个"部分(Section)"，每个 Markdown 文件都可以成为一个"页面(Page)"。

### 基本结构和概念图解

```
content/
├── _index.md           	# 网站首页内容
├── about.md            	# 单独页面 (Page)
├── posts/              	# Section: 博客文章区
│   ├── _index.md       	# posts 列表页
│   ├── my-first-post.md
│   └── hugo-guide.md
├── projects/           	# Section: 项目展示区
│   ├── _index.md       	# projects 列表页
│   ├── project-1.md
│   └── awesome-app/    		# Page Bundle (页面捆绑)
│       ├── index.md    		# 页面内容
│       ├── screenshot.png  	# 页面资源
│       ├── cover.jpg        	# 封面图
│       ├── featured.png     	# 特色图
│       └── thumbnail.jpg    	# 缩略图
└── docs/              		# Section: 文档区
    ├── _index.md      		# docs 首页
    ├── getting-started.md
    └── advanced/      			# 子Section
        ├── _index.md  			# advanced 列表页
        └── performance.md
```

接下来将区分Page (单独页面)、Section (内容分区)、List Page (列表页)、Page Bundle (页面捆绑)这四大概念的不同，在正式写博客文章之前还是非常需要了解这些概念的。

### Page (单独页面)

最基本的内容单元，就是一个独立的 .md 文件成为一个独立页面。
例子：content/about.md，URL路径就是/about/。创建了这个文件了之后可以作为菜单导航，如上文配置一个菜单导航所说的，pageRef = "about"引用这个文档作为关于页面的菜单导航

### Section (内容分区)

content/ 目录下，一个文件夹就是一个 Section，用来组织相关的内容。

例子： content/posts/ 文件夹

- URL 前缀：/posts/
- 可包含多个不同的独立页面
- 可以有自己的列表页 (_index.md)

### List Page (列表页)

用 _index.md 文件定义，可以展示Section下所有内容列表。

例如：

网站根目录的 _index.md

```
content/
└── _index.md			# 控制网站首页
```

Section 根目录的 _index.md

```
content/
└── posts/
    └── _index.md		# 控制 /posts/ 列表页
```

子目录的 _index.md
子分区，形成多级内容结构

```
content/
└── docs/
    └── advanced/
        └── _index.md	# 控制 /docs/advanced/ 列表页
```

不好理解？https://blog.huan666.de/posts/- 这是我的博客的文章页面，你可以理解成一个List Page (列表页)，展示了posts这个Section下所有内容的列表

### Page Bundle (页面捆绑)

hugo的内容组织形式，可以将页面资源组织管理在一个文件夹中

#### Branch Bundle（分支捆绑）

使用 _index.md（注意下划线）来组织，可含其他页面作为子页面，常用于分类页、标签页、章节首页等。
具体效果我们可以看到一个页面展示了很多文章的列表。

```
content/
├── posts/              # Branch Bundle目录
│   ├── _index.md       # 关键：使用 _index.md
│   ├── post-1.md
│   ├── post-2.md
│   └── images/
│       └── hero.jpg
└── about/              # 另一个Branch Bundle
    ├── _index.md
    ├── team.jpg
    └── company.pdf
```

不好理解？https://blog.huan666.de/posts/ – 你也可以理解成一个Branch Bundle（分支捆绑），展示了posts下所有文档的列表。

#### Leaf Bundle（页捆绑）

使用 index.md（不带下划线）来组织，代表这个文件夹是一个完整的页面，最终呈现是单个页面。通常用于将博客文章和图片绑定在一起，封面图、特色图、缩略图等。

```
content/
└── posts/
    └── my-awesome-post/     # 文件夹
        ├── index.md         # 注意：是 index.md，不是 _index.md！
        ├── featured.png     # 特色图
        ├── cover.jpg        # 封面图
        ├── thumbnail.jpg    # 缩略图
        └── images/			 # 图片目录
            ├── gallery-1.jpg
            └── gallery-2.jpg
```

在当前 Congo 主题使用这种方式定义一个文章的封面图、缩略图、特色图的。文章文件夹📁下，文档主体命名为index.md文件，图片命名为thumb或thumbnail则作为文章缩略图在文章列表展示，而命名为cover则在该文章页面的内容顶部展示，而featured其存在时同时替代thumb和cover的作用。

| 特性 | Branch Bundle | Leaf Bundle |
| --- | --- | --- |
| 索引文件 | _index.md | index.md |
| 子页面 | ✅ 可以有 | ❌ 不能有 |
| 资源文件 | ✅ 支持 | ✅ 支持 |
| 典型用途 | 分类页、章节页 | 单篇文章 |

### 添加内容

这行命令创建文章内容

```bash
hugo new content/_index.md			#编辑博客首页内容
hugo new content/posts/_index.md	#编辑文章列表内容
hugo new content content/posts/my-first-post.md   #编辑第一篇文章内容
```

运行hugo server，并且点击访问http://localhost:1313/发现博客首页有内容了，/posts/路径显示了包括第一篇文章在内的文章列表，/posts/my-first-post.md路径对应了刚刚创建的第一篇文章。这体现了前面说的 hugo 内容组织结构如同树状，文件夹就是路径。

## Front Matter

回过头来，刚刚创建文档内容的时候，发现文档头部位置多出了一个这样的玩意：

```toml
+++
title = 'My First Post'
date = 2024-01-14T07:07:07+01:00
draft = true
+++
```

这其实就是 Front Matter ── 是放在文章开头的元数据区块，由特定分隔符包围（如 --- 或 +++），支持 YAML / TOML / JSON 等格式。用于定义和存储这篇文章的标题、日期、标签等属性。在 Hugo 中，Front Matter 决定了页面如何被构建和显示。

接下来我将以 YAML 格式为 Front Matter 示例。

### 属性字段

archetypes/default.md：这是 Hugo 中的原型模板文件，用于定义新建内容时的默认模板。当你使用 hugo new 命令创建新内容时，Hugo 会使用这个模板作为基础。
下面我提供一个供参考的 Front Matter 模板示例，我认为值得关注的 Front Matter 属性设置：

```yaml
---
title: "{{ replace .File.ContentBaseName "-" " " | title }}"
#slug: ""
#description: "Meta标签 页面描述"
#summary: "文章预览 内容摘要"
date: {{ .Date }}
#lastmod: {{ .Date }}
draft: false
categories: []
tags: []
---
```

| 字段 | 说明 |
| --- | --- |
| title | 标题，文章的显示标题 |
| slug | URL 别名，自定义文章的 URL 路径，如不设置则使用文件名 |
| description | 页面描述，用于 HTML meta 标签,影响 SEO 搜索结果显示 |
| summary | 内容摘要，可作为文章列表的预览内容，如不设置 Hugo 会自动截取文章开头部分 |
| date | 创建时间，文章发布日期，可自动生成，影响文章排序 |
| lastmod | 修改时间，文章最后修改时间，可通过配置自动根据文件修改时间更新 |
| draft | 草稿状态，设为 true 时文章不会在生产环境中显示，默认为 false |
| isCJKLanguage | 中日韩语言标识，设为true可优化中文等 CJK 语言的摘要截取和字数统计 |
| categories&tags | 分类和标签，Hugo 默认的 Taxonomies(分类法)，用于内容组织和导航 |

更多 Front Matter 字段请参阅： [hugo](https://gohugo.io/content-management/front-matter/) 和 [Congo主题](https://jpanther.github.io/congo/docs/guides/front-matter/) 的文档

额外的补充内容

**slug**：作用：自定义URL路径，避免使用文件名作为URL。
当文档名是中文时，强烈建议设置英文slug。
唯一性：根据permalink结构决定是否需要全局唯一

```toml
# 情况1：需要全局唯一slug，去掉其他嵌套目录路径
[permalinks]
posts = "/:slug/"
# 情况2：可以重复slug（有日期区分）
[permalinks]  
posts = "/:year/:month/:slug/"
# 情况3：可以重复slug（有分类、嵌套目录区分）
[permalinks]
posts = "/:section/:slug/"
```

**自动时间跟踪**
在hugo.toml中配置，实现对文档修改时间的自动跟踪：

```toml
[frontmatter]
format = "yaml"
# 自动日期填充配置
# date: 文章发布时间 - 优先使用front matter中的date，否则使用文件修改时间
date = ["date", ":fileModTime"]
# lastmod: 最后修改时间 - 优先使用front matter设置，否则使用文件修改时间
lastmod = ["lastmod", ":fileModTime"]
```

**自动中日韩语言标识 isCJKLanguage**
在hugo.toml配置使用**cascade**功能为特定语言自动添加配置，即实现中文文章自动添加isCJKLanguage: true的 Front Matter 字段

```toml
[[cascade]]
isCJKLanguage = true
_target.lang = "zh-Hans"
```

### 自动添加 Front Matter

看完上文之后我猜你跃跃欲试，准备把大量已有的笔记、文档等复制迁移到博客文件夹里，不过还没完~
大量的文档一开始是没有 Front Matter 的，当然肯定不能傻傻地手动一个个在文档开头添加。所以我专门写了一个脚本能够批量为文档添加 Front Matter，具体使用跳转到本文最后的 其他资源 章节查看。

> ❗ 重要
> 如果你不想丢失文件原始创建时间元信息，那最好在你的文档复制到博客📂之前自动添加 Front Matter，不然你得一个个手动添加创建时间了…

### 便捷修改

不过还是要对文档的元信息做具体的修改，比如概括描述、slug、分类、标签等。这里我推荐 Obsidian 这个前面最开始提到过的 markdown 编辑器，它对文档的 Front Matter 的编辑功能做到很不错，谁用谁知道！最让我夸赞的一点就是────编辑 tag 标签时候，它会提供目前所有文档用过的标签作为候选选择！！

### 批量管理文档

最后再推荐一个强力的 VSCode 插件────Front Matter CMS，GitHub VSCode 插件
基于 Front Matter 的文档内容管理系统，根据时间、分类标签等批量管理所有文档。

## 网站图标(Favicon)

在 Congo 主题把图标资源按照以下的方式放在 static/ 文件夹内即可配置博客网站图标。好在，favicon.io这个网站提供了一键生成网站图标的工具。

```
static/
├─ android-chrome-192x192.png
├─ android-chrome-512x512.png
├─ apple-touch-icon.png
├─ favicon-16x16.png
├─ favicon-32x32.png
├─ favicon.ico
└─ site.webmanifest
```

## 数据分析(Analytics)

### Google Analytics

访问官网(analytics.google.com/)：次使用点击"开始衡量"注册账号，若有账号就登录

继续下步骤把信息填完…

开始收集数据：选择平台"网站"，填入信息点击确认

复制衡量 ID，类似于一串G-XXXXXXXXX的字符串。

配置文件：config/_default/hugo.toml在这个文件里填入：

```toml
[services]
  [services.googleAnalytics]
    id = 'G-XXXXXXXXX'
```

然后就会自动添加上谷歌的脚本。这适用于Congo 主题，如果你是其他主题可能也支持该操作，具体得查看对应的文档。实在没有这么简单的操作还可以选择手动添加谷歌脚本到layouts/partials/analytics.html文件里

### Umami Analytics

[文档](https://umami.is/docs/)

## 评论系统

一个优秀的博客当然不能缺少评论系统集成。对于评论系统首要考虑的是时效通知和托管部署的这两大问题。

即时通知是最重要的，如果评论不能通知到对方，那整个评论系统将没有任何意义！一般常规操作是使用邮件来通知。大部分的评论系统要么需要自建邮局和设置SMTP，要么邮件代发服务；前者自建邮局搞起来挺麻烦的，但最棘手让人头疼问题──垃圾邮件──大部分自建邮局的归途，发的邮件全都被丢垃圾箱了；后者邮件代发服务，需要付费，免费额度每天只有一点，但对于个人博客的使用场景完全够用了。
托管与部署：评论系统可以选择自托管，完全掌控全部包括评论数据，那就需要数据库、服务器了，部署起来是有点小麻烦，考虑的事情也比较多。
综上所述，Giscus ── 首选适合的评论系统，快速上手，简单部署。

### Giscus

一个由 GitHub Discussions 驱动的评论系统。让访客通过 GitHub 在你的博客留下评论。

优势：

- 快速上手，简单部署：直接使用 GitHub来托管评论数据，集成在博客只需要通过生成器的一段js脚本。
- 开源免费
- 自带登录认证

劣势：

- 依赖GitHub，必须登录GitHub账号，评论所在仓库必须公开
- 缺乏丰富的自定义功能
- 评论不能先审后发

[Giscus](https://giscus.app/zh-CN)

#### 从[官方配置生成器](https://giscus.app/zh-CN)开始使用吧！

1. 创建一个公开的 GitHub 仓库，可以直接选择博客项目仓库，亦可专门创建一个仓库如myblog-comment作为评论托管
2. 安装 Giscus app：安装这个GitHub APP，并为刚刚的仓库启用。
3. 启用 Discussions 功能：在仓库设置里启用。
4. 继续回到官方配置生成器：

仓库选择：填入仓库名如YourGitHubUsername/my-blog-comments，Giscus 会自动检查仓库是否有效以及 App 是否已安装。

页面 ↔️ discussion 映射关系：
这是最关键的一步，它决定了如何将你的博客文章与 GitHub Discussion 讨论帖对应起来。对于大多数 Hugo 博客，推荐使用以下两种稳定且不易出错的方案：
- pathname：使用文章的相对 URL 路径作为唯一标识。这是最推荐的选项。这与前面说过的hugo于文章的slug关联起来了
- URL：使用文章的完整 URL 作为唯一标识。

Discussion 分类 (Category)：选择一个讨论分类用于存放评论。推荐使用公告（announcements）类型的分类并删除其他分类，以确保新 discussion 只能由仓库维护者和 giscus 创建。或者新建一个专用分类如Comments然后设置权限也是一样的。

功能 (Features)：
- 启用主帖子上的反应（reaction）：建议勾选，允许访客对你的文章点赞或送上其他表情。
- 懒加载评论(Lazy Loading)：强烈建议勾选！这会使评论区只在用户滚动到页面底部时才加载，能显著提升你博客的初始加载速度。

主题 (Theme)：默认选择 用户偏好的色彩方案Preferred Color Scheme

生成脚本：完成以上所有配置后，页面底部会自动生成一段 &lt;script&gt; 代码。点击 "Copy" 按钮复制这段代码。

高级用法：查阅指南，还可以添加额外设置如giscus.json设置仅允许特定来源的请求等

对于使用 hugo 的 Congo 主题的评论功能：只需要创建一个layouts/_partials/comments.html文件，把刚刚复制的代码粘贴到以下示例文件的指定位置去。如果前面article.showComments = true已经启用了评论功能，那在文章底部就能看到评论显示了。

```html
{{/* giscus 评论系统组件 */}}
<div class="giscus-comments">
  粘贴的 <script> 块
</div>
```

或者使用我自定义的layouts/_partials/comments.html文件：

多语言自适应：评论界面跟随语言切换。文章不同语言版本映射到同一个评论区。
更好的深浅模式适配

```html
{{/* giscus 评论系统组件 - 增强版 */}}
{{/* 多语言自适应配置 */}}
{{- $giscusLang := "zh-CN" -}}
{{- if eq .Language.Lang "en" -}}
  {{- $giscusLang = "en" -}}
{{- end -}}

{{/* 路径标准化逻辑 - 统一多语言评论映射 */}}
{{- $commentTerm := .RelPermalink -}}
{{/* 统一去掉开头的斜杠 */}}
{{- $commentTerm = strings.Replace .RelPermalink "/" "" 1 -}}
{{- if eq .Language.Lang "en" -}}
  {{/* 对于英文页面，去掉 en/ 前缀来统一映射 */}}
  {{- $commentTerm = strings.Replace $commentTerm "en/" "" 1 -}}
{{- end -}}

<div class="giscus-comments">
  {{/* giscus评论组件 */}}
  <script src="https://giscus.app/client.js"
          data-repo="替换仓库名"
          data-repo-id="替换仓库ID"
          data-category="替换分类名"
          data-category-id="替换分类ID"
          data-mapping="specific"
          data-term="{{ $commentTerm }}"
          data-strict="1"
          data-reactions-enabled="1"
          data-emit-metadata="0"
          data-input-position="top"
          data-theme="preferred_color_scheme"
          data-lang="{{ $giscusLang }}"
          data-loading="lazy"
          crossorigin="anonymous"
          async>
  </script>
</div>
```