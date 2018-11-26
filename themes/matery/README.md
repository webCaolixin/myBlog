# hexo-theme-matery

[![HitCount](http://hits.dwyl.io/blinkfox/hexo-theme-matery.svg)](http://hits.dwyl.io/blinkfox/hexo-theme-matery) [![GitHub issues](https://img.shields.io/github/issues/blinkfox/hexo-theme-matery.svg)](https://github.com/blinkfox/hexo-theme-matery/issues) [![GitHub license](https://img.shields.io/github/license/blinkfox/hexo-theme-matery.svg)](https://github.com/blinkfox/hexo-theme-matery/blob/master/LICENSE) [![Hexo Version](https://img.shields.io/badge/hexo-%3E%3D%203.0-blue.svg)](http://hexo.io) [![GitHub forks](https://img.shields.io/github/forks/blinkfox/hexo-theme-matery.svg)](https://github.com/blinkfox/hexo-theme-matery/network) [![GitHub stars](https://img.shields.io/github/stars/blinkfox/hexo-theme-matery.svg)](https://github.com/blinkfox/hexo-theme-matery/stargazers)

[中文文档](README_CN.md)

> This is a Hexo blog theme with 'Material Design' and responsive design.

## Feature

- Simple and beautiful, and post is Beautiful and readable.
- [Material Design](https://material.io/)design.
- Responsive design,which can be displayed well on desktop, tablet, mobile phone, etc.
- Changing 'banner' picture dynamically everday.
- Blog posts list with waterflow(There will be 24 images if the articl dosn't have  featured pictures).
- Archive page with timeline.
- Comment module of Gitment and Disqus.

## Download

You should have a [Hexo](https://hexo.io/zh-cn/) blog when you see it here.If not,try to use the hexo and [Markdown](https://www.appinn.com/markdown/) to write your blog and post.
Click[here](https://codeload.github.com/blinkfox/hexo-theme-matery/zip/develop.zip)to download the last stable version of the code.After decompressing,copy the `hexo-theme-matery` folder
to your `themes` folder of your Hexo.Of course,you can uer `Git clone` to download in your `themes` folder.

```bash
git clone https://github.com/blinkfox/hexo-theme-matery.git
```

## Configuration

### Modify theme

Modify the value of `theme` in `_config.yml` of Hexo's root folder: `theme: hexo-theme-matery`.

### config tags page

`tags`page is to show all of tags.If the `source` directory of your blog doesn't have `tags/index.md` file,you need to new one like this:

```bash
hexo new page "tags"
```

to edit your new page files`/source/tags/index.md` ,you need somethings as follows:

```yml
title: tags
date: 2018-09-10 18:23:38
type: "tags"
layout: "tags"
```

### Code highlight

Hexo theme uses Hexo's plugin[hexo-prism-plugin](https://github.com/ele828/hexo-prism-plugin) to show the code highlight instead of its own theme.The Installation commands are as follows:

```bash
npm i -S hexo-prism-plugin
```

Then,modify the value of `highlight.enable` to `false` in `_config.yml` file of Hexo root folder，and add the configuration of `prism` plugin as follows:

```yml
highlight:
  enable: false

prism_plugin:
  mode: 'preprocess'    # realtime/preprocess
  theme: 'tomorrow'
  line_number: false    # default false
  custom_css:
```

### Search

The theme uses the Hexo plugin[hexo-generator-search](https://github.com/wzpan/hexo-generator-search) to search the content,and the Installation commands are as follows:

```bash
npm install hexo-generator-search --save
```

Add configuration of `_config.yml` file in Hexo root folder as follows：

```yml
search:
  path: search.xml
  field: post
```

### Translate Chinese Link to Pinyin

Defualt permalinks of Hexo will include Chinese if your atrticle's title is Chinese.But it's adverse to `SEO`,and `gitment` comments don't suport Chinese Link as well.We can use the [hexo-permalink-pinyin](https://github.com/viko16/hexo-permalink-pinyin) of Hexo plugin to generate permalinks of Chinese Pinyin  when generating posts.

Installation commands are as follows：

```bash
npm i hexo-permalink-pinyin --save
```

Add such configurations in `_config.yml` file of  Hexo:

```yml
permalink_pinyin:
  enable: true
  separator: '-' # default: '-'
```

> **Note*:[hexo-abbrlink](https://github.com/rozbo/hexo-abbrlink) can genarate non-Chinese link in addtion to this plugin.

### Modify social links

You can find the content of `social-link` and add links you need in the `/layout/_partial/footer.ejs` and `/layout/_partial/mobile-nav.ejs` file of theme files as follows:

```html
<a href="https://github.com/blinkfox" class="tooltipped" target="_blank" data-tooltip="访问我的GitHub" data-position="top" data-delay="50">
    <i class="fa fa-github fa-lg"></i>
</a>
```

You can search social icon such as `fa-github` in [Font Awesome](https://fontawesome.com/icons).There are common social icons you can reference:

- Facebook: fa-facebook
- Twitter: fa-twitter
- Google-plus: fa-google-plus
- Linkedin: fa-linkedin
- Tumblr: fa-tumblr
- Medium: fa-medium
- Slack: fa-slack
- Sina Weibo: fa-weibo
- Wechat: fa-wechat
- QQ: fa-qq

> **Note**: The version of `Font Awesome` is `4.5.0`.

## Post Front-matter example

The following are post `Front-matter` example,and all content is **not required**.But we still suggest you write the value of `title`.Of course ,you'd better write all of these information.

```yml
---
title: typora-vue-theme Theme introduction
date: 2018-09-07 09:25:00
author: Qi Zhao
img: /source/images/xxx.jpg # or:http://xxx.com/xxx.jpg
tags:
  - Typora
  - Markdown
---
```

> **Note**: post's featured piature will take remainder if not writing the `img` property,and chose the featured picture of theme to let all of post's picture **have their own characteristics**.

## Screenshot

### Home

![Home](http://static.blinkfox.com/hexo-matery-index1.png)

![Home post list](http://static.blinkfox.com/hexo-matery-index2.png)

### Post page

![Post](http://static.blinkfox.com/hexo-matery-post1.png)

![Post's other parts](http://static.blinkfox.com/hexo-matery-post2.png)

### post's contnet picture

![post's contnet picture](http://static.blinkfox.com/hexo-matery-image.png)

### Label page

![Label](http://static.blinkfox.com/hexo-matery-tags1.png)

![Seletec page](http://static.blinkfox.com/hexo-matery-tags2.png)

### Archive page

![Archive](http://static.blinkfox.com/hexo-matery-archive.png)

## Custom modification

You can modify some custom modification in `_config.yml` as follows:

- Menu
- Inspirational quotes on Home
- `favicon` and `Logo`
- The map of default featured pictures. The theme will take remainde according to `hashcode` of post title if the post dose not set featured piactures.

**I think everyone should have their own style and feature of blog**。if you are not satisfiled with functions and theme color,you can modify by yourself,and more free functions and deatil need to be modified by modify source code when it is hard to be finished in `_config.yml`.

### modeing theme color

Search `.bg-color` to modify background color in `/source/css/matery.css` in theme file：

```css
/* The overall background color, including navigation, mobile navigation, footer, tab, etc.. */
.bg-color {
    background-color: #0f9d58 !important;
}

/* The color of the text with the same background color is only used in one place on the front page. You can also apply it to other places.*/
.text-color {
    color: #0f9d58 !important;
}

@-webkit-keyframes rainbow {
   /* Dynamically switch background colors. */
}

@keyframes rainbow {
    /* Dynamically switch background colors. */
}
```

### Modify banner picture and post's featured pictures

You can change `banner` pictures in `/source/medias/banner` as you like .Theme code can switch  dynamically every day and just need 7 pictures.If you master `JavaScript`,you can change it to your favorite swithing logic,such as Random switching.The code of switching `banner`is in `<script></script>` of `/layout/_partial/bg-cover.ejs`file.

```javascript
$('.bg-cover').css('background-image', 'url(/medias/banner/' + new Date().getDay() + '.jpg)');
```

There are 24 featured pictures in `/source/medias/featureimages`,you can add or delete,and modify it in `_config.yml` at the sametime.