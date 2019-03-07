# Adversarial Learning for 2-level Recommendation System


Denote $X_{user}$ as user information, $L$ as the list of all candidate items (might be a list of embeddings).
We denote a recommendation system as $f_1$, and $P = f_1(L,X_{user};\theta_1)$, where $P_i$ denotes the probability of recommendating $i$-th item. The second level model $f_2$ can either perform a refined recommendation ${P'} = f_2(P,X_{user};\theta_2)$, or an evaluation on $P$: $s=f_2(P,X_{user};\theta_1)$ or any other downstream application based on $P$. Either way, we can define a model $f_2'$, which shares parameters with $f_2$ and serves as a discriminator to help the training of $f_1$.

Specifically, we train the system

$\min_{f_1} \max_{f_2'} D_{f_2'}(f_1(X_{user})||\rho_{X_{user}}) + \lambda \mathcal R(f_1(X_{user}))$

where $\rho_{X_{user}}$ denotes the true training item distribution conditioning on given user information, and $D_{f_2'}$ denotes the neural distance defined by the discriminator $f_2'$. Compared to the traditional GAN loss, in order to contol the number of recommended items, we can add an sparse inducing regularization $\mathcal R$ with a tuning parameter $\lambda$.
For example, $\mathcal R$ can be the entropy of $f_1(X_{user})$. By minimizing $\mathcal R$, we can expect that $f_1(X_{user})$ can output an item distribution with a small entropy, which means the probability concentrates on serveral items.

The above adversarial training can be incorporated with the training of $f_2$ for the downstream application.
