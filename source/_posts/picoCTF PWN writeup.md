---
title: picoCTF PWN writeup (持續更新)
date: 2024-11-13
tags: CTF
---


## 前言

* 最近心血來潮想碰碰 PWN 了，這裡會放我在學 PWN 時在 picoCTF 上解的 PWN 題
* 沒意外會持續更新
* 順帶一題，如果未來的我哪天有空就順便把其他領域的 writeup 補上 XD

## format string 0

[題目連結](https://play.picoctf.org/practice/challenge/433?category=6&page=1) https://play.picoctf.org/practice/challenge/433?category=6&page=1

![image](https://hackmd.io/_uploads/HJ9dI7zzyl.png)

* Launch 後我先連上去看看

![image](https://hackmd.io/_uploads/ry91Pmzzkg.png)

* 接下來，我將題目檔案載下來並開啟觀察

```c=
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <signal.h>
#include <unistd.h>
#include <sys/types.h>

#define BUFSIZE 32
#define FLAGSIZE 64

char flag[FLAGSIZE];

void sigsegv_handler(int sig) {
    printf("\n%s\n", flag);
    fflush(stdout);
    exit(1);
}

int on_menu(char *burger, char *menu[], int count) {
    for (int i = 0; i < count; i++) {
        if (strcmp(burger, menu[i]) == 0)
            return 1;
    }
    return 0;
}

void serve_patrick();

void serve_bob();


int main(int argc, char **argv){
    FILE *f = fopen("flag.txt", "r");
    if (f == NULL) {
        printf("%s %s", "Please create 'flag.txt' in this directory with your",
                        "own debugging flag.\n");
        exit(0);
    }

    fgets(flag, FLAGSIZE, f);
    signal(SIGSEGV, sigsegv_handler);

    gid_t gid = getegid();
    setresgid(gid, gid, gid);

    serve_patrick();
  
    return 0;
}

void serve_patrick() {
    printf("%s %s\n%s\n%s %s\n%s",
            "Welcome to our newly-opened burger place Pico 'n Patty!",
            "Can you help the picky customers find their favorite burger?",
            "Here comes the first customer Patrick who wants a giant bite.",
            "Please choose from the following burgers:",
            "Breakf@st_Burger, Gr%114d_Cheese, Bac0n_D3luxe",
            "Enter your recommendation: ");
    fflush(stdout);

    char choice1[BUFSIZE];
    scanf("%s", choice1);
    char *menu1[3] = {"Breakf@st_Burger", "Gr%114d_Cheese", "Bac0n_D3luxe"};
    if (!on_menu(choice1, menu1, 3)) {
        printf("%s", "There is no such burger yet!\n");
        fflush(stdout);
    } else {
        int count = printf(choice1);
        if (count > 2 * BUFSIZE) {
            serve_bob();
        } else {
            printf("%s\n%s\n",
                    "Patrick is still hungry!",
                    "Try to serve him something of larger size!");
            fflush(stdout);
        }
    }
}

void serve_bob() {
    printf("\n%s %s\n%s %s\n%s %s\n%s",
            "Good job! Patrick is happy!",
            "Now can you serve the second customer?",
            "Sponge Bob wants something outrageous that would break the shop",
            "(better be served quick before the shop owner kicks you out!)",
            "Please choose from the following burgers:",
            "Pe%to_Portobello, $outhwest_Burger, Cla%sic_Che%s%steak",
            "Enter your recommendation: ");
    fflush(stdout);

    char choice2[BUFSIZE];
    scanf("%s", choice2);
    char *menu2[3] = {"Pe%to_Portobello", "$outhwest_Burger", "Cla%sic_Che%s%steak"};
    if (!on_menu(choice2, menu2, 3)) {
        printf("%s", "There is no such burger yet!\n");
        fflush(stdout);
    } else {
        printf(choice2);
        fflush(stdout);
    }
}
```

* 大致能分析出以下資訊
    1. 有兩道題目，func 分別在 51 跟 80 行的位置
    2. 每題都有數種食物給我們選擇
    3. 判斷式使用的 BUFSIZE 為 32

由於這段的限制
```c=
if (!on_menu(choice1, menu1, 3)) {
        printf("%s", "There is no such burger yet!\n");
        fflush(stdout);
```

* 我們無法使用這三種 ```"Breakf@st_Burger", "Gr%114d_Cheese", "Bac0n_D3luxe"``` 以外的東西
* 而這裡使用 ```Gr%114d_Cheese``` 即可
* 但為什麼呢?
* 我們回到這題的 func 裡找到進入下一題的判斷式
```c= 
if (count > 2 * BUFSIZE) {
            serve_bob();
```
* ```count``` 是我們輸入的東西
* ```BUFSIZE``` 前面說是 32 而 2 * 32 是 64
* 所以裡判斷就是說我們輸入的字串長度有沒有大於 64
* 我們先來算算這三種食物的字串長度各為多少

![image](https://hackmd.io/_uploads/Hyt0FQMfyl.png)

* 看起來並沒有一個大於 64
* 既然沒有一個大於 64 為何還可以過?
* 其實是因為 Gr%114d_Cheese 的 ```%114d```
* ```%114d``` 會將輸出填充至 114 個字元
* 所以在進入判斷式就可以過了
* 接下來進入第二題

![image](https://hackmd.io/_uploads/Byza57zz1x.png)

* 這次一樣給出三個選項 ```"Pe%to_Portobello", "$outhwest_Burger", "Cla%sic_Che%s%steak"```
* 這裡使用 ```Cla%sic_Che%s%steak```  即可得到 flag
* 原因是因為
* ```Cla%sic_Che%s%steak``` 裡面的 %s
* %s 需要一個傳入的 strings
* 若缺少這個參數會導致程序崩潰
* Pwned!

![image](https://hackmd.io/_uploads/Hyny3QMM1x.png)

flag : ```picoCTF{7h3_cu570m3r_15_n3v3r_SEGFAULT_ef312157}```
