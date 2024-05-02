---
layout: summary
title: "Playing Atari with Deep Reinforcement Learning"
giscus_comments: true
bib_id: 1312.5602
---

### Three main points

#### 1. Deep Reinforcement Learning
Reinforcement Learning (RL) is a subfield of machine learning in which agents interact with their environment to learn optimal state/action pairings that maximize an external reward function. RL problems are often modeled as Markov Decision Processes (MDPs), and traditionally solved using tabular methods or Monte Carlo. Due to the complexity inherent to reinforcement learning systems, it has been difficult to train them directly from high-dimensional inputs such as vision or speech. However, the advances in extracting such high-level features from deep neural networks in recent years has made the possibility of applying deep learning to reinforcement learning problems much more attractive.

In this paper, the authors seek to apply RL to Atari games, passing only a vector of raw pixels at the current time as the input, and learning an optimal policy $$\pi$$ that maximizes expected future rewards. As stated previously, the optimal action-value function $$Q^*(s,a)$$ is computed using tabulated methods such as value iteration, but this is impractical for real-world problems. In practice, we approximate the action-value function, and update future states based on this approximation. A network can be trained by minimizing the following loss function:

$$ 
\begin{align*}
    & L_i(\theta_i)=\mathcal{E}_{s,a \sim p(\cdot)}[(y_i - Q(s,a;\theta_i))^2]\\
\end{align*}
$$ 

where $$y_i$$ is the target for iteration $$i$$ and $$p(s,a)$$ is a probability distribution over $$(s,a)$$ that we call the behavior distribution. If we calculate these expectations using stochastic gradient descent (SGD), then we arrive at $$Q-learning$$. It is important to note that the algorithm is model-free: it solves the task using samples from the emulator, without explicity estimating the emulator. To learn from the Atari games, we seek to perform deep Q learning.

#### 2. Deep Q Learning
Traditional Q-learning methods struggle to handle high-dimensional state spaces, such as raw pixel inputs from images. Deep Q-Learning addresses this challenge by approximating the action-value function using a deep neural network, referred to as the Deep Q-Network (DQN).

The DQN takes raw sensory inputs (e.g., pixels of the game screen) as input and outputs Q-values for each possible action. By training the DQN to predict Q-values that maximize the expected cumulative reward, the agent effectively learns a policy for decision-making directly from the sensory input.

To stabilize training and improve sample efficiency, the DQN employs an experience replay mechanism. During training, the agent stores experiences $$(s,a,r,s^{'})$$ in a replay buffer. These experiences are randomly sampled from the buffer to update the DQN's parameters. Experience replay helps decorrelate consecutive experiences and break the temporal correlation in the data, leading to more stable and efficient learning.

Another key component of DQN is the target network, which is a separate neural network used to estimate the target Q-values during training. The parameters of the target network are periodically updated to match those of the primary network. This technique helps stabilize training by reducing the risk of divergence that can occur when using the same network to both select and evaluate actions.

The full algorithm is shown below: 
{% include figure.html
    path="/assets/img/summaries/rl-image-1.png"
    width="500px"
    class="z-depth-1"
%}

#### 3. Experimental Results
To test their algorithm, the authors experiment on seven Atari games: Beam Rider, Breakout, Enduro, Pong, Q*bert, Seaquest, and Space Invaders. Identical model settings were used on the original, unmodified games. Positive rewards were fixed to +1, and negative rewards set to -1 for all games. 

To measure success, the authors use highest Q values on games, as this value is more stable over time than immediate reward, as demonstrated in the plot below showing the average reward and Q value on Breakout and Seaquest. 

{% include figure.html
    path="/assets/img/summaries/rl-image-2.png"
    width="500px"
    class="z-depth-1"
%}

Ultimately, the agent trained with Deep Q-Learning was able to perform better than both existing methods and human experts, demonstrating the robustness of the algorithm. In fact, on every single game other than Space Invaders, DQN beats every other algorithm.

{% include figure.html
    path="/assets/img/summaries/rl-image-3.png"
    width="500px"
    class="z-depth-1"
%}

### Criticism
No criticism. 

### Conclusions for future work
This paper demonstrated the utility of deep learning to solve reinforcement learning problems. The authors showed, for the first time, it was possible to efficiently and effectively solve reinforcement learning problems without large amounts of prior knowledge about the environemnt, or heavy feature engineering. It inspired a new branch of deep learning research devoted to solving reinforcement learning problems, eventually culminating in the development of AlphaGo, a reinforcement learning system that beat the world's best experts at Go, a complex strategy game.