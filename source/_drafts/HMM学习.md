---
abbrlink: ''
categories: []
date: '2023-12-03T16:45:57.524274+08:00'
tags: []
title: HMM（隐性马尔可夫模型）学习
updated: 2023-12-3T23:34:18.654+8:0
---
HMM隐性马尔可夫模型

和以前学过的状态机有点像

# **1.抽象理解HMM**

用李琳山教授的例子来抽象一个HMM：假设现在面前有三个盒子(A,B,C)，每个盒子里有不同分布的红、蓝、绿球，现在我们背过身去，不看盒子，由另一个人从这些盒子中随机抽取n个球，于是我们得到了一个序列：红、蓝、蓝、红、绿、红、蓝......

这个过程中，我们只知道拿到了哪些球，而不知道另一个人是从哪个盒子里拿出的哪个球，这叫做隐性

假设拿球人第一次是从盒子A中拿球，拿完一个球后，还在A盒拿球的概率为qaa，去B盒拿第二个球的概率为qab、去C盒拿第二个球的概率为qac，以此类推，得到马尔科夫链(借用了李琳山老师的图，图里用了准确的概率，qaa = 0.6，qab=0.3，qac=0.1，以此类推)：

![https://s2.loli.net/2023/12/03/kpyqUAZs7mLGISv.png](https://s2.loli.net/2023/12/03/kpyqUAZs7mLGISv.png)

若在A盒拿到红、绿、蓝球的几率分别为qa1、qa2、qa3，B盒为qb1、qb2、qb3，C盒为qc1、qc2、qc3，就意味着我们可以计算出所有的概率，推断出哪个球来自于哪个盒子的概率最高，最终取概率最高的为取盒子的顺序

# **2.具体定义**

对于HMM模型，首先我们假设I是所有可能的隐藏状态的集合，O是所有可能的观测状态的集合

![](https://pic4.zhimg.com/80/v2-75f8776c6c70e3b775ceacaa3b865fb3_720w.webp)

对于我们之前的例子来说，盒子的顺序就是隐藏状态集合，球的顺序就是观测状态的集合

为了简化计算，做出两个假设：

```
1.每一个隐藏状态只依赖于上一个隐藏状态，即你抽哪个盒子只由上一次抽的盒子决定

2.观测状态只由当前的隐藏状态决定，即抽球时只从当前盒子里抽，不受其他盒子影响
```

因此我们得到状态转移矩阵(以之前的例子，决定下一个盒子是哪个)：

![https://s2.loli.net/2023/12/03/VI8PtKFrqUxSdX7.png](https://s2.loli.net/2023/12/03/VI8PtKFrqUxSdX7.png)

观测状态概率矩阵(决定抽中哪个球)：

![https://s2.loli.net/2023/12/03/9rfTZ2nEW8aXQYb.png](https://s2.loli.net/2023/12/03/9rfTZ2nEW8aXQYb.png)

再增加一个序列长度T，一个初始状态Π = (q1，q2，q3)表示第一次抽中哪个盒子，我们就得到了一个马尔可夫模型

具体推算过程：

1.首先由初始状态得到隐藏状态i1，即第一个盒子是哪个

2.由隐藏状态i1得到观测状态o1，即第一个球是哪个

3.由隐藏状态i1得到隐藏状态i2，即第二个盒子是哪个

4.重复2~3步，直到得到T个观测状态o，所有o组成观测序列Q，即所有拿出来的球

# **3.HMM的三个问题**

评估观察序列概率：已知模型λ=(A,B,π)和观测序列Q，求观测序列出现的概率

预测问题(解码问题)：已知模型λ=(A,B,π)和观测序列Q，求可能的隐藏序列

模型参数学习问题：已知观测序列Q，求模型的参数

## 3.1 评估问题

### 3.1.1. 暴力解法

最简单的，暴力解法，就和上面例子的一样，一路推过去就能把概率全部算出来，但是对于数据很多的时候就要算很久

### 3.1.2.前向算法(Forward Algorithm)

定义在模型λ=(A,B,π)条件下，t时刻时隐藏状态为 qt = i ，观测状态的序列为 o1,o2,...,ot的概率为前向概率。记为：

![https://s2.loli.net/2023/12/04/8sLoyzpP9trCc73.png](https://s2.loli.net/2023/12/04/8sLoyzpP9trCc73.png)

若我们知道t时刻所有隐藏状态q的前向概率，则可以推出t+1时刻的前向概率：

![https://s2.loli.net/2023/12/03/9OsMIuygtE1Qwcx.png](https://s2.loli.net/2023/12/03/9OsMIuygtE1Qwcx.png)


aij为P(qt+1= j | qt = i)，式子中at(i)*aij 后得到P(o1，o2...ot，qt+1 = j | λ，qt = i)

求和符号就是把所有i的可能性相加，就可以得到t+1时刻为qj的概率，P(o1，o2...ot，qt+1 = j | λ)，目前只确定了隐藏序列，对比前向概率公式，发现观测序列只到了ot，要符合前向概率公式，应把观测序列推到ot+1

观测序列已经确定，因此再乘以bj(ot+1)，即乘上 隐藏序列为qj时，观测序列为ot+1的概率，即可得到P(o1，o2...ot+1，qt+1 = j | λ)，即t+1时刻的前向概率at+1(j)

既然可以有t时刻推导出t+1时刻的一个隐藏状态的前向概率，那么整个t+1时刻所有j的可能的前向概率都可以推出来，那么又可以接着推t+2时刻，直到完成整个序列T，那么初始的前向概率怎么得呢？由于模型已知，则初始状态π已知，而初始状态π乘以b1(o1)就是最初的前向概率，由初始状态递推即可，最终∑aT(i)即为P(O|λ)

具体计算的例子可以去看[隐马尔可夫模型HMM - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/29938926) 2.3节，如果看我的看不懂可以看看他的，理解稍微有些不同

## 3.2预测问题

### 3.2.1后向算法(Backward Algorithm)

和前向算法很像，只是前向算法是从前往后推，后向算法是从后往前推

定义在模型λ=(A,B,π)，t时刻时隐藏状态为 qi 的条件下，观测状态的序列为 ot+1,ot+2,...,oT的概率为后向概率。记为：![https://s2.loli.net/2023/12/03/JdhKCGbkLSt7srN.png](https://s2.loli.net/2023/12/03/JdhKCGbkLSt7srN.png)


![https://s2.loli.net/2023/12/03/UX2Tqt3kuZNBhil.png](https://s2.loli.net/2023/12/03/UX2Tqt3kuZNBhil.png)

由后向概率定义得，βt+1(j) = P（ot+2，ot+3.....oT | qt+1 = j，λ）

aij为P(qt+1= j | qt = i)，乘上aij后得到 P（ot+2，ot+3.....oT ,qt+1 =j | qt = i，λ）

求和后得到P（ot+2，ot+3.....oT  | qt = i，λ）

同样的，还需要把观测序列推到t+1，因此乘上bj(ot+1)，得到βt(i)

那最初的后向概率怎么得到呢，前向概率有初始状态可以推出来，但最初的后向概率是没有条件推导的，因此直接设置T时刻所有的后向概率为1，为什么呢，李琳山教授说的，经验之谈

### 3.2.2 推测隐藏序列

1.定义一个新变量γ，表示已知模型和观测序列时，t时刻为隐藏状态为 i 的概率

![https://s2.loli.net/2023/12/04/m59Jw4SnTzRY3uF.png](https://s2.loli.net/2023/12/04/m59Jw4SnTzRY3uF.png)

式子中的P(O,qt = i | λ)怎么来呢，前向概率和后向概率相乘即得到P(O,qt = i | λ)，可以自己乘一下看看

即P(O,qt = i | λ) = ɑt(i) * βt(i)

![https://s2.loli.net/2023/12/04/74lWRbivNwhysqg.png](https://s2.loli.net/2023/12/04/74lWRbivNwhysqg.png)

这个有什么用？ t时刻的隐藏状态有很多可能，γ最大的那个隐藏状态，我们就认为它是最有可能的隐藏状态


**2.维特比算法(viterbi algorithm)**

和前向算法有点像，定义一个新变量δ，和前向概率ɑ相比，q1~qt-1不再随意，而是确定的，取概率最大的路径

![https://s2.loli.net/2023/12/04/Ja3tIUTmMk859Kh.png](https://s2.loli.net/2023/12/04/Ja3tIUTmMk859Kh.png)

递推公式为
![https://s2.loli.net/2023/12/04/m3jcafCebISNKEk.png](https://s2.loli.net/2023/12/04/m3jcafCebISNKEk.png)

非常好理解，前向概率因为对前t-1个观测状态不设限制，因此用求和符号，而维特比只取概率最大的路径，因此是取最大值

再另取一个变量
![https://s2.loli.net/2023/12/04/OHScIMYrU2wRlea.png](https://s2.loli.net/2023/12/04/OHScIMYrU2wRlea.png)

这个看表达式也知道，就是记录下你是从哪个状态跳过来的，把路径记录下来

![https://s2.loli.net/2023/12/04/tnpkzXUSNeOy1Qi.png](https://s2.loli.net/2023/12/04/tnpkzXUSNeOy1Qi.png)

![https://s2.loli.net/2023/12/04/MZBqpdFCoDrTyNg.png](https://s2.loli.net/2023/12/04/MZBqpdFCoDrTyNg.png)


具体计算例子可以去看[隐马尔可夫模型HMM - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/29938926) 4.4节

**注意，Ψ表示的是δ*aij的最大值的序号，如：**

![https://s2.loli.net/2023/12/04/7LkPrFA6MhVIdun.png](https://s2.loli.net/2023/12/04/7LkPrFA6MhVIdun.png)

Ψ = 3表示它的上一个状态是状态3

**以及再注意：红框的两个不需要算，因为δ不是最大，不要被迷惑了(别问我为什么知道，因为我中了招)**

![https://s2.loli.net/2023/12/04/qVCQdMrJeWwIaER.png](https://s2.loli.net/2023/12/04/qVCQdMrJeWwIaER.png)
