---
title: 使用hexo+Next創建和部署個人博客
date: 2025-04-24 09:57:58
description: 通過hexo生成或維護個人博客靜態網頁,更換Next主題，並deploy到github
tags: 
    - hexo
    - nodejs
---

## 安裝NodeJs和Git

### 下载

- [Node.js](https://nodejs.org/)
- [Git](https://git-scm.com/)

根據安裝界面自行安裝

## 安裝hexo

[Hexo官网](https://hexo.io/zh-cn/index.html)

打开终端（或命令行窗口），运行以下命令来安装 Hexo (-g 為全局安裝)

```bash
npm install hexo-cli -g
```

## hexo新建項目，或從github下載博客源碼

在你选择存储博客文件的目录中，执行以下命令来创建一个新的 Hexo 项目：

```bash
hexo init blog
cd blog
npm install
```

## 配置hexo

编辑**根目錄**下的 _config.yml 文件，配置你的基本信息和其他相关设置

具體參考：<https://hexo.io/docs/configuration>

例如：

```yml
# Site
title: ArHay
subtitle: 当赤道留住雪花，眼泪融掉细沙
description: 
keywords: ArHay
author: ArHay
language: zh-CN
timezone: ''
```

## 創建文章

使用以下命令创建新的博客文章

```bash
hexo new post "My New Post"
```

## 生成靜態文件

在博客根目录下执行以下命令生成静态文件，文件存儲在 public 文件夾下：

```bash
hexo generate
```

或

```bash
hexo g
```

## 本地預覽

```bash
hexo server
```

或

```bash
hexo s
```

## 部署到GithubPage

### 安装 hexo-deployer-git

```bash
npm install hexo-deployer-git --save
```

参数 --save 的作用是在项目下的package.json文件记录安装过的依赖包名称。

当复制项目到另外的电脑上,只需运行命令: npm i 就能自动安装项目用到的依赖包。

### 编辑 _config.yml 文件

配置你的博客信息、GitHub Pages 信息和其他相关设置

```yml
# Deployment
## Docs: https://hexo.io/docs/one-command-deployment
deploy:
  type: git
  repository: https://github.com/ArHay0612/arhay0612.github.io.git
  branch: master
```

### 部署

运行以下命令将生成的静态文件部署到 GitHub Pages

```bash
hexo deploy
```

或

```bash
hexo d
```

然後根據彈窗完成登錄，即可部署完成

## 訪問博客

<https://ArHay0612.github.io>

ArHay0612是我的用戶名。請自行替換到自己的github usernames

## 主題配置

我用的是Next，就以Next為例

參考:

- [Next Github Page](https://github.com/next-theme/hexo-theme-next)

- [NexT Documentation](https://theme-next.js.org/)

### 安裝Next主題

在博客項目根目錄下把Next主題 clone 到 themes/next 文件夾

```bash
cd hexo-site
git clone https://github.com/next-theme/hexo-theme-next themes/next
```

### 編輯 _config.yml 文件

在**根目錄下**找到 _config.yml

然後在theme配置項修改成

```yml
theme: next
```

### Next 主題選擇

next 自帶4種類型的主題方案

- Muse
- Mist
- Pisces
- Gemini

在 **`themes\next\_config.yml`** 文件下
搜索 `Scheme Settings`

然後選擇自己喜歡的主題方案

```yml
# Schemes
# scheme: Muse
# scheme: Mist
scheme: Pisces
# scheme: Gemini

# Dark Mode
darkmode: true
```

### Next 主題配置

Next 主題所有配置都在 **`themes\next\_config.yml`** 文件下

根據需求選擇自己需要的內容，如菜單，統計，搜索，背景等……

具體參考：

- [Next主題設置documentation](https://theme-next.js.org/docs/theme-settings/)

例如：

```yml
menu:
  home: / || fa fa-home #主頁
  about: /about/ || fa fa-user #關於頁
  tags: /tags/ || fa fa-tags  #標籤
  #categories: /categories/ || fa fa-th
  archives: /archives/ || fa fa-archive #歸檔
  #schedule: /schedule/ || fa fa-calendar
  #sitemap: /sitemap.xml || fa fa-sitemap
  #commonweal: /404/ || fa fa-heartbeat
```

## 插件選擇

### 本地搜索

該功能允許用戶在網站內快速搜索文章內容，而不依賴於外部搜索服務（如 Algolia）。本地搜索的數據由 Hexo 生成，並存儲在靜態文件中，適合小型或中型博客。

參考：<https://github.com/next-theme/hexo-generator-searchdb>

#### 安裝

```bash
npm install hexo-generator-searchdb --save
```

#### 配置插件

在 Next 的配置文件 **`themes\next\_config.yml`** 中修改成以下內容：

```yml
# Local Search
# Dependencies: https://github.com/next-theme/hexo-generator-searchdb
local_search:
  enable: true #控制是否啟用本地搜索功能
  # 設置每篇文章中顯示的搜索結果數量。 -1 顯示所有。
  top_n_per_article: 1
  # 控制是否將 HTML 字符（如 &lt; 和 &gt;）轉換為可讀的文本。
  unescape: false
  # 控制是否在頁面加載時預加載搜索數據
  preload: true
```

### Hexo Pangu

Hexo Pangu 是一個用於自動處理中英文之間空格的插件，基於 Pangu.js。它的作用是自動在中文與英文、數字、符號之間插入適當的空格，從而提升文章的可讀性，符合中文排版的最佳實踐。

參考：

- <https://github.com/next-theme/hexo-pangu>
- <https://github.com/vinta/pangu.js>

#### 安裝

```bash
npm install hexo-pangu --save
```

### Hexo Word Counter

Hexo Word Counter 是一個用於統計文章字數和預估閱讀時間的插件。它的主要作用是為每篇文章顯示字數和閱讀時間，幫助讀者了解文章的長度和閱讀所需的時間，從而提升用戶體驗。

參考：<https://github.com/next-theme/hexo-word-counter>

#### 安裝

```bash
npm install hexo-word-counter --save
```

#### 配置插件

在 Next 的配置文件 **`themes\next\_config.yml`** 中修改成以下內容：

```yml
symbols_count_time:
  separated_meta: true
  item_text_total: false
```

### oh-my-live2d

是一個用於在 Hexo 博客中添加 Live2D 模型 的插件。它的作用是為網站添加一個可交互的 2D 虛擬角色，通常顯示在網站的角落，增強網站的趣味性和吸引力。（看板娘）

參考：<https://github.com/next-theme/hexo-word-counter>

#### 安裝

```bash
npm install hexo-oh-my-live2d --save
```

#### 配置插件

在博客根目錄的配置文件 **`.\_config.yml`** 中修改成以下內容：

```yml
OhMyLive2d:
  enable: true
  CDN: https://registry.npmmirror.com/oh-my-live2d/latest/files
  option:
    dockedPosition: 'right' # 模型停靠位置 默认值: 'right' 可选值: 'left' | 'right'
    menus: # 侧边栏菜单配置
      items:  # 菜单项配置
        - id: 'github'
          icon: 'icon-switch'
          title: '我的github'
          onClick: ()=>window.open('https://github.com/ArHay0612')

    mobileDisplay: true # 是否在移动端显示
    models:
      - path: \live2d_models\fll\fll.model3.json # 模型的路径
        mobilePosition: [-10, 23] # 移动端时模型在舞台中的位置。 默认值: [0,0] [横坐标, 纵坐标]
        mobileScale: 0.1 # 移动端时模型的缩放比例 默认值: 0.1
        mobileStageStyle: # 移动端时舞台的样式
          width: 180
          height: 166
        motionPreloadStrategy: IDLE # 动作预加载策略 默认值: IDLE 可选值: ALL | IDLE | NONE
        position: [-10, 35] # 模型在舞台中的位置。 默认值: [0,0] [横坐标, 纵坐标]
        scale: 0.15 # 模型的缩放比例 默认值: 0.1
        # showHitAreaFrames: false # 是否显示点击区域 默认值: false
        stageStyle:
          width: 300
          height: 450
    parentElement: document.body #为组件提供一个父元素，如果未指定则默认挂载到 body 中
    primaryColor: 'var(--btn-bg)' # 主题色 支持变量
    sayHello: false # 是否在初始化阶段打印项目信息
    tips:
      style:
        width: 230
        height: 120
        left: calc(50% - 20px)
        top: -100px
      mobileStyle:
        width: 180
        height: 80
        left: calc(50% - 30px)
        top: -100px
      idleTips:
        interval: 10000
        duration: 1500
        message:
          - 你好呀,欢迎来到ArHay的小站~
```

#### 更換模型

##### 在互聯網上下載自己喜歡的live2D模型

參考：

- [Live2D官方示例数据集（可免费下载](https://www.live2d.com/zh-CHS/learn/sample/)

- [模之屋](https://www.aplaybox.com/model/model)

- [GitHub用戶Eikanya分享](https://github.com/Eikanya/Live2d-model)

##### 項目引入模型

在source 文件夾下，新建文件夾 `live2d_models\`

把下載下來的整個live2d資源包整個放進`live2d_models\`文件夾中

然後修改Hexo的主配置文件 `_config.yml`

```yml
models:
      - path: \live2d_models\fll\fll.model3.json # 模型的路径
```

此處路徑應為這個模型包下的json文件

##### 預覽

![live2d_demo](/images/live2d_demo.jpg)

### 博客訪客和閱讀量統計

busuanzi（不蒜子） 是一個輕量級的網站訪問量和文章閱讀量統計工具，適合用於 Hexo 博客等靜態網站。它無需註冊或配置後端服務，通過嵌入 JavaScript 即可實現訪問量和閱讀量的統計。

參考：<https://busuanzi.ibruce.info/>

#### 配置插件

在 Next 的配置文件 **`themes\next\_config.yml`** 中修改成以下內容：

```yml
# Show Views / Visitors of the website / page with busuanzi.
# For more information: http://ibruce.info/2015/04/04/busuanzi/
busuanzi_count:
  enable: true
  total_visitors: true
  total_visitors_icon: fa fa-user
  total_views: true
  total_views_icon: fa fa-eye
  post_views: true
  post_views_icon: far fa-eye
```

---
>等我發掘到好玩的插件會繼續更新～
>
>*未完待續……*
