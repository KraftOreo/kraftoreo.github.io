---
title:  "RL class Project"
mathjax: true
layout: post
categories: media
---

Recently Robotics has become a major playfield for Reinforcement Learning. Learning-based algorithms have the potential to enable robots to acquire complex behaviors adaptively in unstructured environments, by leveraging data collected from the environment. In particular, with reinforcement learning, robots learn novel behaviors through trial and error interactions. This unburdens the human operator from having to pre-program accurate behaviors. This is particularly important as we deploy robots in scenarios where the environment may not be known.

However, not only is training in the real world very time-consuming, it also puts the robots and the environment at considerable risk. To evade these problems, training in simulation and then transferring to the real is becoming a popular strategy. Training in simulation can lead to susbstantail speed ups because of the possibility of paralelisation without any significant extra incurred costs.

But these models trained in Simulation suffer from what is termed as the Reality Gap. This reality gap might arise because of multiple reasons such as omiting some physical phenomena, having inaccurate parameterised values of physical quantities or even small numerical approximations in typical solvers. One can argue that ideally all of these could be solved by building more accurate simulators, but that's easier said than done. Moreover, the real world itself is non-sationary!

Our project deals with exploring methods to reduce this sim2real gap and deploy a model trained to do the Reach task in simulaiton on a real Xarm7 robot. Our goal is to deploy the model in the real world, with nothing but images as the current state input.

# Training in Simulation
Training in simulaiton provides many advatages. The most important amongst those are access to state variables instead of just the images. This allows us to speed up training by using [Asym-Actor Ciritic](https://arxiv.org/abs/1710.06542) model.
The Asymmetric Actor Critic uses a [DDPG](https://spinningup.openai.com/en/latest/algorithms/ddpg.html) based approach, where the Actor gets uses inputs but Critic at the time of training gets state based input.

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
