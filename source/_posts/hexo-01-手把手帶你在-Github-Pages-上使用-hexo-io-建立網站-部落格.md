---
title: '[hexo 01] 手把手帶你在 Github Pages 上使用 hexo.io 建立網站/部落格'
date: 2024-09-17 22:02:27
tags: Develope
---

## 環境安裝

### 依賴及 hexo
- git 
    - [Windows](https://git-scm.com/download/win)
    - [Mac](https://sourceforge.net/projects/git-osx-installer/)
    - Linux (Ubuntu):

        ```bash=
        sudo apt-get install git-core
        ```
        
- nodejs
    - [官方下載](https://nodejs.org/en/download/package-manager)

- hexo
 
    - 確保安裝完畢 nodejs 後在終端機輸入
        ```bash=
        npm install -g hexo-cli
        ```

## 使用 hexo 架站

### 初始化

- 終端機輸入 (如不指定 <folder> 將產生為 blog)
    
    ```bash=
    hexo init <folder>
    ```
    
- 輸入後會自動產生一個資料夾，然後就可以進去資料夾底下安裝套件了
    
    ```bash=
    cd <folder>
    npm install
    ```
    
- 目前資料夾大概會長得像以下這樣
    
![image](https://hackmd.io/_uploads/rku6xaLTA.png)

- node_modules:
    - 這個就是我們安裝的套件包，不太需要理會
    - 如果有遇到有關套件缺少的錯誤可以重新使用 ```npm install``` 安裝即可

- scaffolds
    - 把他想成是模板即可
    - 等等會教怎麼使用指令新增頁面和貼文，新增出來的東西就是用這裡的模板

- source
    - 我們新增的頁面和貼文都會被保存在這裡

- themes
    - 網站的主題，後續會教怎麼更換網站主題

- _config.yml
    - 這是網站的主要配置檔，可以在這裡完成大部分的設定

- package.json
    - 這也是存套件相關的東西，不太需要理會
    
### 新增網站頁面和貼文

#### 新增頁面
    
- 如要新增網站的頁面可以使用以下指令
    
    ```bash=
    hexo new page "網頁名稱"
    ```

- 產生出來後會在 source 檔案夾底下
- 產生的檔案一樣是 .md 結尾，代表可以使用 markdown 語法編輯
    
#### 新增貼文
    
    - 如要新增網站貼文可以使用以下指令
    
    ```bash=
    hexo new post "貼文名稱"
    ```

- 產生出來後一樣會在 source 檔案夾底下
- 使用 markdown 語法編輯
    
### 本地網站上線
    
- 依序使用
    
    ```bash=
    hexo generate
    hexo server
    ```
    
- 也可以簡化成
    
    ```bash=
    hexo g
    hexo s
    ```
    
- 網站只要有更新都需要使用 hexo g
- hexo s 是讓他運行起來
    
### 部屬到 Github Pages

- 確保相關設定都正確的話就可以來部屬上雲啦 !
    
- 在部屬前請先確保有 Github 帳號
    
- 創立一個新的 repositories 如果要使用 Github 的 domain 請將 repositories 的名字取為
    ```<你的 GitHub 名字>.github.io```
    
- 將檔案上傳完畢 (可以使用 git)
    
- 在你的 repositories 找到 Settings > Pages > Source 將他改成使用 GitHub Actions

- 也可以順便在底下的 Custom domain 綁定自己的 domain
    
- 接著到 Action 新增一個 pages.yml 並貼上
    
```=
name: Pages

on:
  push:
    branches:
      - main # default branch

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          # If your repository depends on submodule, please see: https://github.com/actions/checkout
          submodules: recursive
      - name: Use Node.js <你的 nodejs 版本>
        uses: actions/setup-node@v4
        with:
          # Examples: 20, 18.19, >=16.20.2, lts/Iron, lts/Hydrogen, *, latest, current, node
          # Ref: https://github.com/actions/setup-node#supported-version-syntax
          node-version: "20"
      - name: Cache NPM dependencies
        uses: actions/cache@v4
        with:
          path: node_modules
          key: ${{ runner.OS }}-npm-cache
          restore-keys: |
            ${{ runner.OS }}-npm-cache
      - name: Install Dependencies
        run: npm install
      - name: Build
        run: npm run build
      - name: Upload Pages artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public
  deploy:
    needs: build
    permissions:
      pages: write
      id-token: write
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```
    
- 注意第 17 行的 <你的 nodejs 版本>，請將他替換
    
- 可以使用 node --version 查看
    
- 完成後等待 Github Action 部屬完成就可以訪問了 !
    
> 下一篇會教怎麼更換成自己喜歡的主題還有新增 RSS