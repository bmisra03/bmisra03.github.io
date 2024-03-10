---
layout: summary
title: "Deep Residual Learning for Image Recognition"
giscus_comments: true
bib_id: 1512.03385
---

### Three main points

#### 1. Naively stacking layers does not improve performance
Great advancements in image classification model accuracy have been made by designing deeper and deeper architectures. Experiments have shown that network depth is critical, and that all leading results on the ImageNet dataset have come from these "deep" networks. However, experiments have also shown that learning better networks is not as simple as stacking more layers. Originally, the problem of __vanishing/exploding gradients__ made deep networks unfeasible. However, even as solutions for vanishing/exploding gradients came to light, a __degradation problem__ started being observed: as the number of layers in a network increased, the model accuracy also increased. Notably, this problem was not caused by model __overfitting__, as the training acurracy of these networks also suffered with increased depth. The figure below illustrates this phenomenon on two models of different depths trained on the CIFAR-10 dataset. 

{% include figure.html
    path="/assets/img/summaries/ResNet_image_1.png"
    width="500px"
    class="z-depth-1"
%}

The differences in training accuracy indicate that not all systems are similarly easy to optimize; in other words, deeper models may have a more complex optimization landscape, making it difficult for current optimization techniques to identify a solution. Intuitively, if there is a shallower architecture, and a deeper model simply adds layers onto it, then there must be a solution produced by the deeper model with identical results to the shallower model. In this case, the added layers simply __identity map__, and the previous layers are copied from the shallower model. However, experimental results demonstrate that current optimizers struggle to find this mapping. 

#### 2. Residual learning can improve deep network results
The authors hypothesize that the degredation problem can be dealt with by introducing __deep residual learning__. Formally, if the desired underlying mapping is $$\mathcal{H}(x)$$, instead of hoping that the stacked layers directly fit this mapping, we let the layers fit the __residual mapping__ defined by $$\mathcal{F}(x) \coloneq \mathcal{H}(x) - x$$. In this case, the original mapping is cast as  $$\mathcal{F}(x) + x$$. The authors note that if the original function is asymptotically optimizable, then the alterned form must also be. Thus, the transformation is done in hopes that the transformed function is easier to optimize. The formulation of $$\mathcal{F}(x) + x$$ can be realized by __feedforward connections__, as shown in the figure below. 

{% include figure.html
    path="/assets/img/summaries/ResNet_image_2.png"
    width="500px"
    class="z-depth-1"
%}

As stated above, the degradation problem indicates that deep nonlinear layers make it difficult for solvers to find identity mappings. Thus, the authors hypothesize that the reformulation given above makes it easier to drive the weights of the added nonlinear layers to zero. Therefore, if the optimal solution is closer to the identity mapping than to the zero mapping, then it would be easier for solvers to find perterbuations with reference to the identity mapping, rather than learning a new function. 

In practice, the authors group the stacked layers in blocks, with each block containing a few stacked layers each. They adopt residual learning to every block. Formally, a building block is defined as: 

$$ 
\begin{align*}
    & y = \mathcal{F}(x, \{ W_i \}) + x \\
\end{align*}
$$

The operation $$\mathcal{F}(x) + x$$ is performed via the shortcut connection and element-wise addition. Furthermore, the nonlinear ReLU function is applied after the addition. Thus, it is gauranteed that the input and output of every block is positive. The authors note that the dimensions of $$x$$ and $$\mathcal{F}$$ must be equal. If they are not, then a linear projection matrix $$W_{s}$$ is applied to $$x$$ before the addition to standardize dimensions. 

$$ 
\begin{align*}
    & y = \mathcal{F}(x, \{ W_i \}) + W_{s}x \\
\end{align*}
$$

#### 3. Experimental results
To test their hypothesis, the author evaluate both training and validation accuracy of both a "plain" network and a "residual" network. The architectures of VGG-19, a 34-layer "plain" network, and a 34-layer "residual" network are shown from left to right below. The plain network is inspired heavily from VGG; the convolutional layers mostly have 3×3 filters, layers with the same feature map size have the same number of filters, and if the feature map size is reduced, the number of filters is increase proportionately to maintain computational complexity. The residual network is based on the plain network. However, as described in the previous section, each block has shortcut connections leading to the next block. In cases where the previous and next block dimensions are not identical, the authors consider two options: (A) The shortcut still performs identity mapping, with extra zero entries padded for increasing dimensions. This option introduces no extra parameter; (B) The projection shortcut is used to match dimensions (done by 1×1 convolutions).

{% include figure.html
    path="/assets/img/summaries/ResNet_image_3.png"
    width="625px"
    class="z-depth-1"
%}

The authors test several architectures, with varying levels of depth. A table outlining the specifications of each architecture is shown below: 

{% include figure.html
    path="/assets/img/summaries/ResNet_image_4.png"
    width="500px"
    class="z-depth-1"
%}

The figure below shows the results from training plain and residual networks on the ImageNet dataset. The plot on the left shows the error rates from testing on The thin curves show the error for training, while the bold curves show the error for validation. As observed earlier in the paper on CIFAR-10 data, deeper plain networks have both higher training and validation error rates, while residual networks are able to take advantage of the additional layers for greater classification accuracy. 

{% include figure.html
    path="/assets/img/summaries/ResNet_image_5.png"
    width="500px"
    class="z-depth-1"
%}

The table below shows the ensemble results of training networks of varying depths. ResNet achieved state-of-the-art performance over existing models at the time, despite having far fewer parameters. 

{% include figure.html
    path="/assets/img/summaries/ResNet_image_6.png"
    width="500px"
    class="z-depth-1"
%}

### Criticism
No criticism. Paper was easy to follow and provided convincing results.

### Conclusions for future work
The paper provided massive improvements over current image classification architectures at the time. It helped make training deep networks more feasible by establishing a solid framework, and provided critical insight on why deep networks fail in the first place. 