---
title: Cloudflare WAF套用在Vultr VPS上
date: 2023-11-2
tags: Network
---

## 簡介

[Cloudflare](https://www.cloudflare.com/)
[Vultr](https://vultr.com/)

## 開始設定

首先來到 Vultr 的 Firewall管理介面

![image.png](https://hackmd.io/_uploads/rJ8amblm6.png)

這介面蠻直觀的，掃過去大致可以理解怎麼設定

* **Protocol**
    選擇你想要同意流量進入這個端口

    可設定的協定：
    * SSH
    * HTTP
    * HTTPS
    * MySQL
    * PostgreSQL
    * DNS (TCP)
    * DNS (UDP)
    * MS RDP
* **Port**
    因為 Protocol 都有預設段口，所以Port系統會自動改成預設
* **Source**
    來源

    可設定的來源：
    * MyIP - 自動獲取本機對外IP
    * Custom - 自訂IP 
    * Anywhere - 所有IP
    * Cloudflare - [Cloudflare節點IPs](https://www.cloudflare.com/zh-tw/ips/)
    * LoadBalancer - 負載平衡器

選好後最後按下 + 就完成設定了

但是

這個面板的 Action 只能設定 Accept

沒辦法設定 Deny


## 利用 Cloudflare 滿足進階需求
我們將 Source 設定成 Cloudflare

接著我們來到 Cloudflare 的控制台

點選你的 Domain 後左側找 DNS --> records

新增一個指向紀錄(A)，這個 Proxy 很重要 一定要開

![Screenshot 2023-11-02 at 1.22.47 AM.png](https://hackmd.io/_uploads/BkostbxQ6.png)

如果 Proxy 沒開使用者就會直連 Web Server 不會先到 Cloudflare 的點，這樣一來就不滿足[Cloudflare的節點IPs](https://www.cloudflare.com/zh-tw/ips/)，會被 Vultr drop掉

接下來我們可以來設定 Cloudflare WAF Rules 了
點選左側 Security --> WAF

![image.png](https://hackmd.io/_uploads/SkqxjZgXa.png)

按 Create Rule 可以進到這裡

![Screenshot 2023-11-02 at 1.29.41 AM.png](https://hackmd.io/_uploads/BysFiblQa.png)

1. 這條規則的名稱
2. 第一格是篩選條件
    * AS Number
    * Cookie
    * Country
    * Hostname
    * Source Address
    * ...
    
    第二格是條件
    * 等於
    * 不等於
    * ...

    第三格是值
    * 根據前面第一個選擇的填入適當參數
3. 當上述成立要執行什麼
    * 封鎖
    * 略過
    * 新增挑戰
    * ...

最後設定完畢記得按下 Deploy

## 提醒
若全部流量都要走 Cloudflare 的 WAF ， Vultr 那邊要記得把除了 Cloudflare 以外的 drop 掉