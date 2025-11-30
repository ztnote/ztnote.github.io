---
# 基本内容
title: Hugo # 文章标题
description: A new Start. # 文章描述
slug: 001-hugo # 文章slug
date: 2025-11-30 14:30:00+0800 # 发布时间 创建时间
lastmod: 2025-12-30 14:30:00+0800 # 修改时间 会在文章末尾显示
image: cover.png # 文章封面
# 分类与标签
categories:
    - daily
tags:
    - site
    - hugo
# 其他
draft: true
weight: 1  
# 全局配置覆盖
math: true
readingTime: true
toc: true
comment: true    
---

## 起因
这次搭建这个站点以记录为主要目的，主打一个不折腾。  
~~ZTNOTE:折腾笔记~~  


## 搭建
网站主要参考了[hugo-theme-stack-starter](https://github.com/CaiJimmy/hugo-theme-stack-starter)

### 搭建步骤总结

### 本地查看
在上述方式下，本地构建需要有`git`,`hugo`,`go`等。  

```bash
hugo server -D
```

## 使用
[document](https://stack.jimmycai.com/)

### markdown

`stack`使用[page-bundle](https://gohugo.io/content-management/page-bundles/)来组织页面，这个有待研究。  

插入图片推荐使用bundle内的图片。
```markdown
![image](image.png)
```
### Frontmatter
以当前post为例
```mamrkdown

```

### shortcodes

#### quote
`stack`包含了几种video类型的shortcodes，但是感觉不大用得上。这个quote还不错:

{{< quote author="A famous person" source="The book they wrote" url="https://en.wikipedia.org/wiki/Book">}}
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
{{< /quote >}}


## 文章
### frontmatter 
不要在文章中使用`#`的一级标题，这个在右侧的`toc`中不会显示，从二级标题开始使用。
这个可以在`config markup`中配置`startlevel`来修改。

### math



