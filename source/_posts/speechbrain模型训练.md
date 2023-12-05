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

国外的网站直接下载好像是下不了的，挂VPN的话有时会显示连接超时

如果能下载的话就直接下

如果不能下载，那就只能手动下载

`http://www.openslr.org/resources/31/train-clean-5.tar.gz`

`http://www.openslr.org/resources/31/dev-clean-2.tar.gz`

`https://www.openslr.org/resources/12/test-clean.tar.gz`

数据集下载完后不要解压，在speech_recognition中新建data文件夹，把三个压缩包放进去

## 成功结果

成功是这样的

![https://s2.loli.net/2023/12/05/bEktiX81Th3jxRm.png](https://s2.loli.net/2023/12/05/bEktiX81Th3jxRm.png)

Tokenizer文件夹下会有save文件夹，保存有模型文件

# 训练语言模型

输入`pip install datasets`下载文件

在speech_recognition文件夹里有一个叫LM的文件夹，anaconda里进入这个文件夹

`cd ..`进入上一个文件夹speech_recognition

`cd LM`进入LM文件夹

或者使用绝对路径

输入`python train.py RNNLM.yaml`

## 成功结果

![https://s2.loli.net/2023/12/05/KlHTfpSP6ygWxwa.png](https://s2.loli.net/2023/12/05/KlHTfpSP6ygWxwa.png)

LM文件夹下会有result文件夹，保存有模型文件

# 训练ASR模型

和分词器一样，要从openslr下载数据文件

`http://www.openslr.org/resources/28/rirs_noises.zip`，放在data文件夹下

还要再从huggingface下载模型文件，而huggingface从23年5月开始把国内墙了，国内正常下载不了模型

还是一样，挂了VPN后网络好能直接下载就直接下

自动下载不了再手动下载，下红框的三个，点击文件名右侧的下载图标下载，直接点击文件名的话会进入文件的详细参数，后面有说到

[speechbrain/asr-crdnn-rnnlm-librispeech at main (huggingface.co)](https://huggingface.co/speechbrain/asr-crdnn-rnnlm-librispeech/tree/main)

![https://s2.loli.net/2023/12/05/iA4vDtBqYl2o5Fu.png](https://s2.loli.net/2023/12/05/iA4vDtBqYl2o5Fu.png)

放在C:\Users\wdsha\ .cache\huggingface\hub\models--speechbrain--asr-crdnn-rnnlm-librispeech\blobs

其中wdsha是我自己的用户名，你选择你自己的

直接复制过去是不能用的，还要

asr.ckpt名字改为1e795c7e18f3bab6bd5f47060ab852233deb33d7d550e989994c8683901e18d5

lm.ckpt名字改为

3f73e243f5f0eb070a05a2069ba5b9014232e926384cc7d5ba24cde060c84997

tokenizer.ckpt名字改为

37a6cba34cd520b33fd83612d5efc8ba7e351166541eb2726642bb3032234d31

只能说hugging face在文件命名上有一手的，这些名字是文件在hugging face上的编号，如下图是lm.ckpt的

![https://s2.loli.net/2023/12/05/9v16k3YaRVOhIZf.png](https://s2.loli.net/2023/12/05/9v16k3YaRVOhIZf.png)


cd进入speech_recognition/ASR

输入`python train.py train.yaml`即可进行训练

![https://s2.loli.net/2023/12/05/AOz3C7tBh4Ylpdi.png](https://s2.loli.net/2023/12/05/AOz3C7tBh4Ylpdi.png)

下面的进度条就是训练进度，进度条上的epoch 2表示现在进行到第二轮训练，具体训练的参数在train.yaml里，打开后可以修改参数


## 可能遇到的问题

### 爆显存

不是每个人都拥有GTX4090，肯定会出现性能不够的情况，这个模型初始的batch_size(每次训练多少个数据)为8，这个越大，跑得越快，但是吃显卡性能

epoch(训练轮数)为15，轮数越大跑得越久

打开train.yaml，里面有所有的参数，ctrl+F可迅速查找想修改的参数的位置

![https://s2.loli.net/2023/12/05/HoYU3QyCt45Nrgc.png](https://s2.loli.net/2023/12/05/HoYU3QyCt45Nrgc.png)

我的显卡是GTX1050，而且还是笔记本版，算力很垃圾，batch_size=8时跑到30%显存就爆了，batch_size=4时跑到70%爆了，后面改成batch_size=2才能正常跑，而且跑一轮要4个小时，epoch=15跑太久了，后面改成了epoch=5，还是跑了两天才跑完，人都等晕了


## 成功结果

在speech_recognition下建立一个test.py文件，文件内容为

```
from speechbrain.pretrained import EncoderDecoderASR

asr_model = EncoderDecoderASR.from_hparams(source="speechbrain/asr-crdnn-rnnlm-librispeech", savedir="/content/pretrained_model")
audio_file = 'D:/App data/Audition/ASR_test.wav'
transcribe_result = asr_model.transcribe_file(audio_file)
print({transcribe_result})
```

其中，audio_file的路径是录音的路径，你自己用au或者其他软件录一个wav文件就行

注意，这个模型是英文模型，你要说英文

我录的话是 “speech brain can already do a lot of cool things”

python test.py运行结果如下

![https://s2.loli.net/2023/12/05/8QPihFVJubdTpzk.png](https://s2.loli.net/2023/12/05/8QPihFVJubdTpzk.png)

正确率竟然达到了惊人的33%，我的天哪，只能说是拉的一逼，因为官方说如果要达到可用程度，需要100~1000小时的数据，而我们下载的数据只有几个小时，所以性能不够



# 结语

speech brain的ASR就结束了，搞这个东西搞了差不多一个星期，主要是下载和训练一直要等，碰到问题网上的解决方案也不一定对，有些都过时了，比如pytorch和CUDA toolkit那里，最后也算做出来了吧

吐槽：windows 的屏幕截图竟然不会保存图片，只是把图片自动复制到剪贴板，搞得我少了很多图片，又不想再做一遍，可能有些会出现的问题没提到，各位评论区说一下吧
