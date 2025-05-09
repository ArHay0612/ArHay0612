---
title: 如何在vscode自動生成md目錄並轉換格式
date: 2025-04-28 18:21:20
description: vscode利用插件自動生成md目錄並轉換格式
tags:
  - markdown
  - vscode
  - pdf
  - html
---

## vscode 自動生成 md 目錄並轉換格式

- [前言](#前言)
- [工具準備](#工具準備)
  - [vscode](#vscode)
  - [markdownlint](#markdownlint)
  - [Markdown Preview Enhanced](#markdown-preview-enhanced)
  - [Markdown All in One](#markdown-all-in-one)
  - [Prettier](#prettier)
- [生成目錄](#生成目錄)
- [轉換md格式](#轉換md格式)

### 前言

之前在寫 API 文檔時，由於文件過長，跳轉十分麻煩，後面才想起來可以利用目錄跳轉就查找對應的 Markdown 目錄生成工具

### 工具準備

- vscode
- markdownlint （vscode 插件）
- Markdown Preview Enhanced （vscode 插件）
- Markdown All in One （vscode 插件）
- Prettier （vscode 插件）

![pluginImg1](/images/pluginImg1.jpg)
![pluginImg2](/images/pluginImg2.jpg)

#### vscode

用於編寫 markdown 文件並預覽的 IDE

#### markdownlint

用於檢查 md 文件的編寫規範

#### Markdown Preview Enhanced

用於預覽 md 文件和轉換成其他格式，如 PDF，HTML

#### Markdown All in One

內置多種指令便於生成各種 md 格式

#### Prettier

用於 format md 文件（其實 markdownlint或者Markdown All in One都可以，但是我對比發現這個更好用）

### 生成目錄

1. vscode打開md文件
2. 將光標放在需要生成目錄的位置
3. 然後按下 `ctrl+shift+P` 輸入 `Markdown Preview Enhanced: Create TOC`
   1. _使用關鍵字即可，如 mpetoc_
4. 會在光標位置自動生成以下語句  

   ```xml
   <!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->
   ```

5. 修改相關屬性
   1. depthFrom ：從第幾級目錄開始生成
   2. depthTo ： 到第幾級目錄結束
   3. orderedList : 是否使用有序列表

6. 保存，會根據屬性自動生成目錄

### 轉換md格式

1. md文件編寫好後，右上角按鈕可以點開預覽
![previewBtn](/images/previewBtn.jpg)

2. 在預覽界面點擊右鍵，會有導出選項
![output](/images/output.png)
![output2](/images/output2.png)
  _右鍵菜單還有其他選項，比如更換preview主題等，請自行摸索，本文不再贅述。_

3. 根據所需的類型格式導出即可

**此方法僅支持轉換格式後的正常跳轉！**
**如果是通過hexo發布的md網頁請使用Markdown All in One: Create Table of Contents,否則發布後不能跳轉！！！**
