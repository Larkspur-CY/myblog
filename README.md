# MyBlog

这是一个使用 Hugo 构建的博客项目。

## 项目结构

- `archetypes/` - 内容模板
- `assets/` - 静态资源文件
- `config/` - 配置文件
- `content/` - 博客内容
- `data/` - 数据文件
- `layouts/` - 页面布局
- `static/` - 静态文件
- `themes/` - 主题文件

## 环境要求

- Go
- Hugo

## 安装指南

### 安装 Go

1. 访问 [Go 官方网站](https://golang.org/dl/)
2. 下载适用于您操作系统的 Go 安装包
3. 运行安装程序并按照提示完成安装
4. 验证安装：
   ```bash
   go version
   ```

### 安装 Hugo

1. 访问 [Hugo 发布页面](https://github.com/gohugoio/hugo/releases)
2. 下载适用于您操作系统的 Hugo 版本
3. 解压下载的文件并将 Hugo 可执行文件添加到系统路径
4. 验证安装：
   ```bash
   hugo version
   ```

## 使用说明

1. 克隆项目到本地
2. 进入项目目录
3. 初始化 Hugo 模块（根据 Congo 主题文档，这是最方便的安装方式）：
   ```bash
   hugo mod init myblog
   ```
4. 获取 Congo 主题：
   ```bash
   hugo mod get github.com/jpanther/congo/v2
   ```
5. 启动 Hugo 服务器：
   ```bash
   hugo server
   ```
6. 在浏览器中访问 `http://localhost:1313` 查看博客

## 博客功能

本博客包含以下常见功能：

- 首页文章列表
- 分类和标签系统
- 搜索功能
- 代码复制
- 文章目录
- 多语言支持
- 响应式设计
- 深色模式

## 部署

运行以下命令生成静态文件：

```bash
hugo
```

生成的文件将位于 `public/` 目录中，可以部署到任何静态文件服务器上。