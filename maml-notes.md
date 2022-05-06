## Literature Study
Back in 2021, I identified a few problems/limitations of MAML which were solved or attempted to solve in follow-up literure.

### Problem: Gradient through Optimization Path
[REPTILE - On First-Order Meta-Learning Algorithms](https://arxiv.org/pdf/1803.02999.pdf) and [FOMAML (MAML paper)](https://arxiv.org/pdf/1703.03400.pdf) solve this problem by dropping/avoiding second order terms.
[iMAML - Meta-Learning with Implicit Gradients](https://arxiv.org/pdf/1909.04630.pdf) solves this by only taking the gradient through the optimal inner estimator (decoupling).

[ES-MAML: Simple Hessian-Free Meta Learning](https://arxiv.org/pdf/1910.01215.pdf) uses evolutionary strategies to avoid Hessian computations.

### Problem: No Uncertainty Estimates
[Probabilistic Model-Agnostic Meta-Learning](https://arxiv.org/pdf/1806.02817.pdf) builds a Bayesian Neural Net (hierarchical model) that enables uncertainty predictions. Motivation are domains where reliability of estimators is crucial (e.g. medical imagery data).
[Bayesian Model-Agnostic Meta-Learning](https://papers.neurips.cc/paper/2018/file/e1021d43911ca2c1845910d84f40aeae-Paper.pdf) propose a Bayesian approach that gives (among other improvements such as robustness etc.) access to uncertainty (due to estimating a posterior distribution over meta-parameters).

Also [Uncertainty in Model-Agnostic Meta-Learning using Variational Inference](https://arxiv.org/pdf/1907.11864.pdf) consider a variational approach, working on a distribution over the meta-parameters rather than fixed values.
### Problem: Meta-Overfitting
[CAVIA - Fast Context Adaptation via Meta-Learning](https://arxiv.org/pdf/1810.03642.pdf) propose updating a low-dimensional set of parameters in a network only at meta-test-time to avoid meta-overfitting. The problem of meta-overfitting is motivated by [A simple neural attentive meta-learner](https://arxiv.org/pdf/1707.03141.pdf) which found cases in which MAML "overfits tremendously" (see Appendix B - ablation study). They do not motivate their own approach with avoiding overfitting in MAML but notice that their attention + temporal convolution approach helps with the problem (see more below).

[Meta-SGD](https://arxiv.org/pdf/1707.09835.pdf) also address overfitting in an interesting way. Instead of just meta-learning the "initialization" of the parameters for a new adaption, they also learn learning rates and update direction for a new learner. This method is also very interesting when comparing update directions (since it is learned!)

[Bayesian Model-Agnostic Meta-Learning](https://papers.neurips.cc/paper/2018/file/e1021d43911ca2c1845910d84f40aeae-Paper.pdf) address this problem as well, mentioning the tendency of MAML to overfit and aim to provide a more robust meta-learning approach.

### Problem: Gradient-Based Adaption, Not Task-Specific
[A simple neural attentive meta-learner](https://arxiv.org/pdf/1707.03141.pdf) argue that a lot of meta-learning strategies (MAML, LSTM-based etc.) require gradient-based adaption which might allow for domain independent usage but specific domains might require specific strategies for adaption that are not apparant to researchers. They propose an archtiecture using attention and temporal convolutions (exploiting past experience efficiently).

### Problem: Model-Bias Towards Meta-Learned Tasks
[TAML - Task-Agnostic Meta-Learning for Few-shot Learning](https://arxiv.org/pdf/1805.07722.pdf) is a paradigm for removing bias from meta-learned tasks by constructing a model with maximum uncertainty w.r.t to the training tasks. Results seem to be competitive on Omniglot and miniImagenet compared to MAML, Meta-SGD and even [Snail](https://arxiv.org/pdf/1707.03141.pdf).

### Problem: Only Initialization Parameters Learned
The idea is as follows: MAML learns a good initialization parameter but there are more components involved in gradient-descent. As described above, [Meta-SGD](https://arxiv.org/pdf/1707.09835.pdf) also learn learning rate and update direction. Furthermore, classic approaches like the Newton method also use curvature to obtain better updates. [Meta-Curvature](https://arxiv.org/pdf/1902.03356.pdf) takes this into account and also learns a curvature matrix. However, this does not require third-order derivatives, since they decouple updating the meta-initializtation and the meta-curvature parameter updates. Its a nice paper with very good results on Omniglot and miniImagenet.

[PAMELA - Meta-learning the Learning Trends Shared Across Tasks](https://arxiv.org/pdf/2010.09291.pdf) go even further and also learn trends between update steps and relate different task-specific optimizers to one another while doing the inner update. They perform very good on Omniglot and miniImagenet.

### Problem: Task Heterogeneity
[HSML - Hierarchically Structured Meta-Leaning](https://faculty.ist.psu.edu/jessieli/Publications/2019-ICML-meta-learning-structured.pdf) argue that MAML tailors optimization to closely to the sampled training tasks by combining optimal parameters for each task into one meta-parameter. They further say that by organizing similar tasks into clusters, they can generalize better.

[OSML - Online Structured Meta-learning](https://www.researchgate.net/profile/Huaxiu-Yao/publication/344828393_Online_Structured_Meta-learning/links/5f976f01299bf1b53e497a60/Online-Structured-Meta-learning.pdf) organize tasks in a meta-hierarchical graph to improve generalization and fast adaption. This approach compares well to existing online meta-learners. Note that is does not compete with MAML, Meta-SGD etc. as it is proposed as an online scheme.

On a different note [Toward Multimodal Model-Agnostic Meta-Learning](https://arxiv.org/pdf/1812.07172.pdf) argue that parameter posteriors might be multi-modal and as such require different initializations. While this fundamentally disagrees with the usual prior: Similar tasks lead to similar optimal initialization (and learning rate, update direction etc.), they show that their model outperforms multiple MAMLs represententing the different modes. Their method is also competitive with state-of-the-art methods on Omniglot and miniImagenet. They refined their method in [Multimodal Model-Agnostic Meta-Learning via Task-Aware Modulation](https://arxiv.org/pdf/1910.13616.pdf).

These methods are further extended by [MALTML - Model-Agnostic Learning to Meta-Learn](https://arxiv.org/pdf/2012.02684.pdf) which argue that assuming tasks comming from the same distribution is unrealistic and therefore (and correct me if I'm wrong here) propose to Meta-learn MAML, i.e. meta-learn to quickly adapt to a new task dsitribution. They also introduce the notion of few-task learning. And yes, that introduces a third-order gradient, which they then approximate first-order. Their results are preliminary and though they do work well on the 1D sinusiod setting (at least slightly better than MAML), I cannot identify any more expressive results at this point in time. 

Lastly, 2021 paper [TreeMAML - Meta-Learning withMAML on Trees](https://arxiv.org/pdf/2103.04691.pdf) proposes to exploit task hierarchy by taking gradients for subsequent steps from clusters of tasks, wich are organized in a tree structure. It shows significant improvement of loss on synthetic data and NLI (natural language inference - determining the correctness of a sentence) datasets across languages (which can be structured in a tree). 

### Problem: No Continuous Learning Possible
MAML and its extensions typically assume that all task data is available at once and that the learner learns once and can then quickly adapt. [Finn et al. 2019 - FTML - Online Meta-Learning](https://arxiv.org/pdf/1902.08438.pdf) propose a meta-learner that works online.
[OSML - Online Structured Meta-learning](https://www.researchgate.net/profile/Huaxiu-Yao/publication/344828393_Online_Structured_Meta-learning/links/5f976f01299bf1b53e497a60/Online-Structured-Meta-learning.pdf) try to improve FTML by using hierarchical task structure (see above).

### Other Cool Stuff
[Meta-Learning Probabilistic Inference For Prediction](http://arxiv.org/abs/1805.09921.pdf) propose a unified framwork that generalizes (among others) [Probabilistic Model-Agnostic Meta-Learning](https://arxiv.org/pdf/1806.02817.pdf) by Finn et al.

[MAML++ - How to train your maml](https://arxiv.org/pdf/1810.09502.pdf) work on commong practical pitfalls with MAML training.
