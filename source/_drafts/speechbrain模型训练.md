---
abbrlink: ''
categories: []
date: '2023-12-05T16:16:16.553675+08:00'
tags: []
title: speechbrain模型训练
updated: 2023-12-5T16:43:17.453+8:0
---
speechbrain官网[SpeechBrain Basics](https://speechbrain.github.io/tutorial_basics.html)

# 下载speechbrain

现在你要下载的地方创建一个文件夹speechbrain，然后打开anaconda prompt，输入

`cd /d 你的文件夹目录`进入文件目录

输入`activate speech`进入speech虚拟环境(准备阶段创建的虚拟环境)

输入`git clone https://github.com/speechbrain/speechbrain/`

就会自动下载speechbrain到目录下

输入`cd speechbrain`，进入speechbrain文件夹

依次输入

```
pip install -r requirements.txt
pip install -e .
```

## 会出现的问题

下载的时候会遇到各种各样的问题，下面是我遇到过的

### 问题：连接不到proxy网站

![https://s2.loli.net/2023/12/05/nKO16va3RzTrFD2.png](https://s2.loli.net/2023/12/05/nKO16va3RzTrFD2.png)

解决方法：把VPN关掉

### pip下载贼慢，甚至出现Read time out

方法一 ，在pip后面添加`-i https://pypi.tuna.tsinghua.edu.cn/simple`，使用清华的镜像库下载，只能解决一次安装

`pip install xxx -i https://pypi.tuna.tsinghua.edu.cn/simple`

方法二，替换下载源，一劳永逸

```
pip config set global.index-url https://pypi.douban.com/simple/  
pip config set install.trusted-host pypi.douban.com
```

### 报错说XXX model not found

直接输入`pip install xxx`把模型下载下来就行
