---
title: atom配置vue前端开发环境
date: 2018-06-25 09:48:27
description: 使用atom配置前端vue开发环境
tags:
  - atom
---

1.安装好atom后,安装常用的几个插件

    prettier-atom : 格式化代码插件

    atom-axios : vue-axios提示插件

    autoclose-html : 自定补全闭合html标签插件

    file-icons : 文件图标,便利区分不同类型的文件

    autocomplete-paths : 自动提示补全文件路径插件

    language-vue : atom支持vue的插件

    language-vue-component : 高亮显示vue组件插件

    vue2-autocomplete : vue2.0+提示插件

    linter-eslint : eslint规则校验插件

    px2rem-plus : px转rem插件

    minimap : 在编辑器右边出现预览源代码(类似sublime text3右侧预览导航效果)的插件

    下面是支持markdown的插件:

    markdown-toc : 对markdown文档生成目录的插件

    markdown-table-editor : markdown文档表格编辑插件

2.安装好对应的插件后,大部分情况能够使用,但是vue项目需要支持eslint校验的话,需要对linter-eslint设置下面的 Lint-HTML-Files进行勾选

![](/images/eslint.png)

3.默认的prettier格式化的规则是不符合eslint的,比如会对每行尾部增加分号,单引号变变为双引号,需要修改其配置为下:

![](/images/prettier.png)
