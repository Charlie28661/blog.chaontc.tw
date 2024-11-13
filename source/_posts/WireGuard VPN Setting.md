---
title: WireGuard VPN Setting
date: 2024-11-6
tags: Network
---

## WireGuard VPN Setting

* 這篇文章將教大家如何快速設定 WireGuard VPN
* 這篇文章範例使用 Windows (Server) == MacOS (Client)
* 但其實看懂了大同小異，可以輕鬆在 Windows & Linux 上切換

### 安裝 WireGuard
* 安裝的話這裡就簡單帶過
* 到 [這裡](https://www.wireguard.com/install/) 選自己的操作系統就好

### 配置檔
* 這裡可以參考我的配置，將公私鑰填入即可
* 這裡注意一下
* 一定要確保自己伺服器處在的網段沒有使用192.168.100.X
* 如果撞到了換一個沒在用的即可

* Server`s Settings
    ```
    [Interface]
    PrivateKey = <Server`s PrivateKey>
    ListenPort = 51820
    Address = 192.168.100.1/24

    [Peer]
    PublicKey = <Client`s PublicKey>
    AllowedIPs = 192.168.100.2/32
    ```

* Client`s Settings
    ```
    [Interface]
    PrivateKey = <Client`s PrivateKey>
    ListenPort = 51820
    Address = 192.168.100.2/24
    DNS = 8.8.8.8, 1.1.1.1

    [Peer]
    PublicKey = <Server`s PublicKey>
    AllowedIPs = 0.0.0.0/0
    Endpoints = <Server`s Public IP>:51820
    ```
    
* 如果想要限制哪些IP可以連線，請更改 ```AllowedIPs```

### Port Forwarding

* 這個簡單說就是登入你的路由器 (請確保有連上外網)
* 然後找到一個叫 ```虛擬伺服器``` 的選項
* 新增一筆紀錄並填入
    * 通訊協定:  BOTH
    * 外部通訊埠: 51820
    * 內部通訊埠: 51820
    * 本地 IP 位址: <Your Server`s IP
    
    
### Windows 網卡共用

* 如果要將 Windows 當作 Server 在看這裡
* 控制台 > 網路和網際網路 > 網路連線

![image](https://hackmd.io/_uploads/rktqf4GMkx.png)

* 點選乙太網路

![image](https://hackmd.io/_uploads/BkNCfVMG1g.png)

* 點選內容

![image](https://hackmd.io/_uploads/rJSlXVff1e.png)

* 點選共用並將其打勾
* 選擇 WireGuard 設置的那個介面名稱 (需要先將 WireGuard 連線才會跑出來)

![image](https://hackmd.io/_uploads/ryfB7EMzyl.png)

* 到這裡就設定完畢了

### 測試連線

* Windows (Server)
![image](https://hackmd.io/_uploads/rJZQEEMG1e.png)

* MacOS (Client)
![image](https://hackmd.io/_uploads/Sk5u4EGM1l.png)