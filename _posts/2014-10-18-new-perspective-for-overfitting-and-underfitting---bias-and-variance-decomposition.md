---
layout: post
title: "New perspective for Overfitting and Underfitting -- Bias and Variance Decomposition"
description: "Bias-Variance is a mathematical way to understand what underfitting and overfitting mean. In practise we should find a balance point to avoid either bias or vairance being too large."
category: "machine-learning"
tags: ["computer-science"]
---

<style type="text/css">
	img.img-medium{
		max-width: 300px;
	}

	img.img-large{
		max-width: 500px;
	}
</style>
<script type="text/javascript"
  src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>
<script type="text/javascript"
   src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>

You probably all heard of overfitting and underfitting. You might also heard of [Occam's razor](http://en.wikipedia.org/wiki/Occam's_razor), which is borrowed from philosophy as a preference for simpler model than complex model in machine learning to avoid overfitting. However, these concepts either sound too empirical or too philosophical. [Bias and Variance decomposition](http://en.wikipedia.org/wiki/Bias%E2%80%93variance_tradeoff) is a mathemetical approach for the analysis of this problem.
So how does it work? Let's get an intuitive understanding of it by looking at the polynomial regression problem. 

<img src="/images/bias-variance-1.png" class="img-medium">

The blue curve is a polinomial curve with k=5, representing a data distribution. The blue "*"s are points sampled from the distribution with a gaussian noise. And if we use linear regression to fit these data points we will get the red line. 

If there're multiple datasets sampled from the same data distribution, then we might expect such results from fitting a linear model:

<img src="/images/bias-variance-3.png">

On the other hand, if we use a k larger than 5 (say, 7), we might fit a curve like this:

<img src="/images/bias-variance-2.png" class="img-medium">

Again, given multiple datasets from the same distribution, we might get this for the same k:

<img src="/images/bias-variance-4.png">

From these figures we can see that the linear model is deviated from the data points a lot, but the results we get from different sampled data are very similar; on the other hand, a polynomial curve with a large k might fit the data points quite well, but each time we run it on a different sampled of the same distribution we might get a very different result. 

That's the basic intuition of Bias-Variance decomposition(or Bias-Variance Tradeoff). We say that **linear model has large bias but small variance**, while the large-k-polinomial model has **small bias but small variance**. By doing some maths, it shows that on average, this two terms add up to the error of our model on average. This means if either of bias and variance is big, we are probably not getting a satisfying model.

### Here comes the math
Now let's use Y to represent the true value of all points, <script type="math/tex">f^*(x)</script> to represent the underlying data distribution model we can get (since there're noises so it's different from Y). And f(x) is the model we fit. The first step of Bias-Variance decomposition tells us:

<hr/>
<script type="math/tex">
E[(f(X) - Y)^2] = E[(f(X) - f^*(X))^2] + E[(f^*(X) - Y)^2]
</script>
<hr/>

The term on the left of the equation means how much the model we fit is different from the observed values of data points on average. It turns out to be the summation of two terms: how much the model we fit is different from the underlying model, and how much the observed values differ from the data distribution(again, because of noises). 

The next paragraph is the proof. If not interested can skip it:

<hr/>
**Proof:**

<script type="math/tex">
E[(f(X) - Y)^2] = E[(f(X) - E(Y|X) + E(Y|X) - Y)^2]
</script>
<script type="math/tex">
E[(f(X) - E(Y|X) + E(Y|X) - Y)^2] = E[(f(X) - E(Y|X))^2] + 2E[(f(X) - E(Y|X))(E(Y|X) - Y)] + E[(E(Y|X) - Y)^2]
</script>

And

<script type="math/tex">
E_{XY}[(f(X) - E(Y|X))(E(Y|X) - Y)] = E_{X}[E_{Y|X}[(f(X) - E(Y|X))(E(Y|X) - Y)]] = E_{X}[(f(X) - E(Y|X))(E(Y|X) - E(Y|X))] = 0
</script>

<script type="math/tex">
E(Y|X) = f^*(X)
</script>

So we have <script type="math/tex">E[(f(X) - Y)^2] = E[(f(X) - f^*(X))^2] + E[(f^*(X) - Y)^2]</script>

<hr/>

The term <script type="math/tex">E[(f^*(X) - Y)^2]</script> is beyond our reach, since we can do nothing about it, so let's focus on what we can do: we can further decompose the term <script type="math/tex">E[(f(X) - f^*(X))^2]</script>. 

Let's just skip to the conclusion(the proof is similar to the previous one):

<hr/>
<script type="math/tex">
E_{X,Y,D_n}[ (\hat{f_n}(X) - f^*(X))^2 ] = E_{X,Y,D_n}[(\hat{f_n}(X) - E_{D_n}[ \hat{f_n}(X) ])^2 ] + E_{X,Y,D_n}[(E_{D_n}[ \hat{f_n}(X) ] - f^*(X))^2 ]
</script>
<hr/>

Here <script type="math/tex">\hat{f_n}</script> is the model we train on Dataset <script type="math/tex">D_n</script>. Let's take a look at the two terms on the right side of the equation. The first term measures the variance of the models we get each time(**Variance**), the second term measures that on average, how far our models are from the groundtruth(**Bias**).

Now let's see how can this guide us through machine learning practise. Bias is how well the model approximates the groundtruth(represented by the observed data). A simpler model (linear model in the previous example) will have larger bias. When the bias is always too big, then the model is underfitting, which means it doesn't have enough flexibility to capture the right model. On the other hand, when we choose a model that's too flexible, it may be fitting the data points very well, but a small change of the dataset could produce a completely different model, so the variance would be large. Neither is good. In practise, we should try to find a balance. The figure below could better illustrate the idea:

<img src="/images/bias-variance-5.png" class="img-large">

Similar figures might also be used to illustrate the idea of overfitting. From my perpectives, it's just a different way to represent the same idea.

**Summary**: Bias-Variance is a mathematical way to understand what underfitting and overfitting mean. In practise we should find a balance point to avoid either bias or vairance being too large. 

**Reference**: Figures and initial equations come from the the lecture slides of [Prof. Singh](http://www.cs.cmu.edu/~aarti/) in Carnegie Mellon University.

