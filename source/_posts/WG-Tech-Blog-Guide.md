---
uuid: 5c5da3c0-168a-11ea-8fce-adc538f4fca4
title: WG Tech Blog Guide
urlname: WG Tech Blog Guide
date: 2019-12-04 19:36:49
tags:
categories:
coauthor: liupeixin
---

# WisdomGarden Technology Blog

[![Build Status](https://github.com/wisdom-garden/wisdom-garden.github.io/workflows/build/badge.svg)](https://github.com/wisdom-garden/wisdom-garden.github.io/workflows/build/badge.svg)


## Prerequisites
* node
* git
* github
* nvm (optional)
* git config --global user.name "your name will be show blog"
* join team: [Wisdom Garden](https://github.com/wisdom-garden)


## Setup
- `git clone git@github.com:wisdom-garden/wisdom-garden.github.io.git wg-tech-blog`
- `cd wg-tech-blog`
- **`git checkout hexo-source`** Important!!! **Never checkout master**
- `nvm use` (optional)
- `yarn global add hexo-cli` or `npm install -g hexo-cli`
- `yarn install` or `npm install`


## Write
- `Usage: hexo new [layout] <title>`
- `hexo new "title"`
- use any tool edit `~/xxxxxx/wg-tech-blog/source/_posts/title.md`

## Preview
- `hexo serve`

## Publish
- `git commit`
- `git push`

## Update Article
- edit `~/xxxxxx/wg-tech-blog/source/_posts/title.md`
- update ./source/_posts/\[title-folder\] assets
- `git commit`
- `git push`

## Delete Article
- find article in ./source/_posts/
- delete article name .md and folder
- `git commit`
- `git push`

## picture host
- recommend qiniu or sina
- manually upload get assets url use in article
- TODO: picture host tools will come in the future

## IDE Recommend
- VS Code
- Sublime Text
- Typora

### More Information
- `hexo new --help`
- `hexo --help`
- [Markdown Cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)

### Site
- Github: [Github](https://wisdom-garden.github.io/)
- Coding: [Coding](http://peixinliu.coding.me/wg-tech-blog/)


### e.g.
- `hexo new "你好" -s hello`

url:  /2019/12/04/hello/

- `hexo new "中文"`

url: /2019/12/04/%25E4%25B8%25AD%25E6%2596%2587/
