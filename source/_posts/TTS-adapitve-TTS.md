---
title: 多说话人自适应合成
copyright: True
date: 2022-05-05 19:58:13
tags: TTS
categories:
- TTS
- Adaptive TTS
---
## 多说话人自适应合成

　　本文介绍了三篇自适应多说话人语音合成论文及代码分析。
<!-- more -->
### 1. [Transfer Learning from Speaker Verification to Multispeaker Text-To-Speech Synthesis](https://arxiv.org/pdf/1806.04558)
{% note info %}
github源码:[Real-Time-Voice-Cloning](https://github.com/CorentinJ/Real-Time-Voice-Cloning)
demo演示:[SV2TTS 说话人音色语音合成演示](http://www.panjiangtao.cn/SV2TTS/)
{% endnote %}

#### 1.1 论文内容

　　在tacotron2中加入d-vector向量进行个性化指定说话人语音合成

　　d-vector：将指定说话人的一句语音输入一个DNN网络，获取其最后一个隐藏层的输出，用在说话人识别领域

##### 推理过程：

①选择编码器将语音的文本内容进行编码。得到一个向量。

②将目标人的音色（声音）进行编码或者处理成向量。

③将上面的两种向量在某一种维度上进行拼接得到更长的向量（一般为2倍的长度）在通过解码器进行解码成一个语音信息（并不是最终的），之后经过相应的处理得到真正的语音。整个过程就是模型的推理过程。

#### 1.2 模型结构

![SV2TTS模型](http://pic.panjiangtao.cn/img/image-20220419204452965.png)

##### 1.2.1 说话人编码器网络(提取说话人特征)

　　使用独立的噪声语音数据集进行说话人验证任务的训练，从来自目标说话人几秒钟的参考语音中生成固定维度的**嵌入向量**(说话人语音特征)，该向量称为d-vector

　　训练TTS时使用说话人编码器的预训练模型。

　　模型结构如下，使用一个多层LSTM、线性层和ReLU层。

```python
class SpeakerEncoder(nn.Module):
    def __init__(self, device, loss_device):
        super().__init__()
        self.loss_device = loss_device
        
        # Network defition
        self.lstm = nn.LSTM(input_size=mel_n_channels,
                            hidden_size=model_hidden_size, 
                            num_layers=model_num_layers, 
                            batch_first=True).to(device)
        self.linear = nn.Linear(in_features=model_hidden_size, 
                                out_features=model_embedding_size).to(device)
        self.relu = torch.nn.ReLU().to(device)
        
        # Cosine similarity scaling (with fixed initial parameter values)
        self.similarity_weight = nn.Parameter(torch.tensor([10.])).to(loss_device)
        self.similarity_bias = nn.Parameter(torch.tensor([-5.])).to(loss_device)

        # Loss
        self.loss_fn = nn.CrossEntropyLoss().to(loss_device)
        
    def do_gradient_ops(self):
        # Gradient scale
        self.similarity_weight.grad *= 0.01
        self.similarity_bias.grad *= 0.01
            
        # Gradient clipping
        clip_grad_norm_(self.parameters(), 3, norm_type=2)
    
    def forward(self, utterances, hidden_init=None):
        """
        Computes the embeddings of a batch of utterance spectrograms.
        
        :param utterances: batch of mel-scale filterbanks of same duration as a tensor of shape 
        (batch_size, n_frames, n_channels) 
        :param hidden_init: initial hidden state of the LSTM as a tensor of shape (num_layers, 
        batch_size, hidden_size). Will default to a tensor of zeros if None.
        :return: the embeddings as a tensor of shape (batch_size, embedding_size)
        """
        # Pass the input through the LSTM layers and retrieve all outputs, the final hidden state
        # and the final cell state.
        out, (hidden, cell) = self.lstm(utterances, hidden_init)
        
        # We take only the hidden state of the last layer
        embeds_raw = self.relu(self.linear(hidden[-1]))
        
        # L2-normalize it
        embeds = embeds_raw / (torch.norm(embeds_raw, dim=1, keepdim=True) + 1e-5)        

        return embeds
```



##### d-vector聚类效果

　　预训练模型的聚类效果如下，使用TIMIT数据集进行训练，可能是训练数据较少，效果较差。

<img src="http://pic.panjiangtao.cn/img/D-vector说话人聚类.png" alt="D-vector说话人聚类" style="zoom:67%;" />

##### 1.2.2 基于Tacotron 2的序列到序列合成网络

　　只需将说话人嵌入向量传递到注意力层，就可以在不同的说话人之间汇聚。其基于说话者嵌入从文本生成mel谱图；

　　文本编码后加入说话人嵌入向量代码：

```python
class Encoder(nn.Module):
    def __init__(self, embed_dims, num_chars, encoder_dims, K, num_highways, dropout):
        super().__init__()
        prenet_dims = (encoder_dims, encoder_dims)
        cbhg_channels = encoder_dims
        self.embedding = nn.Embedding(num_chars, embed_dims)
        self.pre_net = PreNet(embed_dims, fc1_dims=prenet_dims[0], fc2_dims=prenet_dims[1],
                              dropout=dropout)
        #PreNet表示的是两个全连接层
        self.cbhg = CBHG(K=K, in_channels=cbhg_channels, channels=cbhg_channels,
                         proj_channels=[cbhg_channels, cbhg_channels],
                         num_highways=num_highways)
    def forward(self, x, speaker_embedding=None):
        x = self.embedding(x)
        x = self.pre_net(x)
        x.transpose_(1, 2)
        x = self.cbhg(x)
        if speaker_embedding is not None:
            # 加入说话人嵌入向量
            x = self.add_speaker_embedding(x, speaker_embedding)
        return x
    
        def add_speaker_embedding(self, x, speaker_embedding):
        # SV2TTS的主要贡献
        # The input x is the encoder output and is a 3D tensor with size (batch_size, num_chars, tts_embed_dims)
        # When training, speaker_embedding is also a 2D tensor with size (batch_size, speaker_embedding_size)
        #     (for inference, speaker_embedding is a 1D tensor with size (speaker_embedding_size))
        # This concats the speaker embedding for each char in the encoder output
 
        # Save the dimensions as human-readable names
        batch_size = x.size()[0]
        num_chars = x.size()[1]
 
        if speaker_embedding.dim() == 1:
            idx = 0
        else:
            idx = 1
 
        # Start by making a copy of each speaker embedding to match the input text length
        # The output of this has size (batch_size, num_chars * speaker_embedding_size)
        speaker_embedding_size = speaker_embedding.size()[idx]
        e = speaker_embedding.repeat_interleave(num_chars, dim=idx)
 
        # Reshape it and transpose
        e = e.reshape(batch_size, speaker_embedding_size, num_chars)
        e = e.transpose(1, 2)
 
        # Concatenate the tiled speaker embedding with the encoder output
        x = torch.cat((x, e), 2)
        return x
```



##### 1.2.3 自回归声码器waveNet

　　声码器网络，其将mel频谱图转换成时域波形。

### 2. [ECAPA-TDNN for Multi-speaker Text-to-speech Synthesis](https://arxiv.org/pdf/2203.10473)

#### 2.1 论文内容

　　在FastSpeech2中加入ECAPA-TDNN(进化版x-vector)进行个性化训练

#### 2.2 模型结构

![ECAPA-TTS模型结构](http://pic.panjiangtao.cn/img/image-20220419204836223.png)

##### 2.2.1 多说话人编码—ECAPA网络

　　使用[ECAPA模型](https://arxiv.org/pdf/2005.07143)进行说话人嵌入生成，模型聚类效果如下，使用VoxCeleb2数据集进行训练，取得很好的效果。

![ecapa说话人聚类](http://pic.panjiangtao.cn/img/ecapa说话人聚类.png)

　　模型结构：

<img src="http://pic.panjiangtao.cn/img/image-20220422133618465.png" alt=" ECAPA-TDNN结构" style="zoom: 50%;" />

模型框架代码：

```python
class ECAPA_TDNN(torch.nn.Module):
    def __init__(self,input_size,device="cpu",lin_neurons=192,activation=torch.nn.ReLU,
                 channels=[512, 512, 512, 512, 1536],kernel_sizes=[5, 3, 3, 3,1],
                 dilations=[1, 2, 3, 4, 1],attention_channels=128,res2net_scale=8,
                 se_channels=128,global_context=True,groups=[1, 1, 1, 1, 1],
    ):
        super().__init__()
        assert len(channels) == len(kernel_sizes)
        assert len(channels) == len(dilations)
        self.channels = channels
        self.blocks = nn.ModuleList()
        # The initial TDNN layer
        self.blocks.append(TDNNBlock(input_size,channels[0],kernel_sizes[0],
                                     dilations[0],activation,groups[0],))
        # SE-Res2Net layers
        for i in range(1, len(channels) - 1):
            self.blocks.append(SERes2NetBlock(channels[i - 1],channels[i],
                    res2net_scale=res2net_scale,se_channels=se_channels,
                    kernel_size=kernel_sizes[i],dilation=dilations[i],
                    activation=activation,groups=groups[i],))

        # Multi-layer feature aggregation
        self.mfa = TDNNBlock(channels[-1],channels[-1],kernel_sizes[-1],dilations[-1],
            activation,groups=groups[-1],)

        # Attentive Statistical Pooling
        self.asp = AttentiveStatisticsPooling(channels[-1],
            attention_channels=attention_channels,global_context=global_context,)
        self.asp_bn = BatchNorm1d(input_size=channels[-1] * 2)

        # Final linear transformation
        self.fc = Conv1d(in_channels=channels[-1] * 2,out_channels=lin_neurons,
            kernel_size=1,)

    def forward(self, x, lengths=None):
        """Returns the embedding vector.
        Arguments
        ---------
        x : torch.Tensor
            Tensor of shape (batch, time, channel).
        """
        # Minimize transpose for efficiency
        x = x.transpose(1, 2)

        xl = []
        for layer in self.blocks:
            try:
                x = layer(x, lengths=lengths)
            except TypeError:
                x = layer(x)
            xl.append(x)

        # Multi-layer feature aggregation
        x = torch.cat(xl[1:], dim=1)
        x = self.mfa(x)

        # Attentive Statistical Pooling
        x = self.asp(x, lengths=lengths)
        x = self.asp_bn(x)

        # Final linear transformation
        x = self.fc(x)

        x = x.transpose(1, 2)
        return x
```



其中，SE-Res2Block的结构为：

<img src="http://pic.panjiangtao.cn/img/image-20220422134136052.png" alt="SE-Res2Block" style="zoom: 50%;" />

##### 2.2.2 在Fastspeech2中加入说话人嵌入向量

　　将说话人嵌入向量在音素编码后与其进行叠加，再输入方差适配器中。

```python
class FastSpeech2(nn.Module):		
    def __init__(self, preprocess_config, model_config):
      	"""
      	"""
        self.speaker_emb = SpeakerEmbedding(model_config)
    def forward()
    	"""
    	"""
    	if self.speaker_emb is not None:
            spker_embed=self.speaker_emb(spker_embed)
            # speaker_classifier_output_1 = self.speaker_classifier_1(spker_embed)
            output=output+self.add_speaker_embedding(output,spker_embed)
    def add_speaker_embedding(self, x, speaker_embedding):
        # 将speaker_embedding处理后与x相同维度
        batch_size = x.size()[0]
        num_chars = x.size()[1]

        if speaker_embedding.dim() == 1:
            idx = 0
        else:
            idx = 1
        speaker_embedding_size = speaker_embedding.size()[idx]
        e = speaker_embedding.repeat_interleave(num_chars, dim=idx)

        e = e.reshape(batch_size, speaker_embedding_size, num_chars)
        e = e.transpose(1, 2)

        return e

class SpeakerEmbedding(nn.Module):
    def __init__(self, model_config):
        super(SpeakerEmbedding, self).__init__()
        assert model_config["multi_speaker"] == True
        self.hidden_size = model_config["speaker_embedding"]["embedding_model"]["lin_neurons"]
        self.embedding_size = model_config["speaker_embedding"]["embedding_size"]
        self.speaker_emb = nn.Linear(self.hidden_size,self.embedding_size)     
        self.norm=nn.LayerNorm(self.embedding_size)

    def forward(self, spker_embeds):
        speaker_emb = self.speaker_emb(spker_embeds)
        speaker_emb = self.norm(speaker_emb)
        return speaker_emb
```

#### 2.3 模型训练效果

　　加入预训练说话人嵌入模型后，模型整体训练效果如下图所示：

<img src="http://pic.panjiangtao.cn/img/image-20220427171316769.png" alt="Fastspeech-ECAPA训练效果" style="zoom:50%;" />

　　缺点：对**未可见的说话人**来说个性化合成的效果较差。

### 3.Meta-TTS: Meta-Learning for Few-Shot Speaker Adaptive Text-to-Speech
{% note info %}
论文地址：[Meta-TTS](https://arxiv.org/pdf/2111.04040)
代码仓库：[Meta-TTS](https://github.com/SungFeng-Huang/Meta-TTS/)
{% endnote %}

#### 3.1 论文内容

　　本文将**元学习**（meta-learning）技术应用到说话人自适应方法当中。提出Model Agnostic Meta-Learning （MAML）算法来训练多说话人TTS模型，使得模型可以找到一个合适的元初始状态（meta-initialization）以便其快速地适应到任意少样本说话人。因此，我们可以将使用元学习算法训练好的TTS模型**对unseen speaker进行高效的适应**。

#### 3.2 模型结构

##### 3.2.1 基于d-vector的Fastspeech2

　　本文在Fastspeech2模型的基础上加入d-vector说话人嵌入，将该嵌入分别在音素编码后、梅尔频谱解码前加入到数据中，如下图所示。

<img src="http://pic.panjiangtao.cn/img/image-20220505191349282.png" alt="多说话人嵌入Fastspeech2模型结构" style="zoom:50%;" />

　　修改后的模型代码如下所示：

```python
class Fastspeech2(nn.module):
    def forward(self,batch):
    	output = self.encoder(texts, src_masks)
        if self.speaker_emb is not None:
            output = output + self.speaker_emb(spker_embed).unsqueeze(1).expand(
                -1, max_src_len, -1)
            
        (output,p_predictions,e_predictions,log_d_predictions,d_rounded,mel_lens,mel_masks) = 			       			self.variance_adaptor(output,src_masks,mel_masks,max_mel_len,p_targets,\
            e_targets,d_targets,p_control,e_control,d_control)
        
        if self.speaker_emb is not None:
            output = output + self.speaker_emb(spker_embed).unsqueeze(1).expand(
                -1, max(mel_lens), -1)
            
        output, mel_masks = self.decoder(output, mel_masks)
        output = self.mel_linear(output)
        postnet_output = self.postnet(output) + output
```

##### 3.2.2 模型训练过程

　　本文研究对象为K-shot语音克隆任务，我们需要从多说话人语音合成数据库中构建一系列的meta-tasks。每一个meta-task的support set和query set均包含相同说话人的K个样本和Q个样本。在训练阶段，我们设置K=Q，在模型推理阶段，我们设置Q=1。

　　为了生成meta-task，我们先从语音合成数据中采用一个说话人，之后针对该说话人随机选择2K个语音样本，其中K个分配到support set，另外K个分配到query set。

　　在使用MAML算法训练多说话人FastSpeech2模型时，在内循环自适应阶段，文本编码器的参数保持固定不变，只微调说话人嵌入、变量适配器和解码器的参数。meta-training训练流程如下图所示。

<img src="http://pic.panjiangtao.cn/img/image-20220505194827685.png" alt="meta-TTS训练流" style="zoom:50%;" />

##### 3.2.3 基于元学习的模型训练

　　为了加速说话人自适应方法的训练，我们提出了基于meta-learning的说话人自适应训练方法。Model-Agnostic Meta-Learning （MAML）算法是一个经典的meta-learning算法，其目的是为快速自适应找到合适的参数初始化状态。MAML包含两层优化循环操作：外循环（outer loop）和内循环（inner loop）。外循环目的是找到一个元初始化状态（meta-initialization），之后内循环可以在此基础上基于少量样本快速适应到新任务中。

<img src="http://pic.panjiangtao.cn/img/640" alt="元学习过程" style="zoom:67%;" />

　　假定我们将要在一个K-shot回归下游任务上评价模型效果，MAML会基于训练数据生成一系列K-shot回归训练任务，其中每个任务被称作一个适用于元学习的“meta-task”。每个任务包含一个含有K个样本的supprt set，以及一个含有Q个样本的query set。每个任务的学习目标都是基于support set学习任务相关的模型参数（内循环，inner loop）。MAML的目标就是学习meta-initialization参数，每个meta-task都可以基于该参数得到表现不错的任务自适应参数（外循环，outer loop）。这一优化过程称为“meta-update”。

#### 3.3 模型效果

##### 3.3.1 训练损失效果

　　加入预训练说话人嵌入模型后，模型整体训练效果如下图所示，由于加入两段说话人嵌入，因此损失波动较大：

<img src="http://pic.panjiangtao.cn/img/image-20220505192410237.png" alt="训练效果" style="zoom:40%;" />

##### 3.3.2 模型合成效果

　　模型训练2000步至2000000步后，合成后的mel频谱图变化如下两张图所示

<img src="http://pic.panjiangtao.cn/img/image-20220505193143278.png" alt="step-2000" style="zoom: 50%;" /><img src="http://pic.panjiangtao.cn/img/image-20220505193056377.png" alt="step-2000000" style="zoom:56%;" />



