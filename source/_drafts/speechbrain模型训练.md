---
abbrlink: ''
categories: []
date: '2023-12-05T16:16:16.553675+08:00'
tags: []
title: speechbrain模型训练
updated: 2023-12-5T16:43:17.453+8:0
---
speechbrain官网[SpeechBrain Basics](https://speechbrain.github.io/tutorial_basics.html)

speechbrain语音识别训练步骤[ASRfromScratch.ipynb - 联合实验室 (google.com)](https://colab.research.google.com/drive/1aFgzrUv3udM_gNJNUoLaHIm78QHtxdIz?usp=sharing#scrollTo=beagAGw5t5bK)

具体的原理请看官方文档，我只介绍步骤和可能遇到的问题

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

## 可能会出现的问题


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

### 报错说XXX not found

直接输入`pip install xxx`把包下载下来就行

### 报错pip install UnicodeDecodeError: ‘gbk‘ codec can‘t decode byte

输入`set PYTHONUTF8=1`

再pip安装

# 准备数据

模型训练需要一个数据清单文件告诉speechbrain语音文件在哪，需要一个脚本自动把所有数据统计出来

直接下载官方的准备文件[mini\_librispeech\_prepare.py](https://github.com/speechbrain/speechbrain/blob/develop/templates/speech_recognition/mini_librispeech_prepare.py)，后面运行模型的时候会自动运行这个文件里的函数，不用干什么


# 训练分词器

输入`cd templates/speech_recognition/Tokenizer`，进入分词器所在文件夹

输入`python train.py tokenizer.yaml`开始训练

## 可能会出现的问题

### mini_librispeech_prepare.py的问题

![https://s2.loli.net/2023/12/05/9vTOCpEagsR5Bir.png](https://s2.loli.net/2023/12/05/9vTOCpEagsR5Bir.png)

原本的文件夹中有mini_librispeech_prepare.py，但是里面只有一句`../mini_librispeech_prepare.py`，本意是想让程序运行上一个目录中的mini_librispeech_prepare.py，但是运行不了，我的解决办法就是把从官方下载的文件覆盖掉这个文件


### soundfile 和 sox问题

在对音频文件进行操作的时候，windows需要soundfile，linux需要sox

`pip install soundfile`下载soundfile


### 数据文件问题

开始训练后，会先下载数据集文件

![https://s2.loli.net/2023/12/05/NzfLivK9uCgUhRn.png](https://s2.loli.net/2023/12/05/NzfLivK9uCgUhRn.png)

但是这个文件是从huggingface下载的，而huggingface从23年5月开始把国内墙了，国内下载不了模型

如果挂VPN下载的话，会显示连接huggingface超时，虽然有时能练上去，看运气


只能手动下载

`http://www.openslr.org/resources/31/train-clean-5.tar.gz`

`http://www.openslr.org/resources/31/dev-clean-2.tar.gz`

`https://www.openslr.org/resources/12/test-clean.tar.gz`

数据集下载完后不要解压，在speech_recognition中新建data文件夹，把三个压缩包放进去

## 成功结果

成功是这样的

![https://s2.loli.net/2023/12/05/bEktiX81Th3jxRm.png](https://s2.loli.net/2023/12/05/bEktiX81Th3jxRm.png)

Tokenizer文件夹下会有save文件夹，保存有模型文件

# 训练语音模型

输入`pip install datasets`下载文件

在speech_recognition文件夹里有一个叫LM的文件夹，anaconda里进入这个文件夹

`cd ..`进入上一个文件夹speech_recognition

`cd LM`进入LM文件夹

或者使用绝对路径

输入`python train.py RNNLM.yaml`
