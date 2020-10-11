---
layout:     post
title:      Law of Large Numbers
date:       2020-05-11
summary:    Part of the 'flashcard' series.
categories: flashcards
mathjax:    true
hidden:     true
---


The average of a sequence of random variables from the same distribution converges to the expected value of that distribution.

$$\frac{1}{N} \sum_{i=1}^N z_{i} \rightarrow E[Z] \quad\text{as } N \rightarrow \infty$$

So long as the expected value is finite.<br/><br/>

#### Sample means converge to expected value, as $$N \rightarrow \infty$$

![convergence to expected value](/images/law_of_large_numbers.png)<br/><br/>

The rate of convergence to the expected value can be calculated using the [standard error of the mean](/flashcards/2020/05/11/standard-error-of-the-mean/).

> TL;DR the sample average converges to the expected value.