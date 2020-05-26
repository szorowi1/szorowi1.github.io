---
title: "Action Execution in RL, Part 1"
layout: post
date: 2020-05-25 00:00
tag:
  - anxiety, self-efficacy, paper
headerImage: false
description: ""
category: blog
author: szoro
externalLink: true
math: false
---

A paper a day did not pan out. C'est la vie.

Today I want to shift gears and play around with some ideas about self-efficacy theory and reinforcement learning. In self-efficacy theories of anxiety (e.g. Bandura, Barlow), anxiety arises from and is maintained by a *perceived* inability to mitigate anticipated future threats. That is, the anxious individual believes they lack the skills or ability to successfully navigate danger (regardless of their actual skill level or competency).

In a reinforcement learning framework, one way of thinking about self-efficacy is through action selection. That is, can or does an agent learn to make the right (reward-maximizing) choice? Someone with low perceived self-efficacy may believe they will make take the wrong actions (e.g. say the wrong thing) in the future and suffer as a consequence.

There is another way to think about self-efficacy though, which is through action execution. That is, an agent may decide to act in an optimal way but fail in carrying out said action. This introduces new complexity into reinforcement learning processes, both during planning (what is the likelihood an agent will fail to execute an action correctly?) and during updating (how should rewards following execution errors be integrated into value estimates?).

A fascinating line of work from Sam McDougle attempts to answer some of these questions. In [Parvin et al. (2018)](https://www.jneurosci.org/content/38/19/4521.short), the authors measure learning during a reaching bandit task (Experiment 2). The task involves two bandits: a risky bandit, returning a large number of points with low probability, and a safe bandit, returning a small number of points with large probability. (The bandits occasionally reverse during the task to encourage learning.) Of central interest here is the manipulation of instructions between participants. Some participants were told that a lack of reward were due to motor execution failures during reaching (Agency+), whereas the other participants were told that a lack of reward was due to noisiness in the bandits and unrelated to reaching (Agency-).

The authors observed a between groups effect in choice behavior: the Agency+ group preferred the risky option whereas the Agency- group preferred the safe option. The latter finding is consistent with previous decisions from experience tasks, and suggests participants attributed negative feedback to the bandits themselves. In contrast, that the Agency+ group preferred the risky option suggests that those participants attributed negative feedback to motor execution failures, and not to the bandits themselves, thereby finding the risky bandits more valuable.

In a follow-up, [McDougle et al. (2019)](https://doi.org/10.1016/j.cub.2019.04.011) perform a similar task in the fMRI. Fascinatingly, the authors observe an attenuation of the signal associated with negative reward prediction errors in the striatum following execution failures. This provides more direct evidence that, in response to perceived motor execution errors, individuals attribute negative feedback to failures in action execution and do not integrate said feedback into the value of the chosen action.

This line of work provides compelling evidence that learning is sensitive to action execution. More importantly, this line of work suggests that priors beliefs over the success of action execution should have interesting effects on how individuals learn from feedback. For example, individuals with low perceived self-efficacy may be more likely to attribute negative feedback to themselves because they believe they failed act correctly.

To test these ideas, it'd be helpful to first have a model... 
