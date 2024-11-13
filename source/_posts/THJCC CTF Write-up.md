---
title: THJCC CTF write-up
date: 2024-05-05
tags: CTF
---

## Welcome

### Welcome 0x1

* 題目敘述可以找到 1/2 flag
* 另一半可以在 Rule 找到

```
THJCC{5cINt_sC4icT_5C1sT}
```

### Discord 0x1

* flag 都在這隻官方 bot 上

![image](https://hackmd.io/_uploads/B15uvhibC.png)

* 上圖可找到第一片段和第三片段
* 第二片段是透過 / 指令觸發的

![image](https://hackmd.io/_uploads/HJZZ_2iWA.png)

```
THJCC{r3meMB3R!JO1Ndi5c0rD_5eRv3r}
```


## Misc

### 原神帳號外流

* 這題給了一個 .pcapng 其實就是 Wireshark 的檔案

![image](https://hackmd.io/_uploads/BkVbY3iWC.png)

* 這份 Wireshark 檔案攔截到了我們登入原神帳號所需要的帳號密碼
* 我在上方的過濾器輸入 http.request.method=="POST"
* 這可以幫我過濾出所有使用 POST 發送的封包

![image](https://hackmd.io/_uploads/H1Sg92iWC.png)

* 286 這個封包就是我們要找的
* 使用裡面的帳號密碼登入平台就有 flag 了

```
THJCC{W3r3_sHarKKKKKK_MasT3R_C8763}
```

### 出題者大合照！

* 這題是 Steganography 圖片隱寫術
* 這題我使用 steghide 去找出藏起來的檔案

```
> steghide info chal.jpg 
"chal.jpg":
  format: jpeg
  capacity: 32.8 KB
Try to get information about embedded data ? (y/n) y
Enter passphrase: 
  embedded file "flag.txt":
    size: 37.0 Byte
    encrypted: rijndael-128, cbc
    compressed: yes
```

* 這張 chal.jpg 嵌入了一個 flag.txt
* 接著把 flag.txt 提取出來

```
> steghide extract -sf chal.jpg
Enter passphrase: 
wrote extracted data to "flag.txt".
```

* 使用 cat flag.txt 即可找到 flag

```
THJCC{S1TC0N_2o2A_a1l_hAnDs0m3_9uY5}
```

### PyJail-0

* server.py 如下
```python=
WELCOME='''
 ____            _       _ _ 
|  _ \ _   _    | | __ _(_) |
| |_) | | | |_  | |/ _` | | |
|  __/| |_| | |_| | (_| | | |
|_|    \__, |\___/ \__,_|_|_|
       |___/                 
'''

def main():
    
    print("-"*30)
    print(WELCOME)
    print("-"*30)
    print("Try to escape!!This is a jail")
    a=input("> ")
    eval(a)

if __name__ == '__main__':
    main()
```

* 第 17 行使用了危險的 eval() 函數
* 而且這個 eval() 函數接受使用者自行輸入字串
* 這是一題 command injection
* 到網上找 payload
* 我使用 `__import__('os').system(‘command’)`
* 使用 ls 列出目錄再 cat flag.txt

```
THJCC{Use_M2g1c_f2un3ti0n_in_P9Ja1l!!}
```


## Web

### Empty

* 進去後發現什麼都沒有
* 我們查看原始碼

![image](https://hackmd.io/_uploads/SkSq1psZR.png)

* 把上面給的 `RkxBRzE9VEhKQ0N7Y29va2llXyZf` 使用 base64.decode後得 `FLAG1=THJCC{cookie_&_`
* 接著前往 /Wh4leE4tSh4rk.html 即可看到第二段 flag

![image](https://hackmd.io/_uploads/HkZLx6sbR.png)

```
THJCC{cookie_&_view_source_!}
```

### Blog

* 這題其實就是眼睛睜大點
* 前往 /login 即可看到大大的 `Only admin is available` 可猜出來 username 使用 admin
* 密碼是首頁某個文章底下的字串

> username: admin
> password: iloveshark

![image](https://hackmd.io/_uploads/H1_Z-pjbC.png)

```
THJCC{w31c0me_h@cker}
```

### Simplify

* 這題我們以題目給的 `test:test1234` 登入後
* 發現 cookie 設置了一個 username: test

![image](https://hackmd.io/_uploads/S130-pob0.png)

* 將 test 修改成 admin 即可看到這個

![image](https://hackmd.io/_uploads/HJUzGpsWR.png)

* 作者要我們玩 flask 的 render SSTI
* url 的 @ 後是可控的

![image](https://hackmd.io/_uploads/B1zvMaibR.png)

* 到網上找 flask SSTI 的 payload
* 我使用 `{{ self.__init__.__globals__.__builtins__.__import__('os').popen('command').read() }}`
* 使用 ls 後 cat flag

```
THJCC{w3ak_auth_+_S$TI}
```


## Crypto

### 博元婦產科

* 將題目給的 `TUFDVlZ7cFBwLnU0VXJmVGQzay52MEYubVB9Cg==` 字串使用 base64.decode 得到 `MACVV{pPp.u4UrfTd3k.v0F.mP}`
* 同時，我們又知道 flag 格式應為 THJCC{*.}
* 可猜測是凱薩加密的偏移
* 偏移 7 得到 flag

```
THJCC{wWw.b4BymAk3r.c0M.tW}
```


## PWN

### nc

* nc 過去給了一個 youtube
* 輸入這個 youtube 的作者即可得到 flag

```
THJCC{N3veR_g0nn4_l37_You_dOwn!!!}
```


## Reverse

### Baby C

* BabyC.c 如下
```c=
#include"stdio.h"
#include"stdlib.h"
#include"string.h"
int main(){
    char c[50];
    int a[50]={44, 48, 50, 59, 59, 3, 16, 12, 12, 8, 11, 66, 87, 87, 15, 15, 15, 86, 1, 23, 13, 12, 13, 26, 29, 86, 27, 23, 21, 87, 15, 25, 12, 27, 16, 71, 14, 69, 75, 32, 59, 46, 53, 75, 63, 75, 8, 22, 11, 5};
    scanf("%s", c);
    for(int i=0;i<50;i++){
        if (((int)c[i]^120)!=a[i]){
            printf("Password Incorrect!!!\n");
            return 0;
        }
    }
    printf("Password Correct!!!\n");
    return 0;
}
```

* 將這個 a 陣列進行反向 xor 運算

```python=
a = [44, 48, 50, 59, 59, 3, 16, 12, 12, 8, 11, 66, 87, 87, 15, 15, 15, 86, 1, 23, 13, 12, 13, 26, 29, 86, 27, 23, 21, 87, 15, 25, 12, 27, 16, 71, 14, 69, 75, 32, 59, 46, 53, 75, 63, 75, 8, 22, 11, 5]
password = ""

for char_code in a:
    password += chr(char_code ^ 120)

print(password)
```

```
THJCC{https://www.youtube.com/watch?v=3XCVM3G3pns}
```


### PYC REVERSE

* 題目給了一個 .pyc 檔和一個 msg.txt
* .pyc 的部分我找了一個線上 [rev tool](https://tool.lu/pyc/)
* 上傳檔案得到 python 檔

```python=
from FLAG import FLAG
from Crypto.Util.number import bytes_to_long

def xor1(flag):
    return flag ^ 124789


def xor2(flag):
    return flag ^ 487531


def xor3(flag):
    return flag ^ 784523


def xor4(flag):
    return flag ^ 642871


def xor5(flag):
    return flag ^ 474745

flag = bytes_to_long(FLAG)
count = 0
count += 1
if count == 1:
    flag = xor1(flag)
    count += 2
    if count == 3:
        flag = xor2(flag)
        count += 1
    if count == 4:
        flag = xor3(flag)
        count -= 2
    else:
        flag = xor2(flag)
        count += 1
else:
    flag = xor3(flag)
    count += 5
if count == 2:
    flag = xor4(flag)
elif count == 6:
    flag = xor5(flag)
print(flag)
```

* 製作一個 python script 用於計算 flag

```python=
from Crypto.Util.number import long_to_bytes

def reverse_xor5(flag):
    return flag ^ 474745

def reverse_xor4(flag):
    return flag ^ 642871

def reverse_xor3(flag):
    return flag ^ 784523

def reverse_xor2(flag):
    return flag ^ 487531

def reverse_xor1(flag):
    return flag ^ 124789

flag = 10730390416708814647386325276467849806006354580175878786363505755256613965929606057246313695

count = 0
count += 1
if count == 1:
    flag = reverse_xor1(flag)
    count += 2
    if count == 3:
        flag = reverse_xor2(flag)
        count += 1
    if count == 4:
        flag = reverse_xor3(flag)
        count -= 2
    else:
        flag = reverse_xor2(flag)
        count += 1
else:
    flag = reverse_xor3(flag)
    count += 5
if count == 2:
    flag = reverse_xor4(flag)
elif count == 6:
    flag = reverse_xor5(flag)

# 將長整數轉換為字串
flag_string = long_to_bytes(flag).decode('utf-8')

print(flag_string)
```

```
THJCC{pyc_rev3r3e_C3n_u32_on1i5e_t0Ol}
```