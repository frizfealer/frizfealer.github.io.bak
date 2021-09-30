---
title: "Intuitive understanding of prior, likelihood, posterior probability"
last_modified_at: 2021-03-04
categories:
  - Blog
tags:
  - machine learning
  - probability
---

For me, the concepts of prior probability, likelihood probability, and posterior probability are easily confused. Therefore, I write this blog to make a note to myself as well as sharing my knowledge.

Consider throwing a coin, let the probability of head on the top be θ. We want to know if the θ = 0.5 (If the coin is of good quality, the θ would be 0.5).

In order to verify that, we do the following experiment: We throw this coin 100 times, and records each result. We have a record X = [x1, x2, ..., x100]
The probability of this record occurs p(X|θ) = p(x1 | θ) * p(X2 | θ) * ... p(x100 | θ). 