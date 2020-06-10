---
title: "Offline Policy Evaluation and a Case Study in Dialogue System"
date: 2020-06-09T17:00:11-04:00
draft: true
toc: true
toctitle: "Overview"
latex: true
description: "Introduction of offline policy evaluation (ope)"
publishdate: 2020-12-31
---


## Problem Setting

> **Problem Definition**: Evaluate a policy without interaction with environment.
>
> **What do we have**:\
> *(i)* historical data \\(\pi\\): \\( \mathcal{D}=\\{s_i,a_i,r_i,s_i'\\} \\) (state, action, reward) pairs of one or more policies ( \\( \mu_1, \mu_2, \mu_3, ... \\) ) interacting with environment. \
> *(ii)* A target policy \\(\pi\\)
> *(optional)* Expert policies \\( \mu_1, \mu_2, \mu_3, ... \\) may or may not be accessible. In a more general setting, they are not accessible (e.g., human expert).

### Definition

**OPE target**:
$$ \rho(\pi) = \mathbb{E}_{(s,a,r) \sim d^\pi}(r) $$
We will often slightly abuse notation and write \\( (s,a,r),(s,a),(s) \sim d^\pi \\),\\( d^\pi \\) is the *normalized discounted stationary distribution* and is not not avaliable for target policy in OPE.

## Two approaches

### Direct Method

DM directly estimate \\( d^\pi \\) by \\( \widehat{d^\pi} \\) and estimate reward by
$$ \hat\rho(\pi) = \mathbb{E}_{(s,a,r) \sim \widehat{d^\pi}}(r). $$
In dialogue, we can directly use self-play for this, where we also train a customer agent as the world model.

### Importance Sampling (IS)

IS (also called *inverse propensity score*) estimate reward by:sss

$$ \rho(\pi) = \mathbb{E}\_{(s,a,r) \sim {d^\mu}}(r\frac{d^\pi (s,a)}{d^\mu (s,a)}) = \mathbb{E}\_{(s,a,r) \sim {d^\mu}}(rw\_{\pi/\mu}(s,a)), $$


where \\(d^\pi (s,a)=d^\pi (s)\pi(a|s)\\),  \\(w\_{\pi/\mu}(s,a)=\frac{d^\pi (s,a)}{d^\mu (s,a)}\\) is the *discounted stationary distribution correction*.

**\\(\mu\\)  may be not available**: 1.) Estimate old policy by \\(\hat\mu\\), which is not reliable. 2). Estimate \\(w\_{\pi/\mu}\\) 

#### Learning Correction
**TD Method** (temporal difference) estimates the correction based on stationary distribution. TD method is very limited. 
**Dual Dice** Turn the problem into a minimax problem is a better approach for OPE. 

## Doubly Robust Estimation


## Reference
- Doubly Robust Policy Evaluation and Learning
- DualDICE: Behavior-Agnostic Estimation of Discounted Stationary Distribution Corrections
