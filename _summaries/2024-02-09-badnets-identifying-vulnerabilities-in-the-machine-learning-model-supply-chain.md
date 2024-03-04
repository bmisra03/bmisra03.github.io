---
layout: summary
title: "BadNets: Identifying Vulnerabilities in the Machine Learning Model Supply Chain"
giscus_comments: true
bib_id: 1708.06733
---

### Three Main Points

#### 1. Outsourcing Model Training Can Make it Vulnerable to Backdoors
Due to the proven effectiveness of deep learning architectures on a wide range of applications, the demand for them has exploded. However, due to the high computational cost associated with training models, many companies/individuals have chosen to outsource trianing to the cloud, or to fine-tune pretrained models and apply them to new problems. 

The authors propose that both these scenarios raise security concerns by introducing the possibility of __backdoored neural networks__, or __BadNets__. In this scenario, parts of the model training process are outsourced to hosts who want to provide the user with a model that has been trained to have a backdoor. The models necessarily must perform well on most inputs, but cause misclassifications on specific backdoored inputs. These misclassifications can either be targeted (i.e., flipping the labels of two specified classes), or untargeted, leading to an overall decrease in classification accuracy for certain inputs. The method through which a bad actor could backdoor a model is shown below. 

{% include figure.html
    path="/assets/img/summaries/BadNet_image_1.png"
    width="500px"
    class="z-depth-1"
%}

The model on the left depicts a benign classifier; given a set of inputs, it classifies them as expected. The model in the middle depicts the ideal behavior of a backdoor network- it uses a separate model to identify trigger inputs and the output of the model takes into account both the benign classifier and the backdoored classifier. The image on the right depicts a realistic manifestation of a backdoored model; since a malicious actor cannot change the model architecture itself, it must influence behavior by altering weights. 

#### 2. Case Study: MNST Digit Recognition Attack
The authors first explore the impact of backdoors on handwritten digit classification using the MNIST dataset. The baseline model is a CNN with two convolution layer and two fully connected layers. The authors implement the back door by __training set poisoning__, in which they augment the training dataset with incorrectly labeled samples to influence model behavior. They use two different backdoors (single pixel backdoor and pattern backdoor, shown in the image below), as well as two different attack types: 
- __Single target attack:__ the attack labels backdoored versions of digit i as digit j.
- __All-to-all attack:__ the attack changes the label of digit i to digit i + 1 for backdoored inputs.

{% include figure.html
    path="/assets/img/summaries/BadNet_image_2.png"
    width="500px"
    class="z-depth-1"
%}

The figure below shows the clean set error and backdoor set error for each of the 90 instances of the single target attack using the single pixel backdoor. The colored values show the error on clean input images and backdoored images, respectively. On clean samples, the model performs well; it correctly classifies images with a low error rate. This is a critical test for backdoors- if the poisoned model does not perform well on validation data, it is likely to raise suspicion. In addition, we can see that it also performs well on backdoored images. Note that the authors report classification error on backdoored images against the poisoned labels; thus, the low classification error on backdoored images is favorable to the attacker and reflective of the attack’s success.

{% include figure.html
    path="/assets/img/summaries/BadNet_image_3.png"
    width="500px"
    class="z-depth-1"
%}

Similar results are observed for all-to-all attacks; BadNet successfully mislabels $$> 99\%$$ of backdoored images. 

{% include figure.html
    path="/assets/img/summaries/BadNet_image_4.png"
    width="500px"
    class="z-depth-1"
%}

#### 3. Case Study: Traffic Sign Detection Attack
The authors train a backdoored road sign model using similar techniques as the MNIST dataset, and yield similar results. For sake of brevity and minimizing redundancy, I will let the reader review this section of the article on their own time. Instead, I will discuss the most challenging and (in my opinion) most interesting part of the paper; the ransfer learning attack. 

The authors model their experiment on the following premise: a bad actor has trained a BadNet on US traffic signs, and uploaded their backdoored model in a public repository. An unassuming scientist downloads the model and retrains it to classify Swedish traffic signs. When applying transfer learning to CNNs, users often resize the output layer to account for differences in the number of classes, and retrain the fully connected layers on the new data. However, convolution layers are frequently left untouched, as they are considered feature extracters. Thus, malicious weights in the convolution layer can lead to poisoned results on a clean dataset. 

{% include figure.html
    path="/assets/img/summaries/BadNet_image_5.png"
    width="500px"
    class="z-depth-1"
%}

As the results demonstrate in the table below, a BadNet used for transfer learning was indeed able to degrade model classification accuracy in the presence of backdoor triggers. The results demonstrate that the BadNet actually performed marginally better on clean Swedish samples, and significantly ($$\approx 10\%$$) worse on backdoored samples. Furthermore, the authors identified that the same nodes activated in the presence of backdoored samples in both the US model and the Swedish model, confirming that the reduction in model accuracy was due to the backdoor samples. When the authors retroactively increased the weights on these nodes, the Swedish model performance on backdoored samples drastically decreased, albeit with a reduction in performance for clean samples as well. Most notably, increasing backdoor strength by a factor of 20 led to a $$7 \%$$ reduction in clean sample performance, and a $$25 \%$$ reduction in backdoor sample performance. The full results highlighting the impact of backdor strength on BadNet performance are tabulated below. 

{% include figure.html
    path="/assets/img/summaries/BadNet_image_6.png"
    width="500px"
    class="z-depth-1"
%}

### Criticism
No criticism. Paper was easy to follow and provided convincing results.

### Conclusions for Future Work
The paper was a seminal work in identifying the vulnerabilites of outsourcing model training to third party sources. It demonstrated the vulnerabiliteis and dangers for researchers, students, developers, and companies using publically available models, and sparked a branch of research dedicated to identifying backdoors and ensuring safe access to pretrained models. 
