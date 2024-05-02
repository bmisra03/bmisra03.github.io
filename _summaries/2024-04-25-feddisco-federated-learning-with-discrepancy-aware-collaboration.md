---
layout: summary
title: "FedDisco: Federated Learning with Discrepancy-Aware Collaboration"
giscus_comments: true
bib_id: 2305.19229
---

### Three main points

#### 1. Data Heterogenity is a Problem When Dealing wiwth Federated Learning Systems
Federated Learning (FL) has been applied to a variety of real-world applications since it was proposed in 2017, such as recommender systems, healthcare IoT, and text prediction. Although results are promising, there are a number of challenges that impede models from reaching state-of-the art performance, such as data heterogenity, communication cost between servers and edge devices, and privacy-preservation. 

In this paper, the authors focus on one issue in data heterogenity: category distribution heterogenity, which is defined as the difference in clients' category distributions. Intuitively this makes sense; there is significant difference in the ways that individual users interact with their mobile devices (which is often where data in FL system comes from). However, this heterogenity causes local models being optimized towards different local objectives, causing divergent optimization directions. Thus, naively aggregating updates from these models makes it difficult to train a robust global model. 

The figure below illustrates the problems with this approach. As shown, clients may have wildly different class distributions from each other, and the vanilla FL algorithm (FedAvg) is agnostic to these differences. The authors' proposed algorithm FedDisco would instead alter weights during aggregation to account for heterogeneity. 

{% include figure.html
    path="/assets/img/summaries/feddisco-image-1.png"
    width="500px"
    class="z-depth-1"
%}

#### 2. Dataset Size is not Enough to Model Client Heterogeneity 
Current models adjust aggregation weights based on client size; in other words, the larger the client is, the more its local updates are weighted in updating the global model. However, the authors argue that this is not optimal, as it is not a comprehensive measure of hetergeneity. To prove their hypothesis, they provide empirical observations in which they split up the CIFAR-10 dataset amongst 10 clients, where local distributions are heterogenous and follow the Dirichlet distribution. They consider two settings: one in which they decide aggregation weights based on dataset size, and another in which they aggregate them based on discrepency between the local class distribution and the global class distribution. L2 distance is used as the measure for discrepency. The results of this exploration for each of the 10 clients are shown below. As demonstrated, relative dataset size is not a good indicator of the model's performance. In contrast, discrepency value is a much more useful metric. 

{% include figure.html
    path="/assets/img/summaries/feddisco-image-5.png"
    width="500px"
    class="z-depth-1"
%}

#### 3. FedDisco Algorithm and Results
The authors propose a novel four stage algorithm that takes category distribution discrepency into account called FedDisco. The steps are outlined below:

1. Local model training. Each client k performs t steps of local training on the local dataset. 

2. Each client performs a discrepency computation between its local distribution and global category distribution. For the scope of this paper, the authors assume that the global distribution is uniform, in order to reduce communication costs and privacy concerns with calculating a global distribution. 

3. Each client $$k$$ calculates its aggregation weights using the following formula by using the relative dataset size $$n_k$$ and local discrepency $$d_k$$: 

$$ 
\begin{align*}
    & p_k = \frac{ReLU(n_k - a \cdot d_k + b)}{\sum_{m=1}^{K}ReLU(n_m - a \cdot d_m + b)}\\
\end{align*}
$$ 

where $$a$$ is a hyperparameter to balance $$n_k$$ and $$d_k$$, and $$b$$ is a bias parameter. 

4. The server computes a global model based on the discrepency-aware aggregation weights.

The image below shows a high-level pictoral representation of the FedDisco algorithm. 

{% include figure.html
    path="/assets/img/summaries/feddisco-image-2.png"
    width="500px"
    class="z-depth-1"
%}

For testing the efficacy of the algorithm, the authors evaluate its performance on five image classification datasets that cover a range of medical, natural, and artifical scenarios. They consider clients with two data distribution settings which they term NIID-1 and NIID-2, with NIID-2 being more heterogeneous. 10 clients are NIID-1, while 5 clients are NIID-2 are considered. For image-clasification tasks, the authors use ResNet18, and for AG news, they use TextCNN. As demonstrated in the table below, FedDisco was able to achieve state of the art results on all classification tasks across a variety of datasets and heterogeneity settings. 

{% include figure.html
    path="/assets/img/summaries/feddisco-image-3.png"
    width="500px"
    class="z-depth-1"
%}

Furthermore, the authors emphasize the modularity of their implementation; specifically, FedDisco can be easily implemented in existing model architectures with easy, and subsequently lead to significant improvements in performance. The table below shows the performance gains associated with incorporating FedDisco. Notably, the largest improvements were seen on the HAM10000 dataset, a collection of multi-source dematoscopic images of common skin legions. 

{% include figure.html
    path="/assets/img/summaries/feddisco-image-4.png"
    width="500px"
    class="z-depth-1"
%}

### Criticism
The assumption of equal global class distribution is a pivotal point in the paper, for discrepency calculation, privacy preservation, and minimization of communication overhead alike. Although it works well on predefined datasets which are inherently balanced, this assumption does not hold up well for real-world data. For example, if we consider the classic example of image recommendation presented in the original FL paper, it is impossible to know a global class distribution of personal image data beforehand. I believe that future work on this paper should focus on estimating an accurate global distribution of data before applying FedDisco.

### Conclusions for future work
This paper demonstrates the importannce of considering the class distribution heterogeneity between clients and the global state. The authors recommend extending category-level discrepency to other aspects of model training, such as feature-level heterogeneity and mask-level heterogeneity. 