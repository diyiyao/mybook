# [GitBook](https://chrisniael.gitbooks.io/gitbook-documentation/content/)

###[简介]()

这是 [gitbook](https://github.com/GitbookIO/gitbook) 项目主页上对 gitbook 的定义。

gitbook 首先是一个软件，正如上面定义的那样，它使用 Git 和 Markdown 来编排书本，如果用户没有听过 Git 和 Markdown，那么 gitbook 可能不适合你！

本书也是使用 gitbook 生成，所以在看到这里的时候，你应该对 gitbook 的魔力有了初步印象！

###[安装]()

[gitbook](https://github.com/GitbookIO/gitbook) 的安装非常简单，详细指南可以参考 gitbook 文档。

这里的安装只需要一步就能完成！
````
$ npm install gitbook-cli -g
````
需要注意的是：用户首先需要安装 nodejs，以便能够使用 npm 来安装 gitbook。


###[基本使用]()

gitbook 的基本用法非常简单，基本上就只有两步：

>1. 使用`gitbook init`初始化书籍目录

>2. 使用`gitbook serve`编译书籍

可能会出现的问题：
````
➜  ebook git:(master) ✗ gitbook pdf .
/usr/local/lib/node_modules/gitbook-cli/node_modules/npm/node_modules/graceful-fs/polyfills.js:287
      if (cb) cb.apply(this, arguments)
                 ^

TypeError: cb.apply is not a function
    at /usr/local/lib/node_modules/gitbook-cli/node_modules/npm/node_modules/graceful-fs/polyfills.js:287:18
````
这是因为node版本过高导致。

##### 修复方式:

打开`/usr/local/lib/node_modules/gitbook-cli/node_modules/npm/node_modules/graceful-fs/polyfills.js`
下面是出问题的函数:
````
function statFix (orig) {
  if (!orig) return orig
  // Older versions of Node erroneously returned signed integers for
  // uid + gid.
  return function (target, cb) {
    return orig.call(fs, target, function (er, stats) {
      if (!stats) return cb.apply(this, arguments)
      if (stats.uid < 0) stats.uid += 0x100000000
      if (stats.gid < 0) stats.gid += 0x100000000
      if (cb) cb.apply(this, arguments)
    })
  }
}
````

这似乎修复了旧版本Node.js中的一些问题。

在同一文件的第62-64行中被使用:
````
fs.stat = statFix(fs.stat)
fs.fstat = statFix(fs.fstat)
fs.lstat = statFix(fs.lstat)
````
修改后
````
// fs.stat = statFix(fs.stat)
// fs.fstat = statFix(fs.fstat)
// fs.lstat = statFix(fs.lstat)
````

下面将结合一个非常简单的实例，来介绍 gitbook 的基本用法。

###[gitbook init]()

首先，创建如下目录结构：
````
$ tree book/
book/
├── README.md
└── SUMMARY.md

0 directories, 2 files
````
README.md 和 SUMMARY.md 是两个必须文件，README.md 是对书籍的简单介绍：
````
$ cat book/README.md
# README

This is a book powered by [GitBook](https://github.com/GitbookIO/gitbook).
````
SUMMARY.md 是书籍的目录结构。内容如下：
````
$ cat book/SUMMARY.md 
# SUMMARY

* [Chapter1](chapter1/README.md)
  * [Section1.1](chapter1/section1.1.md)
  * [Section1.2](chapter1/section1.2.md)
* [Chapter2](chapter2/README.md)
````
创建了这两个文件后，使用 gitbook init，它会为我们创建 SUMMARY.md 中的目录结构。
````
$ cd book
$ gitbook init
$ tree
.
├── README.md
├── SUMMARY.md
├── chapter1
│   ├── README.md
│   ├── section1.1.md
│   └── section1.2.md
└── chapter2
    └── README.md

2 directories, 6 files
````

###[gitbook serve]()

书籍目录结构创建完成以后，就可以使用 gitbook serve 来编译和预览书籍了：
````
$ gitbook serve
Press CTRL+C to quit ...

Live reload server started on port: 35729
Starting build ...
Successfully built!

Starting server ...
Serving book on http://localhost:4000
````
gitbook serve 命令实际上会首先调用 gitbook build 编译书籍，完成以后会打开一个 web 服务器，监听在本地的 4000 端口。

现在，可以用浏览器打开 [http://127.0.0.1:4000]() 查看书籍的效果，如下图：
![](jpg/gitbook-sample.png)

###[配置]()
所有的配置都以JSON格式存储在名为 book.json 的文件中。

你可以粘贴你的`book.json`去 [jsonlint.com](https://jsonlint.com/) 验证JSON语法。

gitbook在编译书籍的时候会读取书籍源码顶层目录中的 `book.js` 或者 `book.json`，这里以 `book.json` 为例，参考 `gitbook` 文档 可以知道，`book.json` 支持如下配置：
````
{
    "author": "Chengwei Yang <me@chengweiyang.cn>",
    "description": "This is a sample book created by gitbook",
    "extension": null,
    "generator": "site",
    "isbn": null,
    "links": {
        "sharing": {
            "all": null,
            "facebook": null,
            "google": null,
            "twitter": null,
            "weibo": null
        },
        "sidebar": {
            "Chengwei's Blog": "http://www.chengweiyang.cn"
        }
    },
    "output": null,
    "pdf": {
        "fontSize": 12,
        "footerTemplate": null,
        "headerTemplate": null,
        "margin": {
            "bottom": 36,
            "left": 62,
            "right": 62,
            "top": 36
        },
        "pageNumbers": false,
        "paperSize": "a4"
    },
    "plugins": [],
    "title": "Sample GitBook",
    "variables": {}
}
````

需要注意的是：GitBook.com 上的书籍标题经试验不能通过配置 book.json 的方式修改 title，需要在书籍的属性页面中的 'Settings' 中进行修改！

###[插件]()

插件是扩展 GitBook 功能（电子书和网站）最好的方式。现在插件可以做很多事：支持数学公式的显示，使用 Google Analytic 追踪访问，...

* 如何查找插件？

可以在 plugins.gitbook.com 上很轻松的查找插件。



