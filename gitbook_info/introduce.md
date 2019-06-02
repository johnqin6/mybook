# gitbook是什么？
gitbook是一个使用git和markdown来构建书籍的工具。它的功能很强大，可以把你的书本生成各种格式，如：PDF,ePub,mobi等,甚至成为一个网站。 

# gitBook官网(gitbook.com)
GitBook.com是一个使用工具链来创建和托管书籍的在线平台[www.GitBook.com](http://www.GitBook.com)

# 文档
[官方文档](https://github.com/GitbookIO/gitbook)
开发文档 (API & 插件) 地址 [developer.gitbook.com](http://www.developer.gitbook.com)。  
企业版本的安装向导和手册 [hep.enterprise.gitbook.com](http://www.hep.enterprise.gitbook.com)

# gitbook的格式
格式：注重简单和易读性  
gitbook约定的一些文件的作用：
* README: 书本的介绍 
* SUMMARY: 章节结构 <!--summary-->
* LANGS: 多语言书籍
* GLOSSARY: 术语描述的清单
一本书至少需要一个 `README` 和 `SUMMARY`文件才能构成

# gitbook的下载
gitbook的使用需要node.js环境,再用npm下载
> $ npm install gitbook-cli -g 

安装后可用下边命令查看版本   

> $ gitbook -V

# gitbook 编辑器
可从gitbook官网下载gitbook编辑器 [传送门](https://legacy.gitbook.com/editor)

# gitbook 初始化一本书
> $ gitbook init   

可以看到他会创建 README.md 和 SUMMARY.md 这两个文件，README.md 应该不陌生，就是说明文档，而 SUMMARY.md 其实就是书的章节目录

# 生成可在浏览器查看的书籍
> $ gitbook serve  
 
命令运行后可在浏览器地址栏输入localhost:4000 预览书籍。  
也可以只生成网页不开启服务器
> $ gitbook build

#gitbook配置文件 book.json
```json
{
    "title": "johnqin的一本书",
    "author": "johnqin",
    "description": "select * from learn",
    "language": "zh-hans",
    "gitbook": "3.2.3",
    "styles": {
        
    },
    "structure": {
        "readme": "README.md"
    },
    "links": {
        "sidebar": {
            "我的博客": ""
        }
    },
    "plugins": [
        "-sharing",
        "splitter",
        "expandable-chapters-small",
        "anchors",

        "github",
        "github-buttons",
        "donate",
        "sharing-plus",
        "anchor-navigation-ex",
        "favicon"
    ],
    "pluginsConfig": {
        "github": {
            "url": "https://github.com/johnqin6"
        },
        "github-buttons": {
            "buttons": [{
                "user": "Blankj",
                "repo": "glory",
                "type": "star",
                "size": "small",
                "count": true
                }
            ]
        },
        "donate": {
            "alipay": "./source/images/donate.png",
            "title": "",
            "button": "赞赏",
            "alipayText": " "
        },
        "sharing": {
            "douban": false,
            "facebook": false,
            "google": false,
            "hatenaBookmark": false,
            "instapaper": false,
            "line": false,
            "linkedin": false,
            "messenger": false,
            "pocket": false,
            "qq": false,
            "qzone": false,
            "stumbleupon": false,
            "twitter": false,
            "viber": false,
            "vk": false,
            "weibo": false,
            "whatsapp": false,
            "all": [
                "google", "facebook", "weibo", "twitter",
                "qq", "qzone", "linkedin", "pocket"
            ]
        },
        "anchor-navigation-ex": {
            "showLevel": false
        },
        "favicon":{
            "shortcut": "./source/images/favicon.jpg",
            "bookmark": "./source/images/favicon.jpg",
            "appleTouch": "./source/images/apple-touch-icon.jpg",
            "appleTouchMore": {
                "120x120": "./source/images/apple-touch-icon.jpg",
                "180x180": "./source/images/apple-touch-icon.jpg"
            }
        }
    }
}

```
# 安装插件
在plugins中进行配置然后输入命令安装
> gitbook install ./



参考文章 [GitBook 使用教程](https://www.jianshu.com/p/421cc442f06c)

