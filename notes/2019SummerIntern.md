# 2019 Internship Project

[TOC]

## Toolkit

### Server

```bash
ssh v-hajia@v1011.westus2.cloudapp.azure.com
ssh v-hajia@13.66.230.50

ssh administrator@10.125.164.133
```

### Docker

[Introduction from docker.com](https://docs.docker.com/get-started/)

```bash
docker run -it --rm  -v ~/project:/work/project biglm bash
docker run -it --rm  -v ~/project:/work/project fairseq bash
docker run -it --rm -v ~/project/MNLI:/work/MNLI --privileged tscience.azurecr.io/biglm/biglm:1.12-0.4.1-cuda9.2-py36 bash
```

#### Run a docker image

```bash
docker run -it --rm  -v /local_dir:/docker_dir docker-image:tag bash
```
`-it`: interactive mode
`--rm`: clean up after exit
`-v`: volume (shared filesystems)

#### Attach to a running docker container
```bash
docker ps
docker exec -it container_name bash
```


#### Build a docker image
1. Get folder with everything you need in it.
2. Write a `Dockerfile`
3. `docker build --tag=docker-image .`

#### Cheatsheet
```bash
## List Docker CLI commands
docker
docker container --help

## Display Docker version and info
docker --version
docker version
docker info

## Execute Docker image
docker run hello-world

## List Docker images
docker image ls

## List Docker containers (running, all, all in quiet mode)
docker container ls
docker container ls --all
docker container ls -aq
```





## Project 1: Semi-supervised Learning of MNLI by Generating Labeled Paired Sample using Conditional CycleGAN

### Motivation
Data vs Performance: Data $\uparrow$ $\Rightarrow$ Performance $\uparrow$.
However, labeled data is limited.


### Difficulty
1. Pairing sentences: Conditional CycleGAN
2. Diversity
3. Domain mismatch
4. How to use noisy generated data: [Gang Niu](https://niug1984.github.io/publication.html)

### Ideas
**Dual Learning**: do not find paring, generate paring!!!! Conditional generator: xxx,xxx,xxxxx + contradict --> yyy,yyy,yyyyy [Almost Unsupervised Text to Speech and Automatic Speech Recognition
](https://mp.weixin.qq.com/s?__biz=MzAwMTA3MzM4Nw==&mid=2649447754&idx=1&sn=8ad44ffc9aad1079f8d58585d5aa58e0&chksm=82c0b4ceb5b73dd8334086200cf17685c565a97b7cf09ef046d1d6ddb40ba71a3a1159a6f6c9&mpshare=1&scene=1&srcid=&key=7009efb4b025cbdb67f09288c684aafc595e2741bfa80d5946feb44b33057940301941a2995dc9f1ee19f31832e430fa5df08f98916c741482c3c200d2ccd825a75d0779eabcbef59427a23891ee026a&ascene=1&uin=MjA1OTU0MjcwMg%3D%3D&devicetype=Windows+10&version=62060739&lang=en&pass_ticket=0Y%2F4Wenpbb1a5Mmkv6ITdzYVnPCjNwCSs%2B70la%2Fan8%2B1nMEqHLhsOW9rEw2xwERb)[paper](https://speechresearch.github.io/papers/almost_unsup_tts_asr_2019.pdf)
View as machine teacher.

### Reading List
**Models**
[BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding](https://arxiv.org/abs/1810.04805)
[Improving Language Understanding by Generative Pre-Training](https://blog.openai.com/language-unsupervised/)
[Transformer-XL: Attentive Language Models Beyond a Fixed-Length Context](http://arxiv.org/abs/1901.02860)
[Language Models are Unsupervised Multitask Learners](https://blog.openai.com/better-language-models/)


[Strong Baselines for Neural Semi-supervised Learning under Domain Shift](https://arxiv.org/abs/1804.09530)
[Semi-supervised Learning by Entropy Minimization](http://www.iro.umontreal.ca/~lisa/pointeurs/semi-supervised-entropy-nips2004.pdf)
[Unified Language Model Pre-training for Natural Language Understanding and Generation](https://arxiv.org/pdf/1905.03197.pdf)


**Data Augmentation** (All of these are too heuristic)
[A Surprisingly Robust Trick for the Winograd Schema Challenge](https://arxiv.org/pdf/1905.06290.pdf): select entities in a sentence -> random mask + filtering
[Unsupervised Data Augmentation](https://arxiv.org/pdf/1904.12848): given x -> x'| Training Signal Annealing
[QANET: COMBINING LOCAL CONVOLUTION WITH GLOBAL SELF-ATTENTION FOR READING COMPREHENSION](https://arxiv.org/pdf/1804.09541.pdf): back translation (1m->1.5m, however ours: 1m -> $+\infty$)
[AutoAugment: Learning Augmentation Policies from Data](https://arxiv.org/pdf/1805.09501)

**Semi-Supervised Learning or Weak Supervised Learning**
[PASSAGE RANKING WITH WEAK SUPERVISION](https://128.84.21.199/pdf/1905.05910.pdf)
[Data Programming: Creating Large Training Sets, Quickly](http://papers.nips.cc/paper/6523-data-programming-creating-large-training-sets-quickly.pdf)

### Baselines
**Bootstrapping Methods**
[Self training](https://arxiv.org/abs/1804.09530): Train -> Collect high confident samples -> loop -> no more confident prediction.
[Tri-training](https://arxiv.org/abs/1804.09530): Three models. For i-th model, if the other two agrees the label, then add that sample to the training set of i-th model. We can further share the some parameters between models.


### Data
[Data](https://www.nyu.edu/projects/bowman/multinli/paper.pdf)



























## Project 2: Emsemble Model

**Aim:** Use *less* models in the ensemble to reach equal or *better* performance.
**Idea:** Try to encourage the diversity between the models.

### Boosting
Hardness: boosting is hard for strong models.

### Residual Connection

The next model can be aware of the previous model. It is similar to a simplified wide structure.

### Regularization
To encourage model diversity, we can use add regularization.

**Penalize parameters:** For example, we can use orthogonal regularization to penalize the similarity between the weight matrices of two models. Take linear regression $y = x^\top b$ as a simple example. Assume there is a lot of redundancy in $x$, we can have $b_1$ and $b_2$, where $b_1^\top b_2=0$ and both of them are near optimal. So generally, we can penalize similarity between the weight matrices by the F-norm $||W_1^\top W_2|| $. And then we jointly train these two model. We can just apply these penalties to the bottom layers to encourage diversity. (*I am not sure if this intuition is right*)

**Encourage embedding diversity:** We next consider the embedding space. For a given input $x$, the embedding of two models are $E_1(x)$ and $E_2(x)$. We then try to encourage the diversity embedding by adding regularization. For example, we can use Euclidean optimal transport regularization, i.e., $-\mathbb{E}_x[||E_1(x)-E_2(x)||^2]$. We can just apply these penalties to the bottom layers to encourage diversity.

### Multitask Embedding

We can use a discriminator to differentiate which model is the embedding coming from. And then, we can use multitask learning to encourage the embedding to be different. This needs to be further twisted. Because it may fall into an undesired situation. For example, the dimension of embedding is 10. The last dimension contains the exact model information and has nothing to do with the other part of the model. The other 9 features are exactly the same for different models. The discriminator will fully depend on the last feature. Such a multitask embedding does not help the model diversity.



## Project 3: DecaNLP

[DecaNLP](https://github.com/salesforce/decaNLP) is a multitask challenge that spans ten tasks. Each task is cast as question answering, which makes the training the same as single task training.

@import "decanlp_example.PNG" {height="300px" title="Deca NLP"}

Each sample consists of three parts: Question, Context, and Answer.

**Idea** The basic idea is to consider the heterogeneous encoding/decoding process of different tasks. Following the framework in [MQAN](https://arxiv.org/abs/1806.08730), we have 2 encoders for question and context, and one decoding process with an RNN.

**Encoder** There is little to say about the context encoder. However, we can see a significant difference in the question descriptions. For example, the summarization tasks always have the question: "What is the summary". For the sentiment analysis and pronoun resolution, it is always something like "XXXX A or B". However, the encoding process is using the same encoder. We can try to refine this part by an encoder with different bottoms. Each bottom roughly corresponds to a class of questions. An encoder with 3 bottoms will look like the following picture:
@import "multibottomencoder.png" {height="200px" title="multimodule encoder"}
Here $g1,g2,g3$ are task-specific gated weight. Like if the task always have the question description as "XXXX A or B", we may have $g1=0.9,g2=0.05,g3=0.05$. If the task always has a fix description such as the summary task, we may have $g1=0.05,g2=0.9,g3=0.05$. For any other tasks with general task description we may have $g1=0.05,g2=0.05,g3=0.9$.

**Decoder** The decoding processes, i.e., the answer generating processes are also quite different for different tasks. Roughly speaking, there are mainly three different generating processes: 1. Some pick important parts from Contexts, such as SQuAD and Summarization. 2. Some need to generate answer from vocabulary just like translation. 3. The others basically pick the important part from the Question. We thus can use 3 different parameter sets for the decoder. Just like the encoder part, each task has a soft membership over these 3 classes. We can mix three different decoders by the soft membership.


### Leaderboard

| Model | decaNLP | [SQuAD](https://rajpurkar.github.io/SQuAD-explorer/) | [IWSLT](https://wit3.fbk.eu/mt.php?release=2016-01) | [CNN/DM](https://cs.nyu.edu/~kcho/DMQA/) | [MNLI](https://www.nyu.edu/projects/bowman/multinli/) | [SST](https://nlp.stanford.edu/sentiment/treebank.html) | [QA&#8209;SRL](https://dada.cs.washington.edu/qasrl/) | [QA&#8209;ZRE](http://nlp.cs.washington.edu/zeroshot/) | [WOZ](https://github.com/nmrksic/neural-belief-tracker/tree/master/data/woz) | [WikiSQL](https://github.com/salesforce/WikiSQL) | [MWSC](https://s3.amazonaws.com/research.metamind.io/decaNLP/data/schema.txt) |
| --- | --- | --- | --- | --- | --- | --- | ---- | ---- | --- | --- |--- |
| [MQAN](https://arxiv.org/abs/1806.08730)(Sampling+[CoVe](http://papers.nips.cc/paper/7209-learned-in-translation-contextualized-word-vectors)) | 609.0 | 77.0 | 21.4 | 24.4 | 74.0 | 86.5 | 80.9 | 40.9 | 84.8 | 70.2 | 48.8 |
| [MQAN](https://arxiv.org/abs/1806.08730)(QA&#8209;first+[CoVe](http://papers.nips.cc/paper/7209-learned-in-translation-contextualized-word-vectors)) | 599.9 | 75.5 | 18.9 | 24.4 | 73.6 | 86.4 | 80.8 | 37.4 | 85.8 | 68.5 | 48.8 |
| [MQAN](https://arxiv.org/abs/1806.08730)(QA&#8209;first) | 590.5 | 74.4 | 18.6 | 24.3 | 71.5 | 87.4 | 78.4 | 37.6 | 84.8 | 64.8 | 48.7 |
| [S2S](https://arxiv.org/abs/1806.08730) | 513.6 | 47.5 | 14.2 | 25.7 | 60.9 | 85.9 | 68.7 | 28.5 | 84.0 | 45.8 | 52.4 |
