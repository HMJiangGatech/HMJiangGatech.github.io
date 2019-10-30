# Potential Usage of Adversarial Learning in NLP

## 1.Single Task Setting

Here we consider how to apply adversarial learning to improve the performance of a single NLP task.

If there is no external data available, the only scenario I can think of is that for imbalanced data, we can take the adversarial generator as an upsampler of the minority class.

Next we consider the following scenario: only limited task-specific data is available, however, you have abundant auxilary dataset.

### Adversarial Learning for limited task-specific data

Consider a simple NLP model structure: Encoder-Decoder model. Denote $g()$ as encoder, $f()$ as decoder. If the model is used for classification, then $f()$ is a classifier.

The amount task-specific data ($X_{cls},Y_{cls}$) is very limited, while lots of auxiliary unlabeled data ($X_a$) is available. Besides using unsupervised learning (e.g. language model) on $X_a$ to pretrain $g$, we can also incorporate the classification training $\min_{f,g} \ell(f(g(X_{cls})),Y_{cls})$ with an additional adversarial training.
We can expect if the encoding of $X_{cls}$ and $X_a$ are similar, the classifier $f()$ can be generalized to more data. Specifically, we construct an additional classifier $d()$ to identify if the embedding is from $X_{cls}$ or $X_a$:

$\min_{g}\max_{d} D_d(g(X_{cls}), g(X_a))$, where $D_d$ is some kind of neural distance defined by $d$.

**Pseudo Label Trick**
Further more, borrowing ideas from domain adaptation [[1]](https://link.springer.com/chapter/10.1007/978-3-030-01225-0_28), after $g$ and $d$ are well trained. If $d$ identify some $\tilde{X}_a \subset {X}_u$ belongs to $X_{cls}$ with high probability. Then we can assign $\tilde{Y}_a=\arg\max_i f_i(\tilde{X}_a)$ as the pseudo label of $\tilde{X}_a$ to further help the training of the classifier:  $\min_{f,g} \ell(f(g(\tilde{X}_a)),\tilde{Y}_a)$. Alternatively, we can apply this trick to the whole auxilary data by reweighted loss: $\min_{f,g} P_d(X_a)\ell(f(g({X}_a)),{Y}_a)$, where ${Y}_a$ is the pseudo label and $P_d(X_a)$ is the probability of $X_a \in X_{cls}$ identified by $d()$.

**Heterogeneous Auxilary Data**
Next, we consider the scenario that the auxilary data from a heterogeneous distribution (from an other domain). There are two possible solutions:
1. Partial adversarial learning: we separate the encoding $g$ into two parts $g_1$ and $g_2$. We use $g_1$ to capture the common information of $X_{cls}$ and $X_a$ by adversarial learning  and $g_2$ to identify the data source/domain. Because we only care about the performance on the domain $X_{cls}$, we only train $f$ over $X_{cls}$.
@import "partial adv.png" {height="100px" title="partial adv"}
2. However, *pseudo label trick* is not applicable for partial adversarial learning. Instead, we can use adversarial training to learn a mapping from $X_{a}$ to $X_{cls}$: $m(X_a) \simeq X_{cls}$. We further can incorporate a cycle consistant loss to ensure the quality of the mapping. The idea is from cycle-GAN ([[2]](https://junyanz.github.io/CycleGAN/)). The only thing we need to take care of is that generating language data is different from generating images, several tircks might be needed (e.g. policy gradient [[3]](https://arxiv.org/pdf/1704.06933.pdf)).



## 2.Multi-Task Setting

We now consider the multi-task setting. We have $n$ different tasks, and corresponding datasets $X_1,X_2,...,X_n$ which have the associated labels $Y_1,Y_2,...,Y_n$. We still consider the Encoder-Decoder model. The idea of partial adversarial learning is also applicable:
@import "multitask partial adv.png" {height="260px" title="multitask partial adv"}.
However all tasks share one encoder, it might not be enough to capture all the task-specific information. Although $d$ can capture the difference of different data, it knows little about the downstream tasks. So $g_2$ is not able to fully adpated to an individual task. So for each task we can construct an auxiliary encoder, which is not involved in the partial adversarial training.
@import "multitask partial adv v2.png" {height="300px" title="multitask partial adv"}.
Here $f_i$ takes the embedding from $g_c$ and $g_i$ as the input. $d$ and $g_c$ are used in partial adversarial learning.

## Other Reference
[Adversarial Neural Machine Translation](https://arxiv.org/pdf/1704.06933.pdf)
[Adversarial Multi-Criteria Learning for Chinese Word Segmentation](https://arxiv.org/pdf/1704.07556.pdf)
[Language Models are Unsupervised Multitask Learners](https://d4mucfpksywv.cloudfront.net/better-language-models/language_models_are_unsupervised_multitask_learners.pdf)
