---
title: 滲透測試工具 - Feroxbuster
date: 2025-7-4
tags: PT
---


## Installation

- On Linux(kali)
```
curl -sL https://raw.githubusercontent.com/epi052/feroxbuster/main/install-nix.sh | bash -s $HOME/.local/bin
```


## Example

### Preliminary

```
cd ~/.local/bin
```

```
./feroxbuster
```

### Attack

- 基本手法
```
feroxbuster -u <target> -w <wordlist>
```

- 加入副檔名
```
feroxbuster -u <target> -w <wordlist> -x php,html,txt
```

- 限制深度
```
feroxbuster -u <target> -w <wordlist> -d <depth>
```