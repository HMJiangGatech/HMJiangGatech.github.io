---
title: "Potential Research Direction of Few-Shot Learning with GPT"
date: 2021-03-03T06:09:47-05:00
draft: true
publishdate: 2025-12-31
latex: true
toctitle: "Overview"
toc: true
tags: [nlp,gpt,few-shot,zero-shot]
---


## Background

Current few-shot/zero-shot learning on GPT/BERT basically formulate an NLP task into a model understandable language format. 

For example, 2-shot sentiment analysis with 2 training samples:

- Input: Subpar acting. Label: Negative
- Input: Beautiful film. Label: Positive

We want to know the sentiment of: 

- Input: Amazing.

We construct a *prompt*: 
- `Input: Subpar acting. Sentiment: Negative\nInput: Beautiful film. Sentiment: Positive\nInput: Amazing. Sentiment:` (for lanugage model, e.g., GPT)
- `Input: Subpar acting. Sentiment: Negative\nInput: Beautiful film. Sentiment: Positive\nInput: Amazing. Sentiment: [MASK]` (for masked lanugage model, e.g., BERT)

Other formulation (Examples):
- QA: e.g., `"It is amazing." What is the sentiment?`
- Cloze test: e.g., `It is amazing. [MASK], it is boring.`

There are some important facts about using GPT for few-shot learning [(ref)](https://arxiv.org/abs/2102.09690): *Majority Label Bias*, *Recency Bias*, *Word Popularity Bias*

## Notation

- \\(x\\): Input Sample;
- \\(y\\): Label;
- \\(p(\cdot, \cdot)\\): Prompt format, i.e., turn training samples \\(\\{(x_i,y_i)\\}\\), and test input \\(x_t\\) into prompt;

## Potential Research: Language Models are Self-learner

*Question:* If we can use unlabeled text to improve few-shot learning.

>  Few-shot + Self-training for semi-supervised training

### Series Concatenation

Progressively generate pseudo labels and augment data with pseudo labeled data.

1. Given few-shot training samples \\(\\{(x_i,y_i)\\}\\), and unlabeled samples \\(\\{x_{i}^u\\}\\).
2. Generate label for the 1st unlabeled sample \\(x_1^u\\): \\(y_1^u=\textrm{GPT}\circ p(\\{(x_i,y_i)\\}, x_1^u)\\)
3. Generate label for the 2nd unlabeled sample \\(x_2^u\\): \\(y_2^u=\textrm{GPT}\circ p(\\{(x_i,y_i)\\} \cup \\{(x_1^u, y_1^u)\\}, x_2^u)\\)
4. Generate label for the 3rd unlabeled sample \\(x_3^u\\): \\(y_3^u=\textrm{GPT}\circ p(\\{(x_i,y_i)\\} \cup \\{(x_1^u, y_1^u),(x_2^u, y_2^u)\\}, x_3^u)\\)
5. Repeat
6. Test by \\(\textrm{GPT}\circ p(\\{(x_i,y_i)\\} \cup \\{(x_i^u, y_i^u)\\}, x_t)\\)

*Drawbacks:* 
1. Seqence becomes longer and longer (memeory concern, model positional encoding); 
2. Error accumulation; 

### Parallel Concatenation


Generate pseudo labels for all unlabeled samples at once, and then augment data with pseudo labeled data.

1. Given few-shot training samples \\(\\{(x_i,y_i)\\}\\), and unlabeled samples \\(\\{x_{i}^u\\}\\).
2. Generate label for the \\(i\\)-th unlabeled sample \\(x_i^u\\): \\(y_i^u=\textrm{GPT}\circ p(\\{(x_i,y_i)\\}, x_i^u)\\)
6. Test by \\(\textrm{GPT}\circ p(\\{(x_i,y_i)\\} \cup \\{(x_i^u, y_i^u)\\}, x_t)\\)

*Drawbacks:* 
1. \\(\\{(x_i,y_i)\\} \cup \\{(x_i^u, y_i^u)\\}\\) is too long.

### Series-Parallel Concatenation

Mix Series Concatenation and Parallel Concatenation in some way. E.g.
1. Given few-shot training samples \\(\\{(x_i,y_i)\\}\\), and unlabeled samples \\(\\{x_{i}^u\\}\\).
2. [Parallel] Generate label for the \\(i\\)-th unlabeled sample \\(x_i^u\\): \\(y_i^u=\textrm{GPT}\circ p(\\{(x_i,y_i)\\}, x_i^u)\\)
2. [Series] Update label for the \\(i\\)-th unlabeled sample \\(x_i^u\\): \\(y_i^u=\textrm{Major}_j(\textrm{GPT}\circ p(\\{(x_i,y_i)\\} \cup \\{(x_j^u,y_j^u)\\}, x_i^u))\\)
6. Test by \\(\textrm{GPT}\circ p(\\{(x_i,y_i)\\} \cup \\{(x_i^u, y_i^u)\\}, x_t)\\)

*Drawbacks:* 
1. Exponentially large combination.

### Improvement: Add Filter by Verification

Select pseudo labeled \\((x_i^u, y_i^u)\\) whenever \\(y_i=\textrm{GPT}\circ p((x_i^u, y_i^u), x_i)\\) for all \\(x_i,y_i\\) in the few-shot training sampels.

### Improvement: State Compression for Long Sequence

It might not be easy, as the pre-training is not conducted in the same.

### Improvement: Use the power from multiple LM's

Generate label by ensembling results from multiple LM's

\\(y_i^u=\textrm{GPT}\circ p(\\{(x_i,y_i)\\}, x_i^u)  \Longrightarrow y_i^u=\textrm{Major}_{\textrm{LM} \in \\{\textrm{GPT}, \textrm{BERT},...\\}} \textrm{LM}\circ p(\\{(x_i,y_i)\\}, x_i^u) \\)

### General Framework:
1. Given few-shot training samples \\(\\{(x_i,y_i)\\}\\), and unlabeled samples \\(\\{x_{i}^u\\}\\).
2. [Parallel+Ensemble] Generate label for the \\(i\\)-th unlabeled sample \\(x_i^u\\): \\(y_i^u=\textrm{Major}_{\textrm{LM} \in \\{\textrm{GPT}, \textrm{BERT},...\\}, p \in \\{p_1,p_2,...\\}} \textrm{LM}\circ p(\\{(x_i,y_i)\\}, x_i^u)\\)  
 (\\(\\{p_1,p_2,...\\}\\) corresponds to different prompt format, reording of few-shot samples).
3. [Filter] Select high-quality pseudo-labeled data from all \\(\\{(x_j^u,y_j^u)\\}\\): \\(U\\)
4. [Series+Ensemble] Update label for the \\(i\\)-th unlabeled sample \\(x_i^u\\): 
\\(y_i^u=\textrm{Major}_{\textrm{LM} \in \\{...\\}, p \in \\{...\\}}\textrm{LM}\circ p(\\{(x_i,y_i)\\} \cup \\{(x_j^u,y_j^u)\\} _{j \in U}, x_i^u)\\)
5. Repeat
6. Test by \\(\textrm{Major}_{\textrm{LM} \in \\{...\\}, p \in \\{...\\}}\textrm{LM}\circ p(\\{(x_i,y_i)\\} \cup \\{(x_i^u, y_i^u)\\} _{j \in U}, x_t)\\)

## Related Reference

- Language Models are Few-Shot Learners [arxiv](https://arxiv.org/abs/2005.14165)
- Calibrate Before Use: Improving Few-Shot Performance of Language Models [arxiv](https://arxiv.org/abs/2102.09690)
- AutoPrompt: Eliciting Knowledge from Language Models with Automatically Generated Prompts [arxiv](https://arxiv.org/abs/2010.15980)
- Language Models as Knowledge Bases? [arxiv](https://arxiv.org/abs/1909.01066)
- Language Models are Unsupervised Multitask Learners [url](https://www.semanticscholar.org/paper/Language-Models-are-Unsupervised-Multitask-Learners-Radford-Wu/9405cc0d6169988371b2755e573cc28650d14dfe)