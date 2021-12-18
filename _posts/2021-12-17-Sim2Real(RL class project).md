---
title: "Transfering Simulated Learning to Real Robots"
layout: post
mathjax: true
date: 2021-12-17 08:31:19 +0700
---

Lately, Robotics has become a major playfield for Reinforcement Learning. Learning-based algorithms have the potential to enable robots to acquire complex behaviors adaptively in unstructured environments by leveraging data collected from the environment. In particular, with reinforcement learning, robots learn novel behaviors through trial and error interactions. This unburdens the human operator from having to pre-program accurate behaviors. This is particularly important as we deploy robots in scenarios where the environment may not be known.

However, not only is training in the real world very time consuming, it also puts the robots and the environment in considerable risk. To evade these problems, training in simulation and then transferring to the real is becoming a popular strategy. Training in simulation can lead to substantial speed-ups because of the possibility of parallelization without any significant extra costs.

But these models trained in Simulation suffer from what is termed as the Reality Gap. This reality gap might arise because of multiple reasons such as omitting some physical phenomena, having inaccurate parameterized values of physical quantities or even small numerical approximations in typical solvers. One can argue that ideally all of these could be solved by building more accurate simulators, but that's easier said than done. Moreover, the real world itself is non-stationary!

Our project deals with exploring methods to reduce this sim2real gap and deploy a model trained to do the Reach task in simulation on a real Xarm7 robot. Our goal is to deploy the model in the real world, with nothing but images as the current state input.

# Training in Simulation
Training in simulaiton provides many advatages. The most important amongst those is access to state variables instead of just the images. This allows us to speed up training by using [Asymmetric Actor Critic](https://arxiv.org/abs/1710.06542) model.
The Asymmetric Actor Critic uses a [DDPG](https://spinningup.openai.com/en/latest/algorithms/ddpg.html) based approach, where the Actor gets image-based inputs but the Critic at the time of training gets state-based input.


### Feature Extractor
We use a 3 layer Conv network to extract the current state (a 3D coordinate of the end effector's possition) from the Current state image (84x84x3) input. It is this state estimation using the feature extractor that is passed to the Actor in Test and Training time. Besides the RL loss that is backpropagated by the Actor Network, we also add an additional loss called the Bottleneck loss on the feature exctractor to speed up training. The Bottleneck loss minimizes the difference between the actual state (which we get at the time of training) and the predicted loss by the feature extractor.

The loss function for the feature extractor looks something like -

$$ L_f = L_{RL}  + MSE(s,s_{predicted}) $$

### Actor Network
The Actor takes in the predicted current state output from the feature extractor and the goal as a 3D coordinate as input, and outputs a 3D vector equivalent of the displacement in the 3D space.
The actor optamizes on the loss-

$$ L_a = -E_s[Q(s,\pi(s))] $$

### Critic Network
The Critic takes in the action yielded by the Actor along with the current state as a 3D coordinate of the end effector and the goal as a 3D coordinate as input. It outputs the predicted Q-value of the action given the current state and goal as a single float value.
The critic tries to optamize over the follwoing loss function-

$$ L_c = ( Q(s_t,a_t) - r_t - \gamma Q(s_{t+1},\pi(s_{t+1}) )  )^2 $$

Depth image from RealSense2 Camera
{% include embed.html url="https://www.youtube.com/embed/FYNW7Zaa-lo" %}

Depth image from simulation

![Depth](../assets/files/depth.png)

Reach Task using full RL-based model
{% include embed.html url="https://www.youtube.com/embed/rLUoR4EkUTo" %}
Reach Task using full RL-based model variation in camera position
{% include embed.html url="https://www.youtube.com/embed/8auil7amjDI"%}
Reach Task using RL-based model with pretrained feature extractor and depth information
{% include embed.html url="https://www.youtube.com/embed/xiiS1zGqjCQ"%}
