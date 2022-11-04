---
title: 语音合成模型Fastspeech2技术报告
copyright: false
date: 2022-04-01 15:12:36
tags: TTS
categories:
- TTS
- Fastspeech2
---
# 语音合成模型Fastspeech2技术报告
{% note info %}
论文：[FastSpeech 2: Fast and High-Quality End-to-End Text to Speech](https://arxiv.org/abs/2006.04558v1)
开源项目：[Fastspeech2 Github开源项目](https://github.com/ming024/FastSpeech2)
合成demo：[FastSpeech 2 语音合成演示](http://www.panjiangtao.cn/fastspeech2/)
{% endnote %}
<!-- more -->
## 服务器部署演示
<video src="http://file.panjiangtao.cn/fastspeech2_server.mp4" type="video/mp4"  poster="http://pic.panjiangtao.cn/img/image-20220408160721138.png" controls="controls">
    <p>你的浏览器不支持video标签.</p>
</video>

## 1 语音质量评估
### 1.1 主观评价

　　主观评价是通过人类对语音进行打分，比如平均意见得分（Mean Opinion Score，MOS）、众包平均意见得分（CrowdMOS，CMOS）和ABX 测试。主观评价中的MOS 评测是一种较为宽泛的说法，由于给出评测分数的主体是人类，因此可以灵活测试语音的不同方面。比如在语音合成领域，主要有自然度MOS（MOS of Naturalness）和相似度MOS（MOS of Similarity）。
　　但是人类给出的评分结果受到的干扰因素较多，谷歌对合成语音的主观评估方法进行了比较，在评估较长语音中的单个句子时，音频样本的呈现形式会显著影响参与人员给出的结果。比如仅提供单个句子而不提供上下文，与相同句子给出语境相比，被测人员给出的评分差异显著。国际电信联盟（International Telecommunication Union，ITU）将MOS 评测规范化为ITU-T P.800，其中绝对等级评分（Absolute Category Rating，ACR）应用最为广泛，ACR 的详细评估标准如下表所示。

![ACR评分表](http://pic.panjiangtao.cn/img/image-20220329115941800.png)

　　在使用ACR 方法对语音质量进行评价时，参与评测的人员（简称被试）对语音整体质量进行打分，分值范围为1 5 分，分数越大表示语音质量越好。MOS 大于4 时，可以认为该音质受到大部分被试的认可，音质较好；若MOS 低于3，则该语音有比较大的缺陷，大部分被试并不满意该音质。

#### 缺点：

　　MOS是一种主观量度，它的优点是显而易见的，但它的缺点也很多。首先，为了保证MOS的可信度足够高，一般来说需要雇佣大量的评价者进行评价，这就导致成本很高；另外，MOS的大小依赖于评价者，有的评价者很苛刻，那么她给的分数就会偏低，而有的评价者却是相反的。除此之外，MOS还会受到听语音时的环境，评价者的状态等多种因素的影响。

　　下图展示了一次评价中评价者给出的MOS分数的均值和标准差的分布，我们可以看到，标准差最大达到了2，这就说明评价者在某些语音的质量上有很大的分歧。

<img src="http://pic.panjiangtao.cn/img/v2-909d9159b4b4a7c9c43ce8dd23c23d1d_720w.jpg" alt="MOS分数均值、标准差" style="zoom: 67%;" />

### 1.2 客观评价

#### 1.2.1 MOSNet（开源）

　　下图为MOSNet的网络结构，就是LSTM、CNN以及它们的组合。注意这里模型输出两种MOS分数，一是frame-level的分数，另一个是utterance-level的分数。

<img src="http://pic.panjiangtao.cn/img/image-20220329120657195.png" alt="MOSNet结构图" style="zoom:67%;" />

　　损失函数为：

<img src="http://pic.panjiangtao.cn/img/image-20220329120853312.png" alt="image-20220329120853312" style="zoom:50%;" />

　　上式第一项是utterance-level的MSE，第二项是frame-level的MSE。

##### 问题：

　　首先不同工作所做的MOS评测所得到的数据是很难合并在一起的，因为不同MOS评测的分数分布都是不同的。所以像VCC 2018这样的大量且统一评价的情况是比较少的，因此简而言之，数据量还是不足。

　　另外，如何增加模型的泛化性也是一个重要的问题，举例来说，我们在VCC 2018上训练的模型是否能够预测其它的MOS评测，比如VCC 2019的分数呢？实际上这被证明是很难的。

#### 1.2.2 SESQA（未开源）

　　下图为SESQA模型结构，实际上是一个multi-task模型。模型接受一个语音的输入x，然后利用神经网络生成x的隐表示z，之后通过计算各种loss来学习到好的数据表示。

<img src="http://pic.panjiangtao.cn/img/image-20220329121351363.png" alt="SESQA模型结构" style="zoom:50%;" />

　　SESQA中包含8种loss，但是在ablation experiment中很多loss被证明是没什么用的，因此这里我将介绍最重要的几种loss，对没有介绍到的loss感兴趣的读者可以参考原论文。

　　首先是MOS loss，这个loss是针对有标注的数据设计的：

<img src="http://pic.panjiangtao.cn/img/image-20220329121709812.png" alt="MOS loss" style="zoom: 67%;" />



　　然后是pairwise ranking loss：

<img src="http://pic.panjiangtao.cn/img/v2-ba26d4f46e9e8e542a381f77f0bc0d33_720w.png" alt="pairwise ranking loss" style="zoom: 67%;" />



　　这个loss中$s_i$代表原语音$x_i$的分数，而$s_i$是在原语音$x_i$中加入一些噪声之后的语音$x_i$的分数，这样从直觉上来说一定有$s_i>s_j$，这个loss会对不符合这个限制的输出进行惩罚，$\alpha$是margin参数。

　　最后是score consistency loss：

<img src="http://pic.panjiangtao.cn/img/image-20220329121732291.png" alt="score consistency loss" style="zoom:50%;" />

这个loss中，$s_k$和$s_l$是两个类似的语音$x_k$和$x_l$的分数，因此有$s_k\approx s_l$；相反$s_i$和$s_j$是品质明显差别的两个语音$x_i$和$x_j$的分数，因此有$|s_i-s_j|>\beta$。loss的中间一项拓展了这两个关系，假设语音分别是语音$x_{ik}$和$x_{il}$的劣化版，且$x_{ik}$和$x_{il}$的品质类似，那么有：$s_{ik}-s_{jk}=s_{il}-s_{jl}$



## 2 语音信号特征

### 2.1 短时能量

　　短时能量体现的是信号在不同时刻的强弱程度。设第n 帧语音信号的短时能量用𝐸𝑛 表示，则其计算公式为：

<img src="http://pic.panjiangtao.cn/img/image-20220329161118385.png" alt="短时能量公式" style="zoom: 67%;" />

　　上式中，𝑀 为帧长，$𝑥_𝑛(𝑚)$为该帧中的样本点。

### 2.2 基频和基音周期

　　基音周期反映了声门相邻两次开闭之间的时间间隔，基频（fundamental frequency，F0）则是基音周期的倒数，对应着声带振动的频率，代表声音的音高，声带振动越快，基频越高。它是语音激励源的一个重要特征，比如可以通过基频区分性别。一般来说，成年男性基频在100-250Hz 左右，成年女性基频在150-350Hz 左右，女声的音高一般比男声稍高。

　　人类可感知声音的频率大致在20-20000Hz 之间，人类对于基频的感知遵循对数律，也就是说，人们会感觉100Hz 到200Hz 的差距，与200Hz 到400Hz 的差距相同。因此，音高常常用基频的对数来表示。在音乐上，把相差一倍的两个基频的差距称为一个八度（octave）；把一个八度12 等分，每一份称为一个半音（semitone）；把一个半音再100 等分，每一份称为一个音分（cent）。

　　基频是语音的重要特征，在包括语音合成的语音处理中有着广泛的应用，比如语音转换（Voice Conversion，VC）和语音合成中基频是一个强特征。基频的提取可以分为时域法和频域法。时域法以波形为输入，基本原理是寻找波形的最小正周期；频域法则会先对信号进行傅里叶变换，得到频谱，频谱在基频的整倍数处有尖峰，频域法的基本原理就是求出这些尖峰频率的最大公约数。但是考虑到基频并非每一帧都有，因此在提取基频前后，都需要判断有无基频，称之为清浊音判断（Unvoiced/Voiced Decision，U/V Decision）。语音的基频往往随着时间变化，在提取基频之前往往要进行分帧，逐帧提取的基频常常含有错误，其中常见的错误就是倍频错误和半频错误，也就是提取出来的基频是真实基频的两倍或者一半，因此基频提取后要进行平滑操作。常见的基频提取算法有基于信号处理时域法的 YIN1，基于信号处理频域法的 SWIPE2，基于机器学习时域法的 CREPE3和基于机器学习频域法的SPICE4。常用的基频提取工具有pyWORLD，Parselmouth，CREPE，YIN等。参见基频提取算法综述。

### 2.3 音高

　　音高（pitch）是由声音的基频决定的，音高和基频常常混用。可以这样认为，音高（pitch）是稀疏离散化的基频（F0）。由规律振动产生的声音一般都会有基频，比如语音中的元音和浊辅音；也有些声音没有基频，比如人类通过口腔挤压气流的清辅音。在汉语中，元音有a/e/i/o/u，浊辅音有y/w/v，其余音素比如b/p/q/x 等均为清辅音，在发音时，可以通过触摸喉咙感受和判断发音所属音素的种类。

### 2.4 MFCC和语谱图

　　对语音进行分析和处理时，部分信息在时域上难以分析，因此往往会提取频谱特征。在语音合成中，通常将频谱作为中间声学特征：首先将文本转换为频谱，再将频谱转换为波形；在语音识别中，则将频谱或者MFCC作为中间声学特征。语音通过预加重、分帧、加窗、傅里叶变换之后，取功率谱的幅度平方，进行梅尔滤波取对数之后，就得到了梅尔频谱（或称FilterBank/FBank），如果再进行离散余弦变换，就能够获得MFCC。语音通常是一个短时平稳信号，在进行傅里叶变换之前，一般要进行分帧，取音频的一个小片段进行短时傅里叶变换（STFT）。STFT 的结果是一个复数，包括幅度和相位信息，将该复数中的频率作为横轴，幅度作为纵轴，如图2.3所示，就组成了频谱图，将频谱图中的尖峰点连接起来，就形成了频谱包络。注意到，频谱图反映一个语音帧的频域情况，没有时间信息。因此，将每个帧对应的频谱图连接起来，以时间作为横轴，频率作为纵轴，颜色深浅表示幅度，如图2.4下面红图所示，就组成了语谱图。语谱图实际上是一个三维图，横轴时间，纵轴频率，颜色深浅表示幅度大小，一般来说，颜色越深，表示幅度值越大。

<img src="http://pic.panjiangtao.cn/img/image-20220329161609642.png" alt="图2.3: 频谱图" style="zoom: 50%;" />

<img src="http://pic.panjiangtao.cn/img/image-20220329161653885.png" alt="图2.4: 波形和对应的语谱图" style="zoom:67%;" />



## 3 数据预处理

### 3.1 将中文转化为拼音

　　首先准备好音频数据和对应的文字，如果文字是中文需要转化为拼音，我使用了pypinyin进行转化，每个中文汉字对应一个拼音，脚本如下，将中文字转为拼音：

```python
import os
import sys
import numpy as np
from pypinyin import pinyin, lazy_pinyin, Style
import re
root_dir = "../train/"
pattern = re.compile(r'(.*)\.txt$')
for root, dir, files in os.walk(root_dir):
	for filename in files:
		#print(filename)
		output = pattern.match(filename)
		if output is not None:
			print(root, filename)
			text_file = open(root+"/"+filename)
			line = text_file.read().strip()
			line = line.replace("，", "")
			pinyin =  lazy_pinyin(line, style=Style.TONE3, neutral_tone_with_five=True)
			pinyinline = ' '.join(pinyin)
			print(line)
			target_text_file = open(root+"/"+output.group(1)+".lab", "w")
			target_text_file.write(pinyinline)
			target_text_file.close()
```

　　保证音频文件的名字和脚本的文件名仅仅后缀不同：

　　例如：T0055G0002S0001.wav 和 T0055G0002S0001.lab

　　其中lab文件内容：yi3 hou4 ni3 shi4 nan2 hai2 zi5 原来的中文为：“以后你是男孩子”

### 3.2 MFA

　　[Montreal Forced Aligner](https://github.com/MontrealCorpusTools/Montreal-Forced-Aligner/) (MFA)，一种forced alignment工具。如果你熟悉有关工具的话，你可能会知道Prosodylab-Aligner这个forced alignment工具，而MFA正是它的升级版，拥有更好的性能。另外MFA使用了Kaldi而不是HTK，因此可以作为单独的package来使用。作者在英语数据上测试了MFA在单词和音素alignment上的表现，并和两个之前的工作进行了对比，实验结果显示MFA有更好的表现。

　　Forced alignment （以下简称FA，不过请注意这个简称不常用）是一个在speech processing中常见的任务。给定一段语音和它的文本，FA需要找到每个单词或是音素对应的时间段。FA中我们一般假设文本和语音的alignment是对角线型的，也就是说如果在text中某个音素出现在另一个音素的后面，那么在语音中这个关系也是成立的，反之亦同。

#### 3.2.1 MFA安装

```sh
pip install montreal-forced-aligner
mfa thirdparty download
pip install montreal-forced-aligner -U
```

#### 3.2.2 使用前准备

- 语言对应的发音词典，对于我们常用的英文、中文，都有比较好的可用的词典。
- 语音数据（`.wav`如果你没安装sox）
- 文本数据，注意文本数据的文件名中除了扩展名之外其他的部分要和对应的语音数据一一对应。

#### 3.2.3 使用

```sh
mfa model download acoustic english  #下载English acoustic model
mfa align /path/to/dataset /path/to/lexicon.txt english /output/path  # 使用预训练模型来进行alignment：
```

　　经过MFA处理后，生成一个包含所有音频的对齐文件.TextGrid

### 3.3 提取能量、音高、梅尔频谱和音素时长信息

　　预处理代码中下面有 6个functions,作用如下述所示，把语音数据，对应的textgrid数据和.lab 文本数据进行整合，提取出需要的energy, pitch, mel spectrogram, duration等信息。

```python
def __init__(self, config):
"""加载configs中配置，按照预设路径读入数据"""
 
def build_from_path(self):
"""
主要程序，主要作用是：
     1.加载从precess_utterance这个function里获得的信息
     2.对信息进行normalize,
     3.最后按照指定路径写入文件
      （speaker.json, stats.json, train.txt, val.txt)
"""
 
def process_utterance(self, speaker, basename):
"""
被build_from_path这个function调用
主要作用是
        1.通过get_alignment这个function获取textgrid files里的duration信息
        2.计算出wav files里的pitch
        3.通过stft（短时傅里叶变换）把声音文件转成mel频谱
        4.计算出wav files里的energy
        5.将获得的pitch, energy, mel，duration信息分别写入以.npy为后缀的文件
"""
 
def get_alignment(self, tier):
"""
被process_utterance这个function调用
主要作用是提取textgrid files里的phone,duration,start_time, end_time等信息
"""
 
def remove_outlier(self, values):
def normalize(self, in_dir, mean, std):
"""
这两个function都是用来normalize data的
"""
```

## 4 合成模型结构

### 4.1 总体结构

　　FastSpeech 2 的模型架构下图(a)所示，它沿用 FastSpeech 中提出的 Feed-Forward Transformer（FFT）架构，编解码器的输入首先进行位置编码，之后进入FFT 块。FFT 块主要包括多头注意力模块和位置前馈网络，位置前馈网络可以由若干层Conv1d、LayerNorm 和Dropout 组成。在音素编码器和梅尔频谱解码器中加入了一个可变信息适配器（Variance Adaptor），从而支持在 FastSpeech 2 和 2s 中引入更多语音中变化的信息，例如时长、音高、音量（频谱能量）等，来解决语音合成中的一对多映射问题（文本到语音合成中，一条文本可以对应到多条可变的语音，这些可变信息包括语音时长、音高、音量等。FastSpeech 通过知识蒸馏降低语音训练目标的变化性来缓解一对多映射问题，但也造成了训练目标的信息损失。FastSpeech 2 通过引入对应的可变信息作为解码器输入，使输入输出信息尽量匹配，来解决这个问题），最终得到的输出为梅尔频谱图，而后交给声码器生成音频。

<img src="http://pic.panjiangtao.cn/img/image-20220329204107873.png" alt="FastSpeech 2 和 2s 模型框架。图（b）中的 LR 表示 FastSpeech 中的序列长度适配操作，图（c）中的 LN 表示层归一化，可变信息预测器（variance predictor）包括时长、音高和能量预测器。" style="zoom:67%;" />

### 4.2 FFT（feed-forward Transformer，前馈Transformer）

采用了Attention机制和1D卷积，其中多头注意力结构如下图所示：

<img src="http://pic.panjiangtao.cn/img/202111181632_987.png" style="zoom:50%;" />

<img src="http://pic.panjiangtao.cn/img/202111181633_144.png" style="zoom: 50%;" />

<img src="http://pic.panjiangtao.cn/img/202111181635_901.png" style="zoom:50%;" />

Attention 机制实质上就是一个寻址过程，通过给定一个任务相关的查询 Query 向量 Q，通过计算与 Key 的注意力分布并附加在 Value 上，从而计算 Attention Value

1. Q，K，V 三个矩阵都来自同一个输入，通过线性变换得到 Q，K，V 三个向量。

2. 计算自注意力得分，计算每个单词的自注意力，和这个单词对句子中其他单词的评分，通过矩阵Q、K相乘计算

3. 第二步计算的分数进行缩放，这里通过除以 根号dk ( 论文中 ![[公式]](http://pic.panjiangtao.cn/img/equation)，这可以让模型有更稳定的梯度，默认值是 64，也可以是其它值 )，将结果进行softmax 归一化。

4. 最后乘以V

5. 上面过程做 H 次，再把输出合并起来


### 4.3 变量适配器（Variance Adaptor）

<img src="http://pic.panjiangtao.cn/img/image-20220330192940582.png" alt="Variance Adaptor" style="zoom:50%;" />

　　在对时长、基频和能量单独建模时，所使用的网络结构实际是相似的，在论文中称这种语音属性建模网络为变量适配器（Variance Adaptor）。时长预测的输出也作为基频和能量预测的输入。最后，基频预测和能量预测的输出，以及依靠时长信息展开的编码器输入元素加起来，作为下游网络的输入。变量适配器主要是由2 层卷积和1 层线性映射层组成，每层卷积后加ReLU 激活、LayerNorm 和Dropout。

#### 4.3.1 基本结构Variance Predictor

　　对时长、基频和能量建模的基本结构为Variance Predictor，其代码如下所示：

```python
class VariancePredictor(nn.Module):
    """Duration, Pitch and Energy Predictor"""
    def __init__(self, model_config):
        super(VariancePredictor, self).__init__()

        self.input_size = model_config["transformer"]["encoder_hidden"]
        self.filter_size = model_config["variance_predictor"]["filter_size"]
        self.kernel = model_config["variance_predictor"]["kernel_size"]
        self.conv_output_size = model_config["variance_predictor"]["filter_size"]
        self.dropout = model_config["variance_predictor"]["dropout"]

        self.conv_layer = nn.Sequential(OrderedDict([
                    ("conv1d_1",Conv(self.input_size,
                            		self.filter_size,
                            		kernel_size=self.kernel,
                            		padding=(self.kernel - 1) // 2,),),
                    ("relu_1", nn.ReLU()),
                    ("layer_norm_1", nn.LayerNorm(self.filter_size)),
                    ("dropout_1", nn.Dropout(self.dropout)),
                    ("conv1d_2",Conv(self.filter_size,
                            		self.filter_size,
                            		kernel_size=self.kernel,
                            		padding=1,),),
                    ("relu_2", nn.ReLU()),
                    ("layer_norm_2", nn.LayerNorm(self.filter_size)),
                    ("dropout_2", nn.Dropout(self.dropout)),
              ]))

        self.linear_layer = nn.Linear(self.conv_output_size, 1)

    def forward(self, encoder_output, mask):
        out = self.conv_layer(encoder_output)
        out = self.linear_layer(out)
        out = out.squeeze(-1)

        if mask is not None:
            out = out.masked_fill(mask, 0.0)

        return out
```

#### 4.3.2 Variance Adaptor实现

　　利用该变量适配器对时长、基频和能量进行建模。

```python
class VarianceAdaptor(nn.Module):
    """Variance Adaptor"""

    def __init__(self, preprocess_config, model_config):
        super(VarianceAdaptor, self).__init__()
        self.duration_predictor = VariancePredictor(model_config)
        self.length_regulator = LengthRegulator()
        self.pitch_predictor = VariancePredictor(model_config)
        self.energy_predictor = VariancePredictor(model_config)
        
        self.pitch_bins =nn.Parameter(torch.exp(torch.linspace(
            np.log(pitch_min), np.log(pitch_max), n_bins - 1)),requires_grad=False,)
        self.energy_bins = nn.Parameter(torch.exp(torch.linspace(
            np.log(energy_min), np.log(energy_max), n_bins - 1)),requires_grad=False,)
		self.pitch_embedding = nn.Embedding(
            n_bins, model_config["transformer"]["encoder_hidden"])
        self.energy_embedding = nn.Embedding(
            n_bins, model_config["transformer"]["encoder_hidden"])

    def forward(self,x,src_mask,mel_mask=None,max_len=None,pitch_target=None,
        energy_target=None,duration_target=None,p_control=1.0,e_control=1.0,d_control=1.0):

        log_duration_prediction = self.duration_predictor(x, src_mask)
		if duration_target is not None:
            x, mel_len = self.length_regulator(x, duration_target, max_len)
            duration_rounded = duration_target
        else:
            duration_rounded = torch.clamp(
                (torch.round(torch.exp(log_duration_prediction) - 1) * d_control),min=0)
            x, mel_len = self.length_regulator(x, duration_rounded, max_len)
            mel_mask = get_mask_from_lengths(mel_len)

        pitch_prediction, pitch_embedding = self.get_pitch_embedding(
            x, pitch_target, mel_mask, p_control)
            x = x + pitch_embedding
        energy_prediction, energy_embedding = self.get_energy_embedding(
            x, energy_target, mel_mask, p_control
            x = x + energy_embedding

        return x,pitch_prediction,energy_prediction,log_duration_prediction,
            duration_rounded,mel_len,mel_mask
```

## 5 声码器

### 5.1 MelGan

论文链接： [MelGAN: Generative Adversarial Networks for Conditional Waveform Synthesis](http://arxiv.org/abs/1910.06711)

开源项目：[Multi-band MelGAN and Full band MelGAN](https://github.com/rishikksh20/melgan)

演示demo：[MelGan声码器效果演示](http://www.panjiangtao.cn/melgan/)

#### 优点

- MelGAN是一种非自回归前馈卷积架构，是第一个由GAN去实现原始音频的生成，在没有额外的蒸馏和感知损失的引入下仍能产生高质量的语音合成模型。
- MelGAN解码器可替代自回归模型，以生成原始音频。
- MelGAN的速度明显快于其他Mel谱图转换到音频的方法，在保证音频质量没有明显下降的情况下比迄今为止最快的可用模型快10倍。

模型包括两部分: Generator（生成器） 和 Discriminator（判别器）。

#### 5.1.1 生成器

　　输入为mel-spectrogram，输出为raw waveform. 从 mel-spectrogram到 audio的过程很显然是一个上采样的过程。

　　这里的上采样是由一维反卷积(transpose1d)实现的，上采样的倍数如何确定呢？

　　需要注意一下，上采样的倍数是由hop_size来决定的，为什么呢？

　　需要明白一点，mel帧数 * 帧移 = 音频长度（采样点个数，可换算为音频时长，具体怎么做不用说了吧）

　　因此，对于22050采样率， hopsize大小设置为256， 那么对应的mel-spectrogram需要上采样 256倍

　　如果是16000采样率呢？ 使用帧长是50ms,帧移 12.5ms 那么hopsize就是200啦，所以上采样倍数就是200倍啦.

　　搞清楚了这些，那么Generator Upsampling层中的上采样倍数也就好理解了，22050的采样倍数为 8 X 8 X 2 X 2 = 256

##### 结构

<img src="http://pic.panjiangtao.cn/img/20200310123249936.png" alt="MelGAN生成器结构" style="zoom: 50%;" />

其中残差块结构：

<img src="http://pic.panjiangtao.cn/img/2020031012332078.png" alt="MelGAN生成器残差快结构" style="zoom: 33%;" />

　　经过一层Conv层后送到上采样网络块，上采样网络块一共有4个，依次为8x,8x,2x,2x，每个上采样网络块中嵌套残差块，每个残差块有三层，依次dilation为1,3,9，最后经过一层conv层得到音频输出，由于音频的channel表示为1，所以最后一层的channel设为1。

##### 设计思路

1. Mel频谱图的时间分辨率比原始音频低256倍，所以使用了堆叠的反卷积层进行unsample。

2. 条件信息足够的情况下，在输入处增加噪声是不必要的。所以与传统GAN不同，并没有增加noise input

3. 使用残差块解决梯度消散的问题，空洞卷积层的感受野随层数的增加而指数增加，能够有效地增加每个输出时间步长的感应野。

4. 反卷积层的k-size和stride仔细选择决定的，可以减少artifacts的出现。

5. 归一化选择Weight Norm，因为不会限制判别器的空间，也不会对激活进行归一化

##### 源码

```python
#对一维卷积层进行Weight Norm
def WNConv1d(*args, **kwargs):
    return weight_norm(nn.Conv1d(*args, **kwargs))
#对一维反卷积层进行Weight Norm
def WNConvTranspose1d(*args, **kwargs):
    return weight_norm(nn.ConvTranspose1d(*args, **kwargs))
#残差块中一层的结构
class ResnetBlock(nn.Module):
    def __init__(self, dim, dilation=1):
        super().__init__()
        #依次为两层卷积层
        self.block = nn.Sequential(
            nn.LeakyReLU(0.2),
            nn.ReflectionPad1d(dilation),
            WNConv1d(dim, dim, kernel_size=3, dilation=dilation),
            nn.LeakyReLU(0.2),
            WNConv1d(dim, dim, kernel_size=1),
        )
        self.shortcut = WNConv1d(dim, dim, kernel_size=1) 

    def forward(self, x):
        return self.shortcut(x) + self.block(x) #残差连接
#生成器
class Generator(nn.Module):
    def __init__(self, input_size, ngf, n_residual_layers):
        super().__init__()
        ratios = [8, 8, 2, 2]
        self.hop_length = np.prod(ratios)
        mult = int(2 ** len(ratios))
		#第一层卷积
        model = [
            nn.ReflectionPad1d(3),
            WNConv1d(input_size, mult * ngf, kernel_size=7, padding=0),
        ]

        # 上采样阶段，共4个，依次为8x,8x,2x,2x的UpSampling layer
        for i, r in enumerate(ratios):
            model += [
                nn.LeakyReLU(0.2),
                WNConvTranspose1d(
                    mult * ngf,
                    mult * ngf // 2,
                    kernel_size=r * 2,
                    stride=r,
                    padding=r // 2 + r % 2,
                    output_padding=r % 2,
                ),
            ]
			#加入残差块，每个残差块中有3层，dilation分别为1,3,9
            for j in range(n_residual_layers):
                model += [ResnetBlock(mult * ngf // 2, dilation=3 ** j)]

            mult //= 2
		#最后一层卷积层
        model += [
            nn.LeakyReLU(0.2),
            nn.ReflectionPad1d(3),
            WNConv1d(ngf, 1, kernel_size=7, padding=0),
            nn.Tanh(),
        ]
        self.model = nn.Sequential(*model)
        self.apply(weights_init)

    def forward(self, x):
        return self.model(x)
```

#### 5.1.2 判别器

　　论文提出了multi-scale的discriminator, 基于的假设是每个scale的discriminator可以学习到不同频率段的音频的特征。

　　每个discriminator的网络结构是由前后各一层1维卷积 和 4层分组卷积构成的downsampling layer构成. discriminator输入是由ground truth的音频和gererator生成的fake音频两部分构成的。 输入维度为[B, T, 1]， 输出维度也是[B,T,1], 中间变换的只是通道数的变化， 最后一层的输出和倒数第二层卷积网络的输出被分别用来计算 featuremap 和 feature_score, 这两部分被用来计算 generator的*feature_matching_loss*(L1_loss) 和discriminator的*mse_loss*

　　上面我们知道了Generator输入mel-spectrogram,生成音频audio, 这个音频可以表示为G （s）, s为mel-spectrogram. 判别器要判断生成器生成音频的真假，这里就涉及到Gan模型的原理啦，

　　Generator输入mel-spectrogram生成音频(fake), Discriminator输入真(real)音频和假(fake)音频，学习一个二分类器（可以这么理解），这里使用的是mse损失来最小化real与1的差异，fake与0的差异。

　　通过对抗学习，使得generator生成的音频达到判别器无法判断真假的效果(loss接近0.5)。

##### 结构

<img src="https://img-blog.csdnimg.cn/20200310123441481.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNTYyNzA0,size_16,color_FFFFFF,t_70#pic_center" alt="melgan判别器模型" style="zoom:50%;" />

##### 损失函数：

<img src="http://pic.panjiangtao.cn/img/image-20220331174705235.png" alt="melgan判别器损失函数" style="zoom: 67%;" />

x表示音频，s表示mel谱图，z表示高斯噪声

##### 源码

```python
#对一维卷积层进行Weight Norm
def WNConv1d(*args, **kwargs):
    return weight_norm(nn.Conv1d(*args, **kwargs))
#对一维反卷积层进行Weight Norm
def WNConvTranspose1d(*args, **kwargs):
    return weight_norm(nn.ConvTranspose1d(*args, **kwargs))
#Discriminator Block结构
class NLayerDiscriminator(nn.Module):
    def __init__(self, ndf, n_layers, downsampling_factor):
        super().__init__()
        model = nn.ModuleDict()
        #第一层卷积
        model["layer_0"] = nn.Sequential(
            nn.ReflectionPad1d(7),
            WNConv1d(1, ndf, kernel_size=15),
            nn.LeakyReLU(0.2, True),
        )
        nf = ndf
        stride = downsampling_factor
        #4层4x Downsampling Layer
        for n in range(1, n_layers + 1):
            nf_prev = nf
            nf = min(nf * stride, 1024)
            model["layer_%d" % n] = nn.Sequential(
                WNConv1d(
                    nf_prev,
                    nf,
                    kernel_size=stride * 10 + 1,
                    stride=stride,
                    padding=stride * 5,
                    groups=nf_prev // 4,
                ),
                nn.LeakyReLU(0.2, True),
            )

        nf = min(nf * 2, 1024)
        #第2层卷积层
        model["layer_%d" % (n_layers + 1)] = nn.Sequential(
            WNConv1d(nf_prev, nf, kernel_size=5, stride=1, padding=2),
            nn.LeakyReLU(0.2, True),
        )
        #第3层卷积层
        model["layer_%d" % (n_layers + 2)] = WNConv1d(
            nf, 1, kernel_size=3, stride=1, padding=1
        )

        self.model = model
    def forward(self, x):
        results = [] #存放每层输出的feature map
        for key, layer in self.model.items():
            x = layer(x)
            results.append(x)
        return results
#完整的判别器
class Discriminator(nn.Module):
    def __init__(self, num_D, ndf, n_layers, downsampling_factor):
        super().__init__()
        self.model = nn.ModuleDict()
        #3个Discriminator Block
        for i in range(num_D):
            self.model[f"disc_{i}"] = NLayerDiscriminator(
                ndf, n_layers, downsampling_factor
            )
		#downsample函数
        self.downsample = nn.AvgPool1d(4, stride=2, padding=1, count_include_pad=False)
        self.apply(weights_init)

    def forward(self, x):
        results = []
        for key, disc in self.model.items():
            results.append(disc(x)) #每次降频处理得到的结果依次存放到result
            x = self.downsample(x) #对输入x进行降频处理
        return results
```

