---
abbrlink: ''
categories:
- - 语音学习
date: '2023-12-07T20:54:46.164456+08:00'
tags:
- 语音学习
title: speechbrain说话人识别和语音增强
updated: 2023-12-8T20:51:9.695+8:0
---
有了之前的ASR的经验后，说话人识别和语音增强的模型训练就很简单了

# 说话人识别

cd进入\speechbrain\templates\speaker_id文件夹

输入`python train.py train.yaml`

然后你就会发现，它又要下一遍数据集😱 ，三个解决办法

## 数据集问题

### 复制data文件夹

把speech_recognition中的data文件夹复制到speaker_id下，其中

dev-clean-2.tar.gz、test-clean.tar.gz、train-clean-5.tar.gz是不需要的

因为这三个文件夹已经解压到data/LibriSpeech文件夹中

### 修改data路径

打开speaker_id/train.yaml

![https://s2.loli.net/2023/12/07/g5GfJCMR3jD9PX2.png](https://s2.loli.net/2023/12/07/g5GfJCMR3jD9PX2.png)

./data的意思就是当前路径下的data文件夹，修改这个路径

使用相对路径` ../speech_recognition/data`

使用绝对路径 `D:/Speech/newspeechbrain/speechbrain/templates/speech_recognition/data`这是我的绝对路径，你改为自己的

### 添加命令

在`python train.py train.yaml`后面添加`--data_folder=/path/of/your/data`

=号后面是data的路径

## 模型使用

在speaker_id文件夹下新建test.py文件，输入

```
import torchaudio
from speechbrain.pretrained import EncoderClassifier
classifier = EncoderClassifier.from_hparams(source="speechbrain/spkrec-xvect-voxceleb")
signal, fs =torchaudio.load('/content/speechbrain/tests/samples/single-mic/example1.wav')

# Compute speaker embeddings
embeddings = classifier.encode_batch(signal)

# Perform classification
output_probs, score, index, text_lab = classifier.classify_batch(signal)

# Posterior log probabilities
print(output_probs)

# Score (i.e, max log posteriors)
print(score)

# Index of the predicted speaker
print(index)

# Text label of the predicted speaker
print(text_lab)
```

torchaudio.load的路径需要修改为自己的路径，example.wav是已有的文件，不需要自己录

运行结果如下

![https://s2.loli.net/2023/12/07/KMuiONrRHSLUYZV.png](https://s2.loli.net/2023/12/07/KMuiONrRHSLUYZV.png)

# 语音增强

cd进入\speechbrain\templates\enhancement文件夹

输入`python train.py train.yaml`

其他同说话人识别相同

## 模型使用

### 失败操作(但有参考价值)

官方教程里没有给语音增强模型的示例

根据前面语音识别和说话人识别的示例代码，发现都引用了`speechbrain.pretrained`库

在搜索引擎搜索`speechbrain.pretrained`可以找到speechbrain的库函数，其实speechbrain官网也可以找到，点击官网上方[DOCUMENTATION](https://speechbrain.readthedocs.io/en/latest/index.html)也可以进来

[speechbrain.pretrained.interfaces module — SpeechBrain 0.5.0 documentation](https://speechbrain.readthedocs.io/en/latest/API/speechbrain.pretrained.interfaces.html)

![https://s2.loli.net/2023/12/08/e2mhMFbHtgoZu7T.png](https://s2.loli.net/2023/12/08/e2mhMFbHtgoZu7T.png)

SpectralMaskEnhancement即为语音增强的库

点击SpectralMaskEnhancement即可跳转到对应位置，找到example

```
import torch
from speechbrain.pretrained import SpectralMaskEnhancement
# Model is downloaded from the speechbrain HuggingFace repo
tmpdir = getfixture("tmpdir")
enhancer = SpectralMaskEnhancement.from_hparams(
    source="speechbrain/metricgan-plus-voicebank",
    savedir=tmpdir,
)
enhanced = enhancer.enhance_file(
    "speechbrain/metricgan-plus-voicebank/example.wav"
)
```

看了下enhance_file的介绍

![https://s2.loli.net/2023/12/08/dh9nPeGBSFyaJuX.png](https://s2.loli.net/2023/12/08/dh9nPeGBSFyaJuX.png)

修改filename为自己录制的文件，运行后什么也没发生，语音也没去噪，以为是没写输出位置的问题

添加output_filename，结果直接报错

Error opening 'D:/ASR_test.flac': Format not recognised

无论是wav格式、flac格式还是文件夹路径都不行

### 成功操作

在example中发现

```
source="speechbrain/metricgan-plus-voicebank"
```

搜索metricgan-plus-voicebank模型，在hugging face中找到了

[speechbrain/metricgan-plus-voicebank · Hugging Face](https://huggingface.co/speechbrain/metricgan-plus-voicebank)

下面有该模型的使用示例

```
import torch
import torchaudio
from speechbrain.pretrained import SpectralMaskEnhancement

enhance_model = SpectralMaskEnhancement.from_hparams(
    source="speechbrain/metricgan-plus-voicebank",
    savedir="pretrained_models/metricgan-plus-voicebank",
)

# Load and add fake batch dimension
noisy = enhance_model.load_audio(
    "speechbrain/metricgan-plus-voicebank/example.wav"
).unsqueeze(0)

# Add relative length tensor
enhanced = enhance_model.enhance_batch(noisy, lengths=torch.tensor([1.]))

# Saving enhanced signal on disk
torchaudio.save('enhanced.wav', enhanced.cpu(), 16000)
```

load_audio的参数为语音文件的路径，torchaudio.save中修改去噪后的语音的路径

我们先来合成一个带噪声的语音，也可以选择去外面直接录

使用au，录制一段自己的语音，之前录过可以跳过

![https://s2.loli.net/2023/12/08/Hc7TenjopyNLqxb.png](https://s2.loli.net/2023/12/08/Hc7TenjopyNLqxb.png)

在白噪声网站下载噪声，我用的是[Free 白噪音 Sound Effects Download - Pixabay](https://pixabay.com/zh/sound-effects/search/%E7%99%BD%E5%99%AA%E9%9F%B3/)

不建议在搜索引擎直接搜白噪声，会找到很多国内的网站，基本上都要收费

au新建多轨混音项目，把语音文件和噪声文件拖进去

![https://s2.loli.net/2023/12/08/bWEZPirN9Q4k5tV.png](https://s2.loli.net/2023/12/08/bWEZPirN9Q4k5tV.png)

在左边调节语音和噪声音量的大小，进度条拖动到语音结束部分，右键噪声部分，选择拆分，之后把后面多余的部分删掉，导出为wav文件即可，如下

![https://s2.loli.net/2023/12/08/IkUVNSy2FK1mqb9.png](https://s2.loli.net/2023/12/08/IkUVNSy2FK1mqb9.png)

运行语音增强，得到去噪声后的语音

![https://s2.loli.net/2023/12/08/d8K5qxOF7gvbrLZ.png](https://s2.loli.net/2023/12/08/d8K5qxOF7gvbrLZ.png)

高频噪声去除了，但是低频的噪声还有很多，语音质量也有点下降
