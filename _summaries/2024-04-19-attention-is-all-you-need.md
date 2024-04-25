---
layout: summary
title: "Attention Is All You Need"
giscus_comments: true
bib_id: 1706.03762
---

### Three main points
#### 1. Transformer Architecture
Recurrent neural networks (RNNs for short) have been established as state of the art methods for modeling sequentional problems, ranging from language modeling to machine translation. The inherently sequential nature of these models makes parallelization difficult, leading to exploding computational costs and space utilization. So-called ttention mechanisms allow for modeling dependencies between data, regardless to their distance in the input/output sequences. Up until this paper, attention mechansims had rarely been used alone to model sequential data. The authors propose leaving RNNs and CNNs behind entireley, and instead relying soley upon self-attention to compute input and output representations.

The proposed architecture is shown below:

{% include figure.html
    path="/assets/img/summaries/attention-image-1.png"
    width="500px"
    class="z-depth-1"
%}

It can be divided in the the following sections: Encoder and Decoder Stacks, Attention Function, Scaled Dot-Product Attention, Multi-Head Attention, and Feed-Forward Networks. Encoders and decoders are traditionally used in RNN architectures for sequence to sequence tasks, such as translation. The encoder takes a variable-length input sequence, processes the sequences sequentially, aiming to caputure the meaning and context of the sequence. It outputs a fixed-length context vector that summarizes the encoded information. Decoders take the context vector from the encoder as an input, gerate the output sequence one elemt at a time, and use internal state and information from previously generated outputs to predict the next element. It outputs a variable-length sequence that corresponds to the encoded input vector. Although typically the encoder and decoder stacks use RNN elements (such as LSTMs), the architecture shown above relies solely on attention functions and fully connected networks. 
#### 2. Self-Attention Mechanism
The authors describe the attention function as "mapping a query and a set of key-value pairs to an output". The output is computed as as weighted sum of the values, where weights are chosen by the compatibility function of the query with the corresponding key. 

The specific attention mechanism used in this paper is called the "Scaled Dot-Product Attention" function. The input contains queries and keys of dimensions $$d_k$$ and values of dimension $$d_v$$.The equation for computing outputs is shown below: 

$$ 
\begin{align*}
    & Attention(Q,K,V) = softmax(\frac{QK^T}{\sqrt{d_k}})V \\
\end{align*}
$$ 

The authors' proposed attention mechanism is very similar to an existing algorithm called dot-product attention; in practice, scaled dot-product attention and dot-product attention are functionally compatible. However, they authors note significant improvements in speed and space utilization with the presence of the scaling factor $$\frac{1}{\sqrt{d_k}}$$. 

Furthermore, the authors choose to use a multi-headed attention mechanism, in which they project the queries, keys, and values to each of the $$h$$ heads. The utility of having multiple attention heads is that it allows the model to jointly process information from "different representation subspaces at different positions"; in other words, it allows for enhanced representation learning (since each head focus on a different part of the input sequence), more robust representation since the model can provide multiple perspectives on the input data, and saved computational cost due to parallelized information processing.

A diagram of Scaled Dot-Product Attention and Multi-Head Attention are shown below:

{% include figure.html
    path="/assets/img/summaries/attention-image-2.png"
    width="500px"
    class="z-depth-1"
%}

The authors use multiheaded attention in three different ways:
1. In encoder-decoder layers to mimic the attention mechanisms in traditional encoder-decoder sequence models.
2. In encoder layers to capture interdependencies between input layers.
3. In decoder layers to allow each position to attend all information in preceding decoder layers.

#### 3. Experimental Results
The authors tested the efficacy of their novel Transformer architecture on two machine translation tasks: the WMT 2014 English-German translation task, and the WMT 2014 English-French translation task. As shown in the table below, the Transformer architecture was able to achieve state of the art results with significantly less computational cost compared to other, popular models at the time.

{% include figure.html
    path="/assets/img/summaries/attention-image-3.png"
    width="500px"
    class="z-depth-1"
%}

The authors also sought to identify how well the Transformer generalized on novel data. They did this by training a 4-layer Transformer on the WSJ portion of the Penn Treebank, a widely used corpus in NLP research. Once again, the Transformer achieved state of the art results on this task, demonstrating the generalizatino ability of the model with no fine-tuning to the novel task.

{% include figure.html
    path="/assets/img/summaries/attention-image-4.png"
    width="500px"
    class="z-depth-1"
%}

### Criticism
No criticism.

### Conclusions for future work
This paper introduced a groundbreaking architecture for sequence modeling, replacing traditional recurrence with self-attention mechanisms. This innovation significantly improved performance on various NLP tasks, enabling more efficient parallelization and capturing long-range dependencies more effectively.
