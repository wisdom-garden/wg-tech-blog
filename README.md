# Wisdom Garden Technology Blog

[![Build Status](https://github.com/wisdom-garden/wisdom-garden.github.io/workflows/build/badge.svg)](https://github.com/wisdom-garden/wisdom-garden.github.io/workflows/build/badge.svg)


## Prerequisites
* node
* git
* github
* nvm (optional)
* git config --global user.name "your name will be show blog"
* join team: [Wisdom Garden](https://github.com/wisdom-garden)


## <a id="user-content-setup" href="#setup">Setup</a>
- `git clone -b hexo-source --recursive git@github.com:wisdom-garden/wisdom-garden.github.io.git wg-tech-blog`
- `cd wg-tech-blog`
- **Never checkout master**
- `git config push.default current`
- `nvm use` (optional)
- `yarn global add hexo-cli` or `npm install -g hexo-cli`
- `yarn install` or `npm install`
<!-- more -->

## Write
- `Usage: hexo new [layout] <title>`
- `hexo new "title"`
- use any tool edit `~/xxxxxx/wg-tech-blog/source/_posts/title.md`

## Preview
- `hexo serve`

## Publish
- `git pull origin hexo-source -r` only pull current branch
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

## More Information
- `hexo new --help`
- `hexo --help`
- [Markdown Cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)

## Site
- Domain: [https://blog.tronclass.com.cn](https://blog.tronclass.com.cn)

  Overseas --> [Github](https://wisdom-garden.github.io/)
  Domestic --> [Coding](http://lwkd88.coding-pages.com/)


## e.g.
- `hexo new "你好" -s hello`

url:  /2019/12/04/hello/

- `hexo new "中文"`

url: /2019/12/04/%25E4%25B8%25AD%25E6%2596%2587/
