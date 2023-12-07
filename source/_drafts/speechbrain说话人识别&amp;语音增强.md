---
abbrlink: ''
categories:
- - 语音学习
date: '2023-12-07T20:54:46.164456+08:00'
tags:
- 语音学习
title: speechbrain说话人识别&语音增强
updated: 2023-12-7T21:18:35.34+8:0
---
有了之前的ASR的经验后，说话人识别和语音增强的模型训练就很简单了

# 说话人识别

cd进入\speechbrain\templates\speaker_id文件夹

输入`python train.py train.yaml`

然后你就会发现，它又要下一遍数据集😱 ，两个解决办法

## 复制data文件夹

把speech_recognition中的data文件夹复制到speaker_id下，其中

dev-clean-2.tar.gz、test-clean.tar.gz、train-clean-5.tar.gz是不需要的

因为这三个文件夹已经解压到data/LibriSpeech文件夹中

## 修改data路径

打开speaker_id/train.yaml

![https://s2.loli.net/2023/12/07/g5GfJCMR3jD9PX2.png](https://s2.loli.net/2023/12/07/g5GfJCMR3jD9PX2.png)

./data的意思就是当前路径下的data文件夹，修改这个路径

使用相对路径` ../speech_recognition/data`

使用绝对路径 `D:/Speech/newspeechbrain/speechbrain/templates/speech_recognition/data`这是我的绝对路径，你改为自己的


# 语音增强

cd进入\speechbrain\templates\enhancement文件夹

输入`python train.py train.yaml`

其他同说话人识别
