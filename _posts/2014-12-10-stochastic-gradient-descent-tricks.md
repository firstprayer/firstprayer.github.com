---
layout: post
title: "Tricks in Stochastic Gradient Descent"
description: ""
category: 
tags: []
---



[Stochastic gradient descent(SGD)](http://en.wikipedia.org/wiki/Stochastic_gradient_descent) is frequently used in large scale machine learning and the training of neural networks. Recently I've done a machine learning project which uses [Recursive Neural Network] to do sentiment classification, and we use SGD as our optimizer. 

### Stochastic Gradient Descent

Recall in original batch gradient descent, we go over all the data to compute the gradient:

\begin{equation}
\frac{\partial loss(X, w)}{\partial w} = \frac{1}{n} \sum_{i=1}^{n} \frac{ \partial loss(x_i, w) }{ \partial w}
\end{equation}

And we update the parameter using some learning rate <script type="math/tex">\alpha</script>:

\begin{equation}
w = w - \alpha \frac{\partial loss(X, w)}{\partial w}
\end{equation}

In SGD, it's very similar, except we only use one data instance or a mini-batch of data instances to calculate the gradient:

\begin{equation}
\frac{\partial loss(X, w)}{\partial w} = \frac{1}{k} \sum_{i=1}^{k} \frac{ \partial loss(x_i, w) }{ \partial w}
\end{equation}

with k be 1 or the mini-batch size(e.g. 32 or 64). For simplicity, let's use <script type="math/tex">g_t</script> to denote the gradient computed in time t. 

There're many discussions about choosing the standard batch gradient descent(or other similar methods like L-BFGS) or SGD, and one can think of many reasons to choose SGD over the other. I would just list two:

1.  Convergence is much faster, while the converged solution is comparably good. Particularly useful for large dataset
2.  Helpful to get out of local optimum.

### Adaptive Learning Rate

Let's just cut to the point. The first trick we should use is to use adaptive learning rate. While there may be many ways of doing this (for example you may simply choose to decrease the learning rate monotonically with the iteration number), I recommend using [AdaGrad](http://www.ark.cs.cmu.edu/cdyer/adagrad.pdf).

The basic idea is, instead of using the same learning rate <script type="math/tex">\alpha</script> for all features, AdaGrad provides a per-feature learning rate at time t:

\begin{equation}
\alpha\_{t,i} = \frac{\alpha}{ \sqrt{\sum\_{\hat{t}=1}^{t} g\_{\hat{t},i}^2 }}
\end{equation}

With this the learning rate per feature is monotonically non-increasing, which is the first property that matches we need. Second, intuitively, we accumulate the square of gradients for each gradient, and assign a larger learning rate to features that are not updated frequently enough. It's also very easy to implement. 

### Momentum

Momentum is a very simple idea: at time t when we're calculating the update for w, we take into account the update of w at the last iteration, namely time t-1. Specifically, it works like this


\begin{equation}
	v_t = \mu v\_{t-1} - \alpha g_t ~ , ~ 
	w\_{t+1} = w_t + v_t
\end{equation}

where <script type="math/tex">\mu </script> is the momentum coefficient, controlling how much effect the last update will have on this update. 

Momentum is useful to accelerate the convergence as well as help the network out of local optima.

### Convergence Test

Compare with batch gradient descent, the convergence test for gradient descent suffers from noise. Even if it has already converged, the costs of each iteration will still be fluctuating and have a lot of variance because we're just using a small part of the data. Honestly I didn't figure out a satisfying enough solution in my project, but I'll just give my idea. 

One idea is each time after certain number of iterations (say, every 10 iterations), go through the whole dataset and compute the cost. With these costs computed, we can treat them as costs we have from batch gradient descent, and test whether the function has converged based on this statistics. It will definitely slow the training, so we might consider only do this after we think the function should more or less be closed to convergence (how to do that is another problem...you may, say, start doing this test after the 5th epoch, for example).

Another idea is to use a validation set, which is a small subset from the training set and will never be used during training, and test the accuracy on it every k iterations. If the accuracy hasn't improved for a sufficiently long time, then we think it's converged, and use the function saved back at the time we achieved the highest accuracy. This is what I used in my project and I find it could give results good enough. Still, it feels like cheating, and maybe not that theoratically supported. 

<hr />

Above are basically what I found useful during our training of recursive neural network. Hope it helps someone, and I hope to get some feedbacks or corrections too :)


