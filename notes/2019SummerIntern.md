# 2019 Internship Project

[TOC]

## Toolkit

### Server
```bash
ssh v-hajia@v1012.westus2.cloudapp.azure.com
```

### Docker
[Introduction from docker.com](https://docs.docker.com/get-started/)
#### Run a docker image
```bash
docker run -it --rm  -v /local_dir:/docker_dir docker-image:tag bash
```
`-it`: interactive mode
`--rm`: clean up after exit
`-v`:

#### Cheatsheet









## Project 1: DecaNLP

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
