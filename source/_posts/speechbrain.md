---
abbrlink: ''
categories:
- - 语音学习
date: '2023-12-04T18:00:35.479014+08:00'
tags:
- 语音学习
title: speechbrain准备工作
updated: 2023-12-4T22:11:58.992+8:0
---
# 介绍

speechbrain是一个不久前才开源的语音项目，包括语音识别、说话人识别、感情识别等，speechbrain是想把语音的功能全都做进去的，听说比kladi操作简单些

# 前期准备

**目前只有N卡（GTX、RTX系列）才能跑这个模型，因为只有N卡支持CUDA，A卡的话就可以放弃不用看了**

如果以前从来没有训练过模型或者用python写过接口啥的，需要先安装python环境，不建议直接安装python，最好通过anaconda来安装python环境

以前从来没有用过github的人，要先安装git

## anaconda的安装

anaconda的官网：[Anaconda | The World’s Most Popular Data Science Platform](https://www.anaconda.com/)

装个软件很简单的，想看傻瓜式的可以看知乎文章[超详细！Python神器Anaconda图文安装教程 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/635869956)

装完后是这个样子的

![https://s2.loli.net/2023/12/04/OxHlvpQ5PEVd694.png](https://s2.loli.net/2023/12/04/OxHlvpQ5PEVd694.png)

## anaconda安装及python环境的配置

打开anaconda prompt

![https://s2.loli.net/2023/12/04/BX71mIVuthkOTG9.png](https://s2.loli.net/2023/12/04/BX71mIVuthkOTG9.png)

输入`conda create -n speechbrain python=3.8.5 ` 后回车

其中'speechbrain'可以改为你想要设置的虚拟环境的名字，python=3.8.5是指python版本为3.8.5，如需下载其他版本，把3.8.5改成版本号即可

会下载一堆东西，然后它会问你
![https://s2.loli.net/2023/12/04/rGKobwzO1FcIlRL.png](https://s2.loli.net/2023/12/04/rGKobwzO1FcIlRL.png)

无脑输入y回车就对了

完成后输入 `activate speechbrain`就可以进入名字叫speechbrain的虚拟环境中了

![https://s2.loli.net/2023/12/04/CSu4QcX7eMR1hOf.png](https://s2.loli.net/2023/12/04/CSu4QcX7eMR1hOf.png)

其他指令

```
conda deactivate  退出当前环境

conda env list    列出所有的环境，因为以后跑不同的模型可能需要不同的环境，所以要有多个环境

conda env remove --name speech   把名为speech的环境删除
```

## 安装pytorch和CUDA toolkit

一般N卡（GTX、RTX系列）是自带CUDA的，因此不需要下载CUDA

而且现在CUDA toolkit以及集成在pytorch里了，下载pytorch是会自动安装好toolkit

在进行这一步之前，建议先将显卡驱动升级成最新的，以免出现驱动和CUDA版本问题

输入`nvidia-smi`查看当前CUDA版本

![https://s2.loli.net/2023/12/04/CtzjB2v48HrWLkZ.png](https://s2.loli.net/2023/12/04/CtzjB2v48HrWLkZ.png)

可以看到我的版本是12.3


进入pytorch下载页面[Start Locally | PyTorch](https://pytorch.org/get-started/locally/)

![https://s2.loli.net/2023/12/04/sYrkjDV5Eeuc4Pp.png](https://s2.loli.net/2023/12/04/sYrkjDV5Eeuc4Pp.png)

pytorch这里选择的CUDA版本一定要比自己电脑的CUDA版本低，我的电脑版本是12.3，因此选择了12.1，如果你的版本较低，就选11.8，或者可以先试试更新驱动，如果CUDA版本比11.8都要低的话，自行百度一下吧

把下面的Run this command复制到anaconda里运行即可，这样pytorch就安装完了


## git安装

git的安装很长，我老早之前就装好了，不记得怎么装的了，自己去看知乎吧[Git下载安装教程：git安装步骤手把手图文【超详细】 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/443527549)

装完后是这样的

![https://s2.loli.net/2023/12/04/AedMOcJQDFmjuz5.png](https://s2.loli.net/2023/12/04/AedMOcJQDFmjuz5.png)

鼠标右键一个文件夹的空地方，会有这两项

![https://s2.loli.net/2023/12/04/g1LpM7IBtDwvyJ9.png](https://s2.loli.net/2023/12/04/g1LpM7IBtDwvyJ9.png)
