---
layout: page
permalink: /about/index.html
title: 唤灵者的召唤法阵
tags: [简介, 博客, 简历, XenonChau]
imagefeature: about/summon.jpg
chart: true
---
<figure>
  <img src="{{ site.url }}/images/about/summon.jpg" alt="Xenon Chau">
  <figcaption>图片来源见水印: http://www.3lian.com</figcaption>
</figure>

{% assign total_words = 0 %}
{% assign total_readtime = 0 %}
{% assign featuredcount = 0 %}
{% assign statuscount = 0 %}

{% for post in site.posts %}
    {% assign post_words = post.content | strip_html | number_of_words %}
    {% assign readtime = post_words | append: '.0' | divided_by:200 %}
    {% assign total_words = total_words | plus: post_words %}
    {% assign total_readtime = total_readtime | plus: readtime %}
    {% if post.featured %}
    {% assign featuredcount = featuredcount | plus: 1 %}
    {% endif %}
{% endfor %}


Hi, 我是 **Xenon Chau**, 一个 iOS 开发者, 这儿是我的博客. 全站共有 {{ site.posts | size }} 篇文章, 分布在 {{ site.categories | size }} 个分类中, 总计加起来有 {{ total_words }} 个字符, 这大概需要花掉你大约 <span class="time">{{ total_readtime }}</span> 分钟来读完. {% if featuredcount != 0 %}其中有 <a href="{{ site.url }}/featured">{{ featuredcount }} 篇精选文章</a>, 你可以点击查看.{% endif %} 最近发表的文章是 {% for post in site.posts limit:1 %}{% if post.description %}<a href="{{ site.url }}{{ post.url }}" title="{{ post.description }}">"{{ post.title }}"</a>{% else %}<a href="{{ site.url }}{{ post.url }}" title="{{ post.description }}" title="Read more about {{ post.title }}">"{{ post.title }}"</a>{% endif %}{% endfor %}。 发表于： {% for post in site.posts limit:1 %}{% assign modifiedtime = post.modified | date: "%Y%m%d" %}{% assign posttime = post.date | date: "%Y%m%d" %}<time datetime="{{ post.date | date_to_xmlschema }}" class="post-time">{{ post.date | date: "%d %b %Y" }}</time>{% if post.modified %}{% if modifiedtime != posttime %} and last modified on <time datetime="{{ post.modified | date: "%Y-%m-%d" }}" itemprop="dateModified">{{ post.modified | date: "%d %b %Y" }}</time>{% endif %}{% endif %}{% endfor %}. 最后提交时间是： {{ site.time | date: "%A, %d %b %Y" }} at {{ site.time | date: "%I:%M %p" }} [UTC](http://en.wikipedia.org/wiki/Coordinated_Universal_Time "Temps Universel Coordonné").


### 我的技术发展发展方向：

*由于 markdown 的流程图我没画明白，所以就找了个简单的方式来画这个吧*  
[百度脑图](http://naotu.baidu.com/file/83c17528d7e435047aadafb90b28efcd)

> 下面是我所持有的技术：

#### Objective-C 技术栈：
* [Runtime](/Delve-Into-Objc-1-Runtime)
* Runloop
* GCD
* Block
* Autolayout
* CocoaPods
* 设计模式
* 事件响应流程、消息传递方式
* 内存管理机制

##### 需要加强的地方（毕竟是主业）
* <del>JSPatch</del>（已经被Apple干死了、前几天在面试的时候面试官提出了解决方案。）
* Hybrid（怎么说呢，好多公司为了敏捷开发选择了Hybrid模式，但我觉得还是Native好。）
* KVC / KVO 
* Core 系列（CGraphic, CAnimation, CTtext, CFoundation, CData就不建议使用了）
* OpenGL ES 编程
* 逆向与安全（class-dump / Reveal / iOS Class Guard）
* 调试与持续集成（Instruments / Test Case / Bugly / Jenkins）

#### Front-End 技术栈：
* HTML 5
* CSS 3
* JavaScript / ES 6 / jQuery / AngularJS

> Front-End 技术栈均为简单了解，暂时只能读懂代码但是还写不出来。自建网站中：[【Code 1 Bit】](http://code1bit.com)

#### Ruby 技术栈：
* rvm / gem
* Ruby on Rails
* Passenger

#### Python 技术栈：
* pip
* virtualenv / virtualenvwrapper
* Flask / Restful / Requests
* uWSGI / gUnicorn
* Scrapy 简单了解

#### DBA 技术栈：
* MySQL 熟悉语法
* PostgreSQL 学习中
* Redis 了解语法
* ElasticSearch

#### Linux 技术栈：
* CentOS 7.3 / Ubuntu 14.04 LTS
* Docker
* NginX
* ssh / git / ftp
* 了解网络协议 / RFC 2616-2617
* Vim 使用 / .vimrc 配置
* 阿里云 / AWS

#### 其他：
* Jekyll / Hexo / Wordpress 
* Markdown
* HHKBer

### 联系方式：

如果你不幸走进了召唤法阵也没关系，简单的发个邮件给我，我就能救你出去了。

<a href="mailto:me@xenonchau.com?subject=Hi, Xenon. 有兴趣到我司工作吗？">me@xenonchau.com</a>



