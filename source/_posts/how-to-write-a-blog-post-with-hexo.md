---
uuid: 3e37df10-1cec-11ea-a768-17a87e931740
title: 如何用Hexo写一篇博客
s: how-to-write-a-blog-post-with-hexo
date: 2019-12-12 01:01:36
tags:
  - hexo
  - guide
categories:
  - Technology
coauthor: liupeixin
---

关于如何使用博客系统 [WG Tech Blog Guide](https://blog.tronclass.com.cn/2019/12/04/wg-tech-blog-guide/) 这篇文章已经简述了流程，有 Git 使用基础，了解 Markdown 语法的人可无障碍使用。

鉴于有些同事不了解 Git，也不太熟悉 Markdown，再撰一文，更详细的阐述步骤。

hexo 的使用，都是基于命令行的。在做好 [Setup](https://blog.tronclass.com.cn/2019/12/04/wg-tech-blog-guide/#setup) 之后，就可以执行 hexo 命令了，流程如下：
<!-- more -->
![hexo-flow](https://ohukd8pbq.qnssl.com/wg-tech-blog/assets/hexo-flow.jpg)



比如我们要写一篇文章，标题叫 《如何用Hexo写一篇博客》，在命令行输入
`$ hexo new "如何用Hexo写一篇博客"`
然后命令行会显示:
`$ INFO  Created: ~/Work/wg/wg-tech-blog/source/_posts/如何用Hexo写一篇博客.md`

表示你创建了一个 名字为 `如何用Hexo写一篇博客.md` 的Markdown文件。然后用某种文本编辑器打开该文件，我用的Typora。Sublime Text, VS Code 都可以，只要能方便书写 Markdown 都行。

- Typora: `$ typora source/_posts/如何用Hexo写一篇博客.md`
- Sublime Text: `$ subl source/_posts/如何用Hexo写一篇博客.md`
- VS Code: `$ code source/_posts/如何用Hexo写一篇博客.md`

打开之后，看到的是下面的内容：

```markdown
---
uuid: ec658570-1ceb-11ea-a8c2-3b478b40e00e
title: 如何用Hexo写一篇博客
s: 如何用Hexo写一篇博客
date: 2019-12-12 22:30:19
tags:
categories:
coauthor: liupeixin
---


```
两个`--`之间的内容叫做 Front-matter ，用来记录一些非文章内容信息，可视为文章属性信息，每个属性都可自行编辑。

- uuid: 每篇文章独一无二的ID，自动生成。
- title: 文章标题，会显示在页面的标题部分
- s: slug，可理解为 URL 后缀。
- date: 文章发布时间
- tags: 文章标签，可以多个
- categories: 文章分类，可以多个
- coauthor: 文章作者，可以多个

然后我们运行命令预览下博文。 `$ hexo serve` 会在本地 4000(默认) 端口启动 HTTP 服务。访问 [http://localhost:4000](http://localhost:4000) ，可以看到 《如何用Hexo写一篇博客 这篇》这篇文章已经出现，点击打开。地址是: `http://localhost:4000/2019/12/12/%E5%A6%82%E4%BD%95%E7%94%A8Hexo%E5%86%99%E4%B8%80%E7%AF%87%E5%8D%9A%E5%AE%A2/`



或许有人已经嗅到了一些 **“坏味道”**：

1. 生成的文件名是中文。虽然现在操作系统早已解决了文件名中文编码的问题，但看着还是不太爽，特别是出现在了git版本控制的代码库里面。(当然出现中文是允许的)

2. 中文、空格等特殊字符出现在 URL 里会被转义，比如
	`http://localhost:4000/2019/12/12/如何用Hexo写一篇博客/` 会被转义成 `http://localhost:4000/2019/12/12/%E5%A6%82%E4%BD%95%E7%94%A8Hexo%E5%86%99%E4%B8%80%E7%AF%87%E5%8D%9A%E5%AE%A2/` 这样很不利于文章地址的识别、传播。



所以我们在标题含有非英文、空格等的时候，使用 -s 参数 指定slug。

`$ hexo new "如何用Hexo写一篇博客" -s "how-to-write-a-blog-post-with-hexo"`

输出

`$ INFO  Created: ~/Work/wg/wg-tech-blog/source/_posts/how-to-write-a-blog-post-with-hexo.md`

文章 URL 是 `http://localhost:4000/2019/12/12/how-to-write-a-blog-post-with-hexo/`



> 建议你总是使用 slug 参数，给文章一个可读的标题，以及一个可读的 URL。
> `$ hexo new "可读的标题" -s "readable-slug"`



新生成博文内容

```
---
uuid: 3e37df10-1cec-11ea-a768-17a87e931740
title: 如何用Hexo写一篇博客
s: how-to-write-a-blog-post-with-hexo
date: 2019-12-13 01:01:36
tags:
categories:
coauthor: liupeixin
---

```

然后你就可以在 第二个 `--` 之后写正式的博客内容了，使用 Markdown 语法即可。

写好之后，比如这篇文章最终的文件内容是:
```
---
uuid: 3e37df10-1cec-11ea-a768-17a87e931740
title: 如何用Hexo写一篇博客
s: how-to-write-a-blog-post-with-hexo
date: 2019-12-13 01:01:36
tags:
  - hexo
  - guid
categories:
  - Technology
coauthor: liupeixin
---

bala bala bala
```


之后就是一顿git操作了
```
$ git add .
$ git commit -m 'add post how to write a blog post with hexo'
$ git pull origin hexo-source -r
$ git push
```

当然，请遵循日常的git操作，比如 先 pull, rebase，除非文章名完全重名，大概率不会有冲突合并。

过了1分钟左右，你就能看到你的文章安静的躺在了 我们的技术博客里: [https://blog.tronclass.com.cn/](https://blog.tronclass.com.cn/)



几点说明:

- 每次 pull 仅pull当前分支。 `git pull origin hexo-source -r` 否则会把master的也fetch下来。
- Front-matter 默认使用 **YAML** 语法，数组(多个参数，比如标签，分类，作者)，可以这样写。
  ```
  tags:
    - hexo
    - guid
  ```
  或者
  ```
  tags: [hexo, guid, "some words"]
  ```
  只有一个可以直接写 `tags: hexo`

- 含有空格的值用 **双引号** 扩起来。如：`tags: [hexo, guid, "some words"]`

- hexo 每次 build 会生成所有网页， `push --force` 到 `master` 分支。

- slug，建议用中画线做连字符。

- 博文中使用图片，请使用图床(比如七牛)，不要把图片提交到代码库里。后面打算做一个 插件，快速给当前文章上传图片到七牛，获得 image URL 并插入博客。

- author 默认使用的是当前 git 的 username，请正确配置，当然你也可以自己在md文件里修改。 后面打算做个 author mapping 的插件，可以显示 author 的 display name，以及根据 author 统计文章个数。

- 首页列表默认会显示博文全文，可以在文章内容合适的部分，写一行 `<!-- more -->` 则这行之前视为摘要，显示在首页列表，并显示 Read More 按钮。也有自动摘要的插件，不尽人意，还是手动控制的好。

- `hexo help` 和 `hexo new --help` 可以看看



附赠链接：

[Hexo](https://hexo.io/)
[git - 入门指南](https://zhuanlan.zhihu.com/p/21193604)
[Pro Git](https://git-scm.com/book/zh/v2)
[我是个清新不做作的关于Markdown与Typora的教程](https://www.yuque.com/nju_nova/tips/ghaamc)
[Markdown Cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)
