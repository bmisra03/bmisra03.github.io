---
layout: summary
title: "Communication-Efficient Learning of Deep Networks from Decentralized Data"
giscus_comments: false
bib_id: 1602.05629
---

### Three main points

#### 1. Training on decentralized data from mobile devices
Many deep learning applications have the following characteristics: 
- Training on real-world data from mobile devices provides an advantage over training on public or proxy data available to servers.
- Training data is privacy-sensitive or too large to store in a data center.
- If the task is supervised, then data labels can be inferred from user interaction.

The two example problems the authors give are __image classification__ (i.e., identifying which photos are likely to be shared or viewed multiple times) and __language models__ (i.e., next word prediction), both of which require data that is: 
1. Privacy sensitive. 
2. Likely to be distributed differently from data available to servers. 

For example, user-uploaded images available on platforms such as Flickr or Instagram are likely much different from all the photos that a mobile device user may take, and the formal diction published on the web is likely much different from the way people text each other. 

To take advantage of the data available on mobile devices and minimize the privacy risk/data overhead of sending complete user data to a data center, the authors propose only transmitting the minimal update needed to improve a model. Due to the data processing inequality, this operation ensures that the data received by the data center contains at most the same amount of information as the raw data on the device, and often times much less. 

The authors also describe key characteristics of the "Federated Optimization" problem. Namely, training data is:
- Non-IID, since data on a particular user's device is most likely not representative of data on all users' devices.
- Unbalanced, since some users use certain applications with much greater frequency than others, leading to high variation in the amount of data per user. 
- Massively distributed, since the authors expect the total number of clients to be much greater than the amount of data available per client. 
- Limited communications, since devices are frequently offline or on slow/expensive connections.

#### 2. FederatedAveraging algorithm

The authors use stochastic gradient descent (SGD) as a baseline to compare their algorithm with. They select a random $$C$$-fraction of clients each round and compute the gradient of loss over all data held in these clients. Thus, for a current model $$w_t$$ with $$C=1$$ and learning rate $$\eta$$, the server computes the update $$w_t \leftarrow w_t - \eta \sum_{k=1}^{K}\frac{n_j}{n}g_k$$. In other words, the server takes the weighted average of each client locally taking one step of gradient descent on the current model. The authors extend this algorithm by allowing each client to perform multiple local updates before averaging gradients. Thus, the total computation is controlled by three parameters: 
- $$C$$- fraction of clients that perform computation per round. C = 0 corresponds to 1 client per round.
- $$E$$- number of training passes each client makes on its local dataset each round. 
- $$B$$- local minibatch size used for client updates. $$B = \infty$$ corresponds to the full local dataset being used for updates. 

The full algorithm is shown below: 
{% include figure.html
    path="/assets/img/summaries/FederatedAveraging.png"
    width="500px"
    class="z-depth-1"
%}

#### 3. Experimental results
The authors present results for for datasets total. The first study includes three model families on two datasets. 

The first two models (multilayer-perceptron and convolution neural network) are used for the MNIST digit recognition task. They study two ways of partitioning the data: __IID__ (data shuffled, partitioned into 100 clients each receiving 600 samples) and __Non-IID__ (sort data by digit label, divide into 200 shards of size 300, and assign each of 100 clients 2 shards). The next model (stacked character-level LSTM language model) is used on a dataset build from _The Complete Works of William Shakespeare_. They again create an __IID__ and __Non-IID__ version of the dataset, with an 80% train-test split each. 

First, the authors investigate the effects of increasing multi-client parallelism. A table showing the effect of increasing $$C$$ for a fixed $$E$$ and $$B$$ is shown below. As shown, there is a significant speedup compared to baseline for increasing $$C$$ values. 

{% include figure.html
    path="/assets/img/summaries/FederatedAveragingShakespeare.png"
    width="500px"
    class="z-depth-1"
%}

Based on the results, the authors fix $$C=0.1$$ for the investigating the effects of increasing computation per client. The results for both the MNIST CNN and the Shakespeare LSTM are shown in the table below. Encouragingly, there is a significant speedup associated with using FedAvg in comparison to FedSGD, especially for Non-IID data. 

{% include figure.html
    path="/assets/img/summaries/FederatedAveragingMNIST.png"
    width="500px"
    class="z-depth-1"
%}

The authors also run experiments on the CIFAR-10 dataset and a large-scale next-word prediction task to demonstrate the efficacy of FedAvg on real-world problems. The results are shown below. FedAvg converges to desired test accuracy scores much faster than FedSGD in both instances; for CIFAR, which standard SGD took 1975,000 minibatch updates to reach a test accuracy of 86%, FedAVG achieved similar results after only 2,000 rounds. Similar results were observed for the LSTM word prediction task; While it took around 800 round to reach a test accuracy of 10% using FedSGD, it took less than 50 rounds to reach the same test accuracy using FedAvg.

{% include figure.html
    path="/assets/img/summaries/FederatedAveragingCIFAR.png"
    width="500px"
    class="z-depth-1"
%}
{% include figure.html
    path="/assets/img/summaries/FederatedAveragingNextWord.png"
    width="500px"
    class="z-depth-1"
%}

### Criticism
No criticism. Paper was easy to follow and provided convincing results.

### Conclusions for future work
The paper demonstrates that federated learning can be a practical solution to solving deep learning problems that require user data privacy.
