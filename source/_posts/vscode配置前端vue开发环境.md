---
title: vscode配置前端vue开发环境
date: 2018-12-13 14:05:39
description: vscode配置前端vue开发环境
tags:
  - vscode
---

    摘要:
      使用vscode配置前端vue开发环境

1.安装好vscode后,安装常用的几个插件

    Auto Close Tag : 自动闭合标签插件

    Beautify : 格式化js,json,css,sass,html等文件

    ESLint : 使用eslint规范对代码进行处理

    file-icons : 文件图标,便利区分不同类型的文件

    Monokai Theme : 一款类似sublime text主流的主题设置,使界面美观,享受美好的编码心情

    Path Intellisense : 自动提示文件路径插件

    Prettier : 因为vscode默认的格式化是不能通过eslint校验规范的,需要改为此插件

    Vetur : vscode的vue工具插件

    HTML CSS Support : 在标签中class属性的时候,提示class的名称

    px2rem : 将像素值转为rem插件

    下面是markdown相关的插件

    Markdown-TOC : 对markdown文档生成目录的插件,有2个,请选择作者为AlanWalk的

2.安装好上述插件后,对其进行配置设置(2018-12-15更 v.1.30.0)

    在文件->首选项->设置->用户设置里面写入以下配置

  ```json
  {
    "workbench.startupEditor": "newUntitledFile",
    "window.title": "${dirty}${activeEditorLong}${separator}${rootName}${separator}${appName}",
    "extensions.ignoreRecommendations": false,
    "workbench.iconTheme": "file-icons",
    "workbench.colorTheme": "Monokai",
    "vetur.format.defaultFormatter.js": "vscode-typescript",
    "extensions.autoUpdate": false,
    "update.channel": "none",
    "eslint.autoFixOnSave": false,
    "files.autoSave": "off",
    "eslint.validate": [
        "javascript",
        "javascriptreact",
        {
            "language": "vue",
            "autoFix": true
        },
        {
            "language": "html",
            "autoFix": true
        },
        "vue"
    ],
    "eslint.options": {
        "plugins": [
            "html"
        ]
    },
    "editor.tabSize": 2,
    "prettier.eslintIntegration": true,
    "vetur.format.defaultFormatterOptions": {
        "html": "prettier",
        "css": "prettier",
        "postcss": "prettier",
        "scss": "prettier",
        "less": "prettier",
        "js": "prettier",
        "ts": "prettier",
        "stylus": "stylus-supremacy",
        "wrap_attributes": "force-aligned",
        "prettier": {
            "semi": false,
            "singleQuote": true
        }
    },
    "eslint.alwaysShowStatus": true,
    "window.titleBarStyle": "custom",
    "files.eol": "\n"
}
  ```
3.vscode 1.29版本以上markdown-toc生成目录默认是有问题的,1.29版本以下能够直接正常使用

        1.29版本以上,请在file->preferences->setting->text editor中找到Eol配置的地方,设置为\n即可.
        详细情况见 https://github.com/AlanWalk/markdown-toc/issues/65

4.vscode 1.30版本在file->preferences->setting下找不到打开setting.json文件的入口了.如下图,可以在系统路径下找到该文件编辑即可.

        文件路径
        Windows: %APPDATA%\Code\User\settings.json
        macOS: $HOME/Library/Application Support/Code/User/settings.json
        Linux: $HOME/.config/Code/User/settings.json

![](/images/vscode.1.30.png)