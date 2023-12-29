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
    path="/assets/img/summaries/defense-corrupted-base-set.png"
    width="600px"
    class="z-depth-1"
%}

The authors recognize the connection between data sifting and the classical outlier detection problem. Specifically, if all the outliers in a dataset are identified, then the complement of the outlier set can be used as the clean base set. Thus, to mark the efficacy of current automated methods in solving the data sifting problem, the authors measure five outlier detection algorithms' precision for sifting out a size-1000 CIFAR-10 subset (each class 100 samples). Precision is calculated using # correct clean samples in the selected subset divided by the total # samples for the respective target class (100). The results are tabulated below. Red cells denote worse performance than random selection, and yellow cells denote methods that are not time efficient. __Overall, there is no method that is both accurate and efficient for all poison attack types.__

{% include figure.html
    path="/assets/img/summaries/alternative-method-performance.png"
    width="600px"
    class="z-depth-1"
%}

#### 2. Meta-Sift

#### 3. Implementation

### Most Glaring Deficiency

### Conclusions for Future Work
