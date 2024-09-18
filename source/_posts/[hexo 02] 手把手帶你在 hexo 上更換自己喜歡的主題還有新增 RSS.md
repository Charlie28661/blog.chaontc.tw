---
title: '[hexo 02] 手把手帶你在 hexo 上更換自己喜歡的主題還有新增 RSS'
date: 2024-9-18
tags: Develope
---

## hexo themes

- 到官網的 [主題列表](https://hexo.io/themes/) 選一個自己喜歡的主題

- 這邊就示範 ```Ochuunn``` 這個主題

    ![image](https://hackmd.io/_uploads/rk7uUXO6R.png)

- 點選 ```Ochuunn``` 超連結可以到 Github 的 repositories

    ![image](https://hackmd.io/_uploads/r1Q4vm_pA.png)

- 基本上每個主題都會附  README  照著做就可以架起來了

- 如果沒有可以參考我的作法

- 很重要的一件事情，如果 README 中有要安裝的套件就一定要安裝，否則 100% 失敗

### 使用 Ochuunn 主題示範

- 以下是他的 README

    ![image](https://hackmd.io/_uploads/rkrXO7_TC.png)

- 首先使用 ```npm install hexo-renderer-pug --save``` 安裝 renderer-pug 套件

- 到資料夾的跟目錄使用以下指令

    ```bash=
    git clone https://github.com/ochukai/hexo-theme-ochuunn.git themes/ochuunn
    ```
    
- 這是將我們選的 ```ochuunn``` 主題從 Github 上的 repositories 複製下來到本地的 ```themes/ochuunn``` 資料夾中

- 接著到 ```ochuunn``` 資料夾底下將 ```_config.default.yml``` 改名成 ```_config.yml```

- 然後到根目錄底下的 ```_config.yml``` 最後面會看到 ```theme``` 將他從 ```landspace``` 改成我們要的主題 ```ochuunn```

- 像這樣:

    ![image](https://hackmd.io/_uploads/SJD1qm_aR.png)

- 接著就可以
    ```bash=
    hexo g 
    hexo s
    ```

- 確認沒問題後就可以部屬了，如何部屬到 Github Pages 可以看我上一篇教學

- 接著我們要來新增 RSS 啦 !

## Add RSS on your hexo website

- 這邊示範沒有內建 RSS 功能的模板，如何自行新增 RSS 功能

- 安裝 RSS 套件
    ```bash=
    npm install hexo-feed --save-dev
    ```
    
- 在 ```themes/layout``` 底下新增一個 ```rss.ejs```

- 直接在剛剛創的 ```rss.ejs``` 貼上

    ```bash
    <?xml version="1.0"?>
    <rss version="2.0">
        <channel>
            <title><%= config.title %><%= tag ? ` • Posts by "${tag}" tag` : '' %><%= category ? ` • Posts by "${category}" category` : '' %></title>
            <link><%= config.url %></link>
            <description><%= config.description %></description>
            <language><%= config.language %></language>
            <pubDate><%= moment(lastBuildDate).locale('en').format('ddd, DD MMM YYYY HH:mm:ss ZZ') %></pubDate>
            <lastBuildDate><%= moment(lastBuildDate).locale('en').format('ddd, DD MMM YYYY HH:mm:ss ZZ') %></lastBuildDate>
            <%_ for (const { name } of (tags || [])) { _%>
            <category><%= name %></category>
            <%_ } _%>
            <%_ for (const post of posts) { _%>
            <item>
                <guid isPermalink="true"><%= post.permalink %></guid>
                <title><%= post.title %></title>
                <link><%= post.permalink %></link>
                <%_ for (const tag of (post.tags ? post.tags.toArray() : [])) { _%>
                <category><%= tag.name %></category>
                <%_ } _%>
                <pubDate><%= moment(post.date).locale('en').format('ddd, DD MMM YYYY HH:mm:ss ZZ') %></pubDate>
                <description><![CDATA[ <%= post.content %> ]]></description>
            </item>
            <%_ } _%>
        </channel>
    </rss>
    ```
    
- 然後至根目錄的 ```_config.yml``` 查找 ```feed``` 或 ```rss``` 相關字眼

- 然後修改成這樣

    ```bash
    rss:
    enable: true
    template: "themes/layout/rss.ejs"
    output: "rss.xml"
    ```
    
- 接著就可以重新部屬了
 
    ```bash=
    hexo g 
    hexo s
    ```
    
- 要使用時可以直接在網址後加上 ```/rss.xml``` 就可以訪問了