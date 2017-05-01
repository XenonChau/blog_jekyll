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
  <figcaption>图片来源见水印: [3lian](http://www.3lian.com)</figcaption>
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
	• Runtime、Runloop 特性
	• Masonry、Autolayout、Constraints
	• CocoaPods
	• GCD
	• 事件响应流程、消息传递方式
	• 内存管理机制

#### Front-End 技术栈：
	• HTML
	• CSS
	• jQuery

#### Ruby 技术栈：
	• Rails
	• Passenger

#### Python 技术栈：
	• Flask
	• 分析页面抓取内容。

#### DBA 技术栈：
	• MySQL 只会用，不会并发优化。
	• Rails 中集成过PostgreSQL。
	• Flask 中集成过Redis。

#### Linux 技术栈：
	• 主力开发 MacBook、备用 Ubuntu14.04LTS。
	• 有自己的阿里云 ECS（自定义 CentOS 7.2）。
	• 熟悉 Git、SecureShell、Apache。
	• 惯用zsh，vi 略懂，emacs、nano不常用。

#### 其他：
	• 现用基于 Jekyll 的博客，Hexo、Wordpress 都用过。
	• 在朋友的 AWS EC2 上实操过 Docker。
	• 熟练使用 markdown 写文档（包括扩展语法）。
	• HHKB 坑中人。

### 联系方式：

如果你不幸走进了召唤法阵也没关系，简单的发个邮件给我，我就能救你出去了。

<a href="mailto:me@xenonchau.com?subject=Hi, Xenon. 有兴趣到我司工作吗？">me@xenonchau.com</a>

