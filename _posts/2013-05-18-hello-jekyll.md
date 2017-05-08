---
layout: post
title: "Hello Jekyll!"
description: "博客, Jekyll, 模版, Notepad"
modified: 2013-05-18
category: "闲言碎语"
imagefeature: github_jekyll.jpg
---

> 记录一下我第一次使用 Jekyll 创建博客成功。

[Jekyll](https://jekyllrb.com) 是用 [Liquid](https://github.com/Shopify/liquid/wiki) 语言书写的一个开源框架。它的设置文件位于 `_config.yaml`，是用 [YAML](http://yaml.org/) 语言书写的。文章通常位于 `_posts` 目录下，可以用用 [Markdown](https://daringfireball.net/projects/markdown/) 格式写的。

## Liquid

Liquid 语言是一种标记语言, 将模板转化为相应的代码从而进行相关的”编译”. Liquid 使用 Ruby 编写而成.
Liquid 语言有两大类标记: 一种是结果型 (Output), 一种是语句型, 前者进行输出为 text , 后者主要进行语句相关操作,如 `if`, `for` 等.
Output 型使用 `{%raw%}{{ output }}{%endraw%}` 进行, 而 Tag 型则采用 `{%raw%}{% tag %}{%endraw%}`.

常用语句：

由于 Liquid 会直接解释相应语法，我是在 highlight 中嵌套了 raw 才实现的。

{% highlight python linenos %}
{% raw %}
{{ site.url }}/images/some_pic.jpeg # 定位 _config.yaml 中设置的 url， 即网站的网址。
{% include header.html %} # 定位 _include 目录下的 header.html。
{% endraw %}
{% endhighlight %}

{% highlight python linenos %}
{% raw %}
{% highlight ruby linenos %} # 语法高亮，语言名称，显示行号
    {% comment %}中间的代码格式会保留，会根据语言名称来选择高亮某些关键字。 {% endcomment %}
{% endhighlight %} # 高亮区域结束
{% endraw %}
{% endhighlight %}

不难看出其语法和 Ruby 语言如出一辙（毕竟是用 ruby 写的）。

## YAML

YAML 是 "YAML Ain't a Markup Language"（YAML 不是一种标记语言）的递归缩写。在开发的这种语言时，YAML 的意思其实是："Yet Another Markup Language"（仍是一种标记语言），但为了强调这种语言以数据做为中心，而不是以标记语言为重点，而用反向缩略语重命名。

YAML 参考了其他多种语言，包括：XML、JSON、Python、C语言、Perl 以及 [RFC2822](https://www.rfc-editor.org/rfc/rfc2822.txt)。

数据结构可以用类似大纲的缩排方式呈现，结构通过缩进来表示，连续的项目通过减号 `-` 来表示，map结构里面的 key/value 对用冒号 `:` 来分隔。

```yaml
---
receipt:     Oz-Ware Purchase Invoice
date:        2012-08-06
customer:
    given:   Dorothy
    family:  Gale
   
items:
    - part_no:   A4786
      descrip:   Water Bucket (Filled)
      price:     1.47
      quantity:  4

    - part_no:   E1628
      descrip:   High Heeled "Ruby" Slippers
      size:      8
      price:     133.7
      quantity:  1

bill-to:  &id001
    street: | 
            123 Tornado Alley
            Suite 16
    city:   East Centerville
    state:  KS

ship-to:  *id001   

specialDelivery:  >
    Follow the Yellow Brick
    Road to the Emerald City.
    Pay no attention to the
    man behind the curtain.
...
```

注意在 YAML 中，字符串不一定要用双引号标示。另外，在缩进中空白字符的数目并不是非常重要，只要相同层次结构的元素左侧对齐就可以了（不过不能使用 TAB 字符）。这个文件的的顶层由七个键值组成：其中一个键值 `items` ，是个两个元素构成的数组（或称清单），这清单中的两个元素同时也是包含了四个键值的散列表。文件中重复的部分用这个方法处理：使用锚点 `&` 和引用 `*` 标签将"bill-to"散列表的内容复制到"ship-to"散列表。也可以在文件中加入选择性的空行，以增加可读性。在一个文件中，可同时包含多个文件，并用 `---` 分隔。选择性的符号 `...` 可以用来表示文件结尾（在利用流的通信中，这非常有用，可以在不关闭流的情况下，发送结束信号）。[^1]


## Markdown

知道这个语言的时候简直激动不已，书写文档然后生成 html 文件简直一气呵成。由于太长了，有兴趣的可以参考[Markdown语法 @图灵社区](http://www.ituring.com.cn/article/775)。


[^1]: [YAML @维基百科](https://zh.wikipedia.org/zh-cn/YAML)

