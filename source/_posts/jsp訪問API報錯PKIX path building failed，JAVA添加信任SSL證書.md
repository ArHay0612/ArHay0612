---
title: jsp訪問API報錯PKIX path building failed，JAVA添加信任SSL證書
date: 2025-04-10 15:17:19
description: JAVA添加信任SSL證書
tags:
  - jsp
  - SSL
  - Certification
  - JAVA
---

## jsp訪問API報錯PKIX path building failed，JAVA添加信任SSL證書

### 問題

```text
JSP訪問API時，報錯PKIX path building failed
```

### 原因

```text
HTTPS域名的SSL Certification 不在 JDK/JRE 的證書庫中,被JAVA認為是不可信的HTTPS域名。
```

### 解決辦法

#### 下載目標地址的證書

```text
    通過瀏覽器打開API,在地址欄 "HTTPS" 旁邊點擊按鈕,彈出相關證書信息窗口。

    通過Export，得到後綴為 .crt 的 base64 證書文件。(如可選請選擇base64)
```

#### 找到JAVA安裝目錄（即JAVA_HOME）

e.g. `C:\Program Files\Java\jdk-21`

##### 找到證書庫存放位置

```text
證書庫存放在 `%JAVA_HOME%\lib\security` 下，一個名為 `cacerts` 的文件

記錄路徑：`"C:\Program Files\Java\jdk-21\lib\security\cacerts"`
```

##### 找到目標地址下載的證書位置

e.g. `"C:\Users\ArHay\Downloads\example.crt"`

##### 使用命令行註冊Certification到證書庫

1. 進入 `"C:\Program Files\Java\jdk-21\bin"`文件夾

   (若已經配置全局變量`%JAVA_HOME%`,可以忽略該步驟)
2. 使用命令keytool註冊證書

    ```bash
    keytool -import -alias example -keystore "C:\Program Files\Java\jdk-21\lib\security\cacerts" -file "C:\Users\ArHay\Downloads\example.crt"
    ```

3. 輸入兩次密碼

   ```text
   changeit
   ```

##### 結果

再次嘗試訪問API，無報錯，正常返回。問題解決。
