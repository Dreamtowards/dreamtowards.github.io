

# Setup GoogleAnalytics on GithubPages.
Posted on Jun 31, 2021.
Tags: Bullshits

流量分析的用处自然不必多说。今天 我们将使用最新的可靠方法 在GithubPages基本版本上设置 GoogleAnalytics (GA4) 分析设置。

需要注意的是，本文采用的是新版GA4流量分析版本，而非网上大部分教程和现有'框架'所用的 早期UA版本(Universal Analytics)。

> 选择分析平台: 流量分析平台有很多，中国大陆的有类似 腾讯分析 百度分析 或较小的 站长分析等。国际上也有较多 商业性的 或轻量级的。
> 但是目前最受推荐的还是 GoogleAnalytics，因为他的发展历史 功能 安全 可靠性。

## Approximate steps and Methodology

我们先说大致的步骤和方法。这将更有助于高效理解。  
有可能您看了本段后就大致知道主要该怎么做了 并且可以自由地搭配/使用你自己的方法 例其他的分析平台 或任何你想做的事。

1. 你的终极目的是 需要有一块"全局的"html代码嵌入在所有或公开页面中。  
   因此 请你先准备好这样一个代码，无论是从某些分析平台获得 或是你自己编写的。  
   这通常是一个script代码 你应该已经知道了 它可以做很多事情。  
   
2. 为了让GithubPages的"每个"页面都有这个代码, - 去使用/扩充/修改 GithubPages Themes.
   <small markdown="1">
   1. [ ] 你不太可能给每个页面都手动复制上去那块全局代码，那样会有些混乱繁琐并且 不太靠谱
   2. [ ] 使用Vue.js的"思维模式"，让网页变成一个客户端 那样当然就可以完全掌控内容了。但是这样过于复杂了
   3. [x] 你可能已经想到了 - GithubPages Themes. 可不可以把全局代码作为主题的一部分。
      </small>  
      我使用的是 jekyll-theme-primer 主题. 但无论哪个主题 方法大致是一样的:  
      请在你的仓库中 创建/修改这个文件: [/_layouts/default.html](https://github.com/pages-themes/primer/blob/master/_layouts/default.html) 
      它将是您所有"自动生成"页面的主要布局结构。  
      随后 将你的代码放入其中合适的位置.

3. 完成 看看效果如何?

## Setup GoogleAnalytics GA4 Analytics objective.

1. 注册/进入到 GoogleAnalytics 主界面
2. 页面左下角 [管理] 页面按钮 -> [管理] 选项卡
3. [创建账号] (要是还没有的话)(是分析账号 不用担心 只要设置个名字即可)
4. [创建媒体资源]. 
5. 准备好你的 全局网站内嵌代码. (顺便一说 你的`衡量ID` 应该是 `G-` 开头但形式 但可以不用在意这些)

你可能将会得到这样一个代码
```html
<!-- Global site tag (gtag.js) - Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-********"></script>
<script>
    window.dataLayer = window.dataLayer || [];
    function gtag(){dataLayer.push(arguments);}
    gtag('js', new Date());

    gtag('config', 'G-********');
</script>
```

## Edit GithubPages Theme.

1. 去查找你的 GithubPages 主题的源代码仓库.
2. 找到 `/_layouts/default.html` 文件. [(例)](https://github.com/pages-themes/primer/blob/master/_layouts/default.html)
   它将是您所有"自动生成"页面的主要布局结构。 
3. 将它复制到你仓库的相同位置。
4. 修改其中的内容 把你的全局代码放在其中合适的位置.
5. 完成 试试看效果吧.

<br><br>