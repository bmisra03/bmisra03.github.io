---
layout: summary
title: "How to Sift Out a Clean Data Subset in the Presence of Data Poisoning?"
giscus_comments: true
bib_id: 2210.06516v2
---

### Three Main Points

#### 1. Sifting Out a Clean Enough Base Set is Hard

Many popular defense algorithms require access to clean base sets in order to be effective. For example, a common Trojan-Net Detection approach is to generate potential triggers from a target model, and seeing if applying those triggers to a clean data set leads to suspicious results. Likewise, a common defense against Label-Flipping attacks is to find a subset of label-feature pairings, such that its exclusion from the training dataset leads to maximal accuracy when testing on a clean base set.

A chart showing the performance of four common defense paradigms when given access to clean base sets (green) vs contaminated base sets (red) is shown below. The yellow row contains baselines results (either random guessing as a defense strategy or no defense used). Poison Detection was evaluated using __Poison Filtering Rate (PFR)__, which measures the ratio of poisoned samples that are correctly detected. Trojan-Net Detection was evaluated using __Area Under the ROC Curve (AUC)__, which measures the entire 2-D area underneath the ROC curve. Backdoor Removal was evaluated using __Attack Success Rate (ASR)__, which calculates the frequency with which non-target-class samples patched with the backdoor trigger are misclassified into the attacker-desired target class. Finally, Robust Training was evaluated using __Test Accuracy (ACC)__, which measures the accuracy of the trained model on a clean test set. __In all instances, not having access to a clean base set significantly reduced defense performance.__

{% include figure.html
    path="/assets/img/summaries/meta-sift-table-1.png"
    width="750px"
    class="z-depth-1"
%}

The authors recognize the connection between data sifting and the classical outlier detection problem. Specifically, if all the outliers in a dataset are identified, then the complement of the outlier set can be used as the clean base set. Thus, to mark the efficacy of current automated methods in solving the data sifting problem, the authors measure five outlier detection algorithms' precision for sifting out a size-1000 CIFAR-10 subset (each class 100 samples). Precision is calculated using # correct clean samples in the selected subset divided by the total # samples for the respective target class (100). The results are tabulated below. Red cells denote worse performance than random selection, and yellow cells denote methods that are not time efficient. __Overall, there is no method that is both accurate and efficient for all poison attack types.__

{% include figure.html
    path="/assets/img/summaries/meta-sift-table-2.png"
    width="750px"
    class="z-depth-1"
%}

#### 2. Meta-Sift
The authors present a novel approach to solve the data sifting problem; instead of using outlier detection to extract a clean base set, they propose reducing the sifting problem to a splitting problem. They argue that a model trained on clean data will perform poorly when tested on poisoned data, regardless of the attack type, since any form of data poisoning leads to a distributional shift from clean data.

Concretely, the authors formulate the splitting problem as a bilayer optimization problem. Given the contaminated dataset $$D\ = D_1\ \cup ... \cup\ D_k $$, where $$D_k$$ is the subset of $$D$$ containing only samples with class labels $$k \in \{1 , ... , k \}$$, divide each $$D_k$$ into two splits $$B_k$$ and $$D_k \backslash B_k$$, such that:

$$ 
\begin{align*}
    & \mathcal{B}^{*} = \argmax_{\mathcal{B}} \sum_{k=1}^{K} \sum_{z_i \in D_k \backslash B_k} L(\theta^{*}(\mathcal{B}), z_i) \\
    & s.t.\ \theta^{*}(\mathcal{B}) = \argmin_{\theta} \sum_{k=1}^{K} \sum_{z_j \in B_k} L(\theta, z_j) \\
    & |B_1|\ = ... =\ |B_k| \\
    & |B_1|\ + ... +\ |B_K|\ \geq (1-\varepsilon) |D| \\
\end{align*}
$$

where where $$\mathcal{B} = B_1\ \cup ... \cup\ B_K$$ is the union of the class-wise training splits, $$\theta$$ represents the parameters of an ML model, and $$ \theta^{*}(\mathcal{B}) $$ denotes the parameters obtained from training on B. $$L$$ is the loss function, e.g., cross-entropy loss for classification tasks. The inner optimization finds a model trained over one split $$\mathcal{B}$$, and the outer optimization finds the split that maximizes loss when tested on its complement $$D \backslash \mathcal{B}$$. Furthermore, the algorithm requires that $$\mathcal{B}$$ is a perfectly label-balanced subset of $$D$$. 

The authors recognize the combinatorial nature of this approach lead to exploding computational costs. Thus, they relax the splitting problem to a continous splitting problem: 

$$ 
\begin{align*}
    & \mathcal{V}^{*} = \argmax_{\mathcal{V}} \sum_{k=1}^{K} \sum_{z_i \in D_k \backslash B_k} (1-v_{(k, i)}) L(\theta^{*}(\mathcal{V}), z_i) \\
    & s.t.\ \theta^{*}(\mathcal{V}) = \argmin_{\theta} \sum_{k=1}^{K} \sum_{z_i \in D_k} v_{k, i} \cdot L(\theta, z_i) \\
    & ||V_1||_1\ = ... =\ ||V_k||_1 \\
    & ||V_1||_1\ + ... +\ ||V_k||_1 \geq (1-\varepsilon) |D| \\
\end{align*}
$$

where instead of directly optimizing the split, the algorithm instead optimizes a tuple of binary variables indicating each sample's presence; if $$z_i \in B$$, then $$v_{(k, i)} = 1$$. To solve the equations, the authors relax the binary variables $$v_{(k, i)}$$ as continous ones ($$v_{(k, i)} \in [0, 1]$$). Now, $$v_{(k, i)}$$ represents the likelihood that $$z_i$$ is assigned to the training split, and gradient-based methods can be used to solve the equations. 

However, when dealing with large datasets, the outer optimization still has to optimize over a large space. Thus, the authors choose to learn a weight assigning network to assign weight to each point, instead of directly optimizing weights. Let $$ \mathcal{S}( \cdot ) $$ denote the weight-assigning network and let $$ \psi $$ deote its parameters. In addition, $$ \forall k$$ and $$ z_i \in D_k $$, let $$ v_{(k, i)} = \mathcal{S}( L(\theta, z_i), \psi )$$, and let $$ L_i( \theta) = L(\theta, z_i) $$. Then, the splitting problem is reformulated as: 

$$ 
\begin{align*}
    & \psi^{*} = \argmax_{\psi} \sum_{i=1}^{|D|} (1-S(L_i(\theta)); \psi) L_i(\theta^{*}(\psi)) \\
    & s.t.\ \theta^{*}(\psi) = \argmin_{\theta} \sum_{i=1}^{|D|} S(L_i(\theta); \psi) L_i(\theta) \\
\end{align*}
$$

#### 3. Implementation and Results
The authors separate the sifting process into two stages; training and identification. A sifter is defined as the combination of $$\mathcal{S}(\cdot)$$ parameterized by $$\psi^*$$ and the classification model parameterized by $$\theta^*$$. The training stage produces _m_ sifters, and the identification stage assigns weights to the data in the dataset. An overview of the process is outlined below: 

{% include figure.html
    path="/assets/img/summaries/meta-sift-overview.png"
    width="750px"
    class="z-depth-1"
%}

The authors present their results for META-SIFT. Shown below are NCR results under CIFAR-10 settings, and NCR results under ImageNet settings: 

{% include figure.html
    path="/assets/img/summaries/meta-sift-table-5.png"
    width="750px"
    class="z-depth-1"
%}

{% include figure.html
    path="/assets/img/summaries/meta-sift-table-6.png"
    width="750px"
    class="z-depth-1"
%}

As shown, the results demonstrate that META-SIFT is able to sift a dataset with 0 NCR and significant orders of magnitude less than than existing methods, for both CIFAR-10 and ImageNet datasets.

### Most Glaring Deficiency

### Conclusions for Future Work
