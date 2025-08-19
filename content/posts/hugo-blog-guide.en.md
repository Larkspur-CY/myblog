---
title: "Hugo Blog Setup Guide"
date: 2025-08-19T10:00:00+09:00
draft: false
categories:
  - "Essays"
  - "Technology"
tags:
  - "Hugo"
  - "Blog"
  - "Congo"
---

## Introduction

Hugo is a powerful static site generator written in Go. It's known for its speed, flexibility, and extensibility, making it an excellent choice for building personal blogs. Compared to traditional dynamic blog systems (like WordPress, halo, Typecho), Hugo doesn't require a database. It generates static pages that can be deployed quickly with simple maintenance.

However, the learning curve for beginners can be steep: setting up the environment, configuring themes, organizing content, integrating comment systems, and automating deployment are all interconnected steps. Without systematic guidance, it's easy to feel overwhelmed when consulting tutorial documents!

## References
[Huan Zhaojun's Blog](https://blog.huan666.de/posts/hugo-blog-setup-guide/)
[Official Documentation](https://gohugo.io/documentation/) [Getting Started](https://gohugo.io/getting-started/) [Quick Start](https://gohugo.io/getting-started/quick-start/)

## Prerequisites

### Environment

- git: Used for version control and subsequent GitHub uploads. git -v: Check installation

- Go: Required for using Hugo module functionality. go version: Check installation

- Hugo: [Official Installation Documentation](https://gohugo.io/getting-started/installing/) provides different installation methods for various systems. The simplest method is to download the latest precompiled binary package from GitHub. After extracting, move the executable file to your target directory and add that directory to your PATH environment variable. hugo version: Check installation

- AI: (Highly recommended) for vibe coding. For Hugo beginners, AI can not only explain Hugo-related concepts and settings but also quickly help you set up and modify configurations. Recommended tools include Claude code / Gemini CLI (command-line form), or augment / cursor (IDE plugin form). Also consider using the Context7 MCP tool to query Hugo documentation.

### Editors

- Typora: Markdown editor. Features: Instant startup, clean and efficient, focused on markdown writing

- Obsidian: Markdown editor. Features: Batch processing of md documents, suitable for structured, hierarchical projects

- VSCode: Project file management & editor. The "best editor in the universe" needs no introduction.

## Getting Started

First, you need to choose a theme. Here I'll use [Congo](https://github.com/jpanther/congo) as an example. Other themes can be found in the [official theme list](https://themes.gohugo.io/) and on GitHub (I've also summarized and listed some Hugo themes for you to choose from). Starting with other themes is also fine. Just follow the corresponding theme documentation as the main concepts are similar.

### Initialization

#### Install Hugo and Initialize Blog

```bash
# Check if Hugo is installed correctly
hugo version
# Create directory `myblog` as a new Hugo site
hugo new site myblog
cd myblog
```

#### Initialize Hugo Modules and Add Theme

According to [Congo's installation documentation](https://jpanther.github.io/congo/docs/installation/), using Hugo modules supported by Go is the most convenient installation method.

```bash
# Initialize module locally
hugo mod init myblog
```

Create a new file config/_default/module.toml and add the following content to add the theme to the configuration:

```toml
[[imports]]
path = "github.com/jpanther/congo/v2"
```

Start the Hugo server with hugo server, which will automatically download the theme. Then open http://localhost:1313/ to see your blog website.

### Theme Configuration Files

config/_default/ serves as the folder for Hugo theme configurations, where all settings are stored.

First, download the Congo theme configuration example file as a reference.
Then delete the root configuration file hugo.toml or config.toml, and use config/_default/hugo.toml to configure the basic site settings. Copy the example file to the config/_default/ directory (don't overwrite module.toml!). The final structure should look like:

```
config/_default/
├─ hugo.toml            # Site basic configuration
├─ languages.en.toml    # Language-specific configuration
├─ markup.toml          # Markup language settings
├─ menus.toml           # Menu configuration
├─ module.toml          # Module import configuration
└─ params.toml          # Theme parameters
```

Now you can start the basics.

### Updates

Updating a theme installed via Hugo modules is simple. Just execute the following command in your project path:

```bash
hugo mod get -u
```

## Basics

When building a blog with Hugo, we need to understand the overall process roadmap:
Basic Configuration » Content Organization » Front Matter » Comment Systems » Automated Building and Deployment » Headless CMS (Backend Content Management) » Advanced Content

Let's start with a tree diagram to understand the Hugo project directory structure:

```
myblog/                 # Project root directory
├── archetypes/         # Content template directory
├── assets/             # Asset pipeline directory (processed at build time)
├── config/             # Configuration file directory
├── content/            # Content directory (website content)
├── data/               # Data file directory
├── i18n/               # Internationalization file directory
├── layouts/            # Layout template directory
├── static/             # Static file directory
├── themes/             # Theme directory (empty means using module import)
├── public/             # Build output directory
├── resources/          # Resource cache directory (generated at build time)
└── go.mod              # Go module file
```

We initially focus on the config/ and content/ directories, the former for configuration and the latter for content.

The configuration directory structure looks like this:

```
config/_default/        # Default environment configuration
├── hugo.toml           # Site basic configuration
├── languages.*.toml    # Language-specific configuration
├── menus.*.toml        # Menu configuration
├── params.toml         # Theme parameters
├── markup.toml         # Markup language settings
└── module.toml         # Module import configuration
```

## Basic Configuration

### Site Configuration

config/_default/hugo.toml: Site configuration file, sets the basic configuration for the entire site.

Set the blog website baseURL ending with /, then fill in the language code to set the language and default language. Refer to Congo's language codes.

```toml
baseURL = "https://your-domain/"

languageCode = "en"
defaultContentLanguage = "en"
defaultContentLanguageInSubdir = false
timeZone = "America/New_York"
```

Complete reference configuration file:

```toml
baseURL = "https://blog.huan666.de/"

languageCode = "en"
defaultContentLanguage = "en"
defaultContentLanguageInSubdir = false
timeZone = "America/New_York"

enableRobotsTXT = true
summaryLength = 0

[pagination]
pagerSize = 10

[frontmatter]
format = "yaml"
# Automatic date filling configuration
# date: Article publication time - uses front matter date first, otherwise file modification time
date = ["date", ":fileModTime"]
# lastmod: Last modification time - uses front matter setting first, otherwise file modification time
lastmod = ["lastmod", ":fileModTime"]

[[cascade]]
isCJKLanguage = false
_target.lang = "en"

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

### Multilingual Configuration

Next, configure the corresponding language settings.

languages.*.toml: This is the configuration for the corresponding language. If your site has Simplified Chinese and English, you'll need two files: languages.zh-Hans.toml and languages.en.toml, following the naming format **languages.[language code].toml**.

Example configuration for languages.en.toml, with [more configuration options reference](https://jpanther.github.io/congo/docs/configuration/):

```toml
languageCode = "en"
languageName = "English"
weight = 1
contentDir = "content"

title = "Huan Zhaojun's Blog"

[params.author]
name = "Huan Zhaojun"
image = "img/author.webp"
headline = "Unity of knowledge and action, improving daily"
bio = "Unity of knowledge and action, improving daily"
links = [
    { email = "mailto:huan@huan666.de" },
    { github = "https://github.com/Huan-zhaojun" },
    { linuxdo = "https://linux.do/u/huan/summary" }
]
```

### Avatar Setup

image = "img/author.webp": Sets the author's avatar image using a relative path under assets/. Find this folder 📁 in your blog project and drag your avatar image into it.

> 💡 Tip
> It's best to compress image files to WebP format to reduce file size.

### Menu Configuration

menus.*.toml: Menu navigation configuration. Replace * with the corresponding language code such as menus.en.toml and menus.zh-Hans.toml.
Two types of menu navigation can be configured: main in the top bar and footer at the bottom.

Configuration parameters:

- name: Menu navigation name
- weight: Weight determines sorting
- pageRef: Menu path used to reference content pages or taxonomies.

> ⚠️ Warning
> ⚠️ Referenced pages must actually exist to take effect! The content organization section below will fully explain this.

- url: Direct URL link
- params: Additional parameter settings, can add icons, showName, target, etc.

Reference configuration file:

```toml
[[main]]
name = "Posts"
pageRef = "posts"
weight = 10

[[main]]
name = "Categories"
pageRef = "categories"
weight = 20

[[main]]
name = "Tags"
pageRef = "tags"
weight = 30

[[main]]
name = "Friends"
pageRef = "friends"
weight = 97

[[main]]
name = "About"
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
name = "Explore Randomly"
pageRef = "random"
weight = 10
[footer.params]
icon = "lightbulb"

[[footer]]
name = "RSS Feed"
url = "/index.xml"
weight = 20
[footer.params]
icon = "rss"

[[footer]]
name = "Sitemap"
url = "/sitemap.xml"
weight = 30
[footer.params]
icon = "globe"
```

### Theme Parameter Configuration

config/_default/params.toml: Hugo theme parameter configuration. Different themes provide numerous configuration options. Refer to the corresponding theme documentation for specific option functions. This article uses Congo theme parameter configuration as an example, focusing on some options that need modification.

| Name | Recommended Value | Description |
| --- | --- | --- |
| colorScheme | | Sets the theme color scheme, see color schemes |
| autoSwitchAppearance | true | Automatically switches theme appearance based on visitor's system |
| enableSearch | true | Site search functionality |
| enableCodeCopy | true | Code block copying |
| header.layout | hybrid | Top bar & menu layout style<br>Set to hybrid to automatically collapse the top bar on mobile devices with insufficient width |
| footer.showAppearanceSwitcher | true | Shows appearance switcher in website footer |
| homepage.layout | | Homepage layout mode, see homepage layouts |
| homepage.showRecent | true | Whether to show recent article list on homepage |
| homepage.recentLimit | | Maximum number of recent articles to display |
| article.showDateUpdated | true | Whether to show article update date |
| article.showAuthor | | Whether to show author information box in article footer |
| article.showEdit | | Whether to show link to edit article content |
| article.showTableOfContents | true | Whether to show table of contents in article<br>Modify tableOfContents in markup.toml to adjust TOC level display |
| article.showTaxonomies | | Whether to show related taxonomies (categories and tags) for this article |
| article.showWordCount | true | Whether to show article word count |
| article.showComments | true | Whether to include comment section after article footer |
| list.showTaxonomies | | Whether to show related category tags on list pages |

For more information, refer to the [Congo theme parameter documentation](https://jpanther.github.io/congo/docs/configuration/#parameter-reference).

## Content Organization

Hugo treats website content as a tree structure, just like a file system. Each folder can become a "Section," and each Markdown file can become a "Page."

### Basic Structure and Concept Diagram

```
content/
├── _index.md                   # Website homepage content
├── about.md                    # Single page (Page)
├── posts/                      # Section: Blog articles area
│   ├── _index.md               # posts list page
│   ├── my-first-post.md
│   └── hugo-guide.md
├── projects/                   # Section: Project showcase area
│   ├── _index.md               # projects list page
│   ├── project-1.md
│   └── awesome-app/            # Page Bundle (page bundling)
│       ├── index.md            # Page content
│       ├── screenshot.png      # Page resources
│       ├── cover.jpg           # Cover image
│       ├── featured.png        # Featured image
│       └── thumbnail.jpg       # Thumbnail image
└── docs/                       # Section: Documentation area
    ├── _index.md               # docs homepage
    ├── getting-started.md
    └── advanced/               # Subsection
        ├── _index.md           # advanced list page
        └── performance.md
```

Next, we'll distinguish between the four major concepts: Page (single page), Section (content section), List Page (list page), and Page Bundle (page bundling). It's very important to understand these concepts before formally writing blog articles.

### Page (Single Page)

The most basic content unit is a single .md file that becomes an independent page.
Example: content/about.md, with URL path /about/. After creating this file, it can be used as a menu navigation. As mentioned in the previous menu configuration section, pageRef = "about" references this document as the menu navigation for the about page.

### Section (Content Section)

Under the content/ directory, each folder is a Section used to organize related content.

Example: content/posts/ folder

- URL prefix: /posts/
- Can contain multiple different independent pages
- Can have its own list page (_index.md)

### List Page (List Page)

Defined with an _index.md file, it can display a list of all content under a Section.

For example:

Root _index.md of the website

```
content/
└── _index.md           # Controls website homepage
```

Root _index.md of Section

```
content/
└── posts/
    └── _index.md       # Controls /posts/ list page
```

_index.md of subdirectory
Subsection, forming a multi-level content structure

```
content/
└── docs/
    └── advanced/
        └── _index.md   # Controls /docs/advanced/ list page
```

Hard to understand? https://blog.huan666.de/posts/ - This is my blog's articles page, which you can understand as a List Page, displaying a list of all content under the posts Section.

### Page Bundle (Page Bundling)

Hugo's content organization form that can organize and manage page resources within a folder.

#### Branch Bundle

Organized using _index.md (note the underscore), can contain other pages as subpages, commonly used for category pages, tag pages, and chapter homepages.
The specific effect shows a page displaying a list of many articles.

```
content/
├── posts/              # Branch Bundle directory
│   ├── _index.md       # Key: Using _index.md
│   ├── post-1.md
│   ├── post-2.md
│   └── images/
│       └── hero.jpg
└── about/              # Another Branch Bundle
    ├── _index.md
    ├── team.jpg
    └── company.pdf
```

Hard to understand? https://blog.huan666.de/posts/ – You can also understand this as a Branch Bundle, displaying a list of all documents under posts.

#### Leaf Bundle

Organized using index.md (without underscore), represents a complete page in this folder, ultimately presenting as a single page. Commonly used to bind blog articles with images, cover images, featured images, and thumbnails.

```
content/
└── posts/
    └── my-awesome-post/     # Folder
        ├── index.md         # Note: index.md, not _index.md!
        ├── featured.png     # Featured image
        ├── cover.jpg        # Cover image
        ├── thumbnail.jpg    # Thumbnail image
        └── images/          # Image directory
            ├── gallery-1.jpg
            └── gallery-2.jpg
```

In the current Congo theme, this method defines an article's thumbnail, cover image, and featured image. Under the article folder 📁, the main document body is named index.md, images named thumb or thumbnail serve as article thumbnails displayed in the article list, while those named cover are displayed at the top of the article page content, and featured images replace both thumb and cover when present.

| Feature | Branch Bundle | Leaf Bundle |
| --- | --- | --- |
| Index file | _index.md | index.md |
| Subpages | ✅ Can have | ❌ Cannot have |
| Resource files | ✅ Supported | ✅ Supported |
| Typical use | Category pages, chapter pages | Single articles |

### Adding Content

This command creates article content

```bash
hugo new content/_index.md          # Edit blog homepage content
hugo new content/posts/_index.md    # Edit article list content
hugo new content content/posts/my-first-post.md   # Edit first article content
```

Run hugo server, then visit http://localhost:1313/ to see that the blog homepage has content. The /posts/ path displays a list of articles including the first article, and the /posts/my-first-post.md path corresponds to the first article just created. This demonstrates the tree-like content organization structure mentioned earlier, where folders are paths.

## Front Matter

Looking back, when creating document content earlier, you noticed something like this at the beginning of the document:

```toml
+++
title = 'My First Post'
date = 2024-01-14T07:07:07+01:00
draft = true
+++
```

This is actually Front Matter ── a metadata block placed at the beginning of an article, surrounded by specific delimiters (like --- or +++), supporting YAML / TOML / JSON formats. It's used to define and store attributes like the article's title, date, tags, etc. In Hugo, Front Matter determines how the page is built and displayed.

Next, I'll use YAML format as the Front Matter example.

### Attribute Fields

archetypes/default.md: This is Hugo's prototype template file used to define the default template for new content. When you use the hugo new command to create new content, Hugo uses this template as the basis.
Below I provide a reference Front Matter template example with attributes I think are worth paying attention to:

```yaml
---
title: "{{ replace .File.ContentBaseName "-" " " | title }}"
#slug: ""
#description: "Meta tag page description"
#summary: "Article preview content summary"
date: {{ .Date }}
#lastmod: {{ .Date }}
draft: false
categories: []
tags: []
---
```

| Field | Description |
| --- | --- |
| title | Title, the displayed title of the article |
| slug | URL alias, custom URL path for the article, uses filename if not set |
| description | Page description, used for HTML meta tags, affects SEO search result display |
| summary | Content summary, can serve as preview content in article lists, Hugo automatically extracts the beginning part if not set |
| date | Creation time, article publication date, can be automatically generated, affects article sorting |
| lastmod | Modification time, article last modification time, can be automatically updated based on file modification time through configuration |
| draft | Draft status, when set to true, the article won't be displayed in production environment, defaults to false |
| isCJKLanguage | CJK language identifier, when set to true, optimizes summary extraction and word count statistics for Chinese and other CJK languages |
| categories&tags | Categories and tags, Hugo's default Taxonomies, used for content organization and navigation |

For more Front Matter fields, refer to: [hugo](https://gohugo.io/content-management/front-matter/) and [Congo theme](https://jpanther.github.io/congo/docs/guides/front-matter/) documentation.

Additional supplementary content

**slug**: Function: Custom URL path, avoids using filename as URL.
When the document name is in Chinese, it's strongly recommended to set an English slug.
Uniqueness: Whether global uniqueness is required depends on permalink structure.

```toml
# Case 1: Global unique slug, removing other nested directory paths
[permalinks]
posts = "/:slug/"
# Case 2: Duplicate slug allowed (distinguished by date)
[permalinks]  
posts = "/:year/:month/:slug/"
# Case 3: Duplicate slug allowed (distinguished by category, nested directories)
[permalinks]
posts = "/:section/:slug/"
```

**Automatic Time Tracking**
Configure in hugo.toml to achieve automatic tracking of document modification time:

```toml
[frontmatter]
format = "yaml"
# Automatic date filling configuration
# date: Article publication time - uses front matter date first, otherwise file modification time
date = ["date", ":fileModTime"]
# lastmod: Last modification time - uses front matter setting first, otherwise file modification time
lastmod = ["lastmod", ":fileModTime"]
```

**Automatic CJK Language Identifier isCJKLanguage**
Configure in hugo.toml using the **cascade** function to automatically add configuration for specific languages, implementing automatic addition of isCJKLanguage: true Front Matter field for Chinese articles:

```toml
[[cascade]]
isCJKLanguage = true
_target.lang = "zh-Hans"
```

### Automatic Front Matter Addition

After reading the above, I guess you're eager to copy a large number of existing notes and documents to your blog folder, but there's more to it~
A large number of documents initially don't have Front Matter, and you certainly can't manually add it one by one at the beginning of each document. I've specifically written a script that can batch add Front Matter to documents. For specific usage, jump to the Other Resources section at the end of this article.

> ❗ Important
> If you don't want to lose the original creation time metadata of files, it's best to automatically add Front Matter before copying your documents to the blog folder 📂, otherwise you'll have to manually add the creation time one by one…

### Convenient Modification

However, you still need to make specific modifications to the document metadata, such as summary descriptions, slug, categories, tags, etc. Here I recommend Obsidian, the markdown editor mentioned at the beginning, which does an excellent job editing Front Matter in documents. Who uses it knows!
The most praiseworthy feature is ──── when editing tag labels, it provides all currently used tags in documents as candidate choices!!

### Batch Document Management

Finally, I recommend a powerful VSCode plugin ──── Front Matter CMS, GitHub VSCode plugin
A document content management system based on Front Matter, batch managing all documents by time, categories, tags, etc.

## Website Icon (Favicon)

In the Congo theme, icon resources can be configured as blog website icons by placing them in the static/ folder in the following way. Fortunately, favicon.io provides a one-click tool for generating website icons.

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

## Analytics

### Google Analytics

Visit the official website (analytics.google.com/): For first-time use, click "Start Measuring" to register an account, or log in if you already have one.

Continue with the following steps to fill in the information…

Start collecting data: Select platform "Website", fill in information and click confirm.

Copy the Measurement ID, similar to a string like G-XXXXXXXXX.

Configuration file: config/_default/hugo.toml Fill this in this file:

```toml
[services]
  [services.googleAnalytics]
    id = 'G-XXXXXXXXX'
```

Then the Google script will be automatically added. This applies to the Congo theme. If you're using another theme, it might also support this operation. You should check the corresponding documentation. If there's no such simple operation, you can manually add the Google script to the layouts/partials/analytics.html file.

### Umami Analytics

[Documentation](https://umami.is/docs/)

## Comment System

An excellent blog certainly can't be without comment system integration. For comment systems, the two most important considerations are timely notifications and hosting deployment.

Timely notifications are the most important. If comments can't notify the other party, the entire comment system becomes meaningless! The conventional operation is to use email for notifications. Most comment systems either require self-hosting a mail server and setting up SMTP, or email relay services; the former is quite troublesome to set up, but the most frustrating problem ── spam ── is the fate of most self-hosted mail servers, with sent emails mostly ending up in spam folders; the latter email relay services require payment, with free quotas only a little per day, but completely sufficient for personal blog usage scenarios.
Hosting and Deployment: Comment systems can choose self-hosting, completely controlling everything including comment data, which requires a database and server, making deployment a bit complicated with many considerations.
In summary, Giscus ── the preferred and suitable comment system, quick to get started, simple deployment.

### Giscus

A comment system powered by GitHub Discussions. Allows visitors to leave comments on your blog through GitHub.

Advantages:

- Quick to get started, simple deployment: Directly uses GitHub to host comment data, integration in blogs only requires a js script generated by the generator.
- Open source and free
- Built-in login authentication

Disadvantages:

- Depends on GitHub, must log in with GitHub account, the comment repository must be public
- Lacks rich customization features
- Comments can't be reviewed before posting

[Giscus](https://giscus.app/)

#### Let's start using it from the [official configuration generator](https://giscus.app/)!

1. Create a public GitHub repository, you can directly choose the blog project repository, or specially create a repository like myblog-comment for comment hosting
2. Install Giscus app: Install this GitHub APP and enable it for the newly created repository.
3. Enable Discussions feature: Enable it in the repository settings.
4. Continue back to the official configuration generator:

Repository selection: Fill in the repository name like YourGitHubUsername/my-blog-comments. Giscus will automatically check if the repository is valid and if the App is installed.

Page ↔️ discussion mapping relationship:
This is the most critical step, determining how to map your blog articles to GitHub Discussion threads. For most Hugo blogs, the following two stable and error-resistant solutions are recommended:
- pathname: Uses the relative URL path of the article as a unique identifier. This is the most recommended option. This is related to the slug of articles mentioned earlier.
- URL: Uses the complete URL of the article as a unique identifier.

Discussion Category: Select a discussion category for storing comments. It's recommended to use an announcements-type category and delete other categories to ensure new discussions can only be created by repository maintainers and giscus. Or create a dedicated category like Comments and set permissions accordingly.

Features:
- Enable reactions on main posts: Recommended to check, allows visitors to like your article or send other emojis.
- Lazy loading comments: Strongly recommended to check! This makes the comment section load only when users scroll to the bottom of the page, significantly improving your blog's initial loading speed.

Theme: By default, select User Preferred Color Scheme

Generate script: After completing all the above configurations, a <script> code block will be automatically generated at the bottom of the page. Click the "Copy" button to copy this code.

Advanced usage: Check the guide for additional settings like giscus.json to allow requests only from specific sources.

For using comment functionality with Hugo's Congo theme: Just create a layouts/_partials/comments.html file and paste the copied code into the specified position in the following example file. If article.showComments = true has already enabled the comment function, you'll see the comments displayed at the bottom of the article.

```html
{{/* giscus comment system component */}}
<div class="giscus-comments">
  Pasted <script> block
</div>
```

Or use my custom layouts/_partials/comments.html file:

Multilingual adaptability: Comment interface follows language switching. Different language versions of articles map to the same comment area.
Better dark/light mode adaptation

```html
{{/* giscus comment system component - enhanced version */}}
{{/* Multilingual adaptive configuration */}}
{{- $giscusLang := "en" -}}
```