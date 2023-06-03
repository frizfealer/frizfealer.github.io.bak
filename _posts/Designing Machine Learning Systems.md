---
title: "Designing Machine Learning Systems - Chapter 1"
date: 2023-03-29
draft: false
categories:
  - Book Note
tags:
  - Machine Learning
  - System Design
  - Software Engineering
---

# Designing Machine Learning Systems

Author: Chip Huyen

Publisher: O'Reilly

Its a reading note for this book

## Chapter 1. Overview Of Machine Learning Systems

### Outline

1. When to use machine learning
2. Understanding machine learning systems
    1. Research vs production
    2. ML system vs traditional software system

### Recall

1. What are the different components of ML systems?
    <details><summary> possible answer</summary>

    infrastructure, data, feature (engineering), ML algorithm, evaluation metrics, deployment, and monitoring continuous updates (CI/CD).

    </detail>
2. What does MLOps mean?  
    <details><summary> possible answer</summary>

    MLOps comes from DevOps, short for development and operations. To operate something means bringing it into production, which includes deploying, monitoring, and maintaining.
    </detail>
3. What does a machine learning solution do?
    <details><summary> possible answer</summary>

    An ML system learns complex patterns from data that can be used to make predictions on unseen data.
    </detail>
4. What’s the difference between a traditional algorithm and an ML algorithm?
    <details><summary> possible answer</summary>

    In the traditional algorithm, you code logic/patterns, whereas the ML algorithm, it learns logic/patterns from data.
    </detail>
5. What are the cases where ML algorithms excel?
    <details><summary> possible answer</summary>

    Wrong predictions are cheap, the ML system will be used a lot (at scale) because the development cost is high. Without ML you cannot do the task.
    </detail>
6. What are some customer ML applications?
    <details><summary> possible answer</summary>

    Recommendation, searching, face recognition. Text autocompletion
    </detail>
7. The majority of ML use cases are still in the enterprise world. What are some enterprise ML applications?
    <details><summary> possible answer</summary>

    Fraud detection, price/cost optimization, demand forecasting, customer acquisition, churn prediction, and internal IT ticket routing predictions, brand monitoring.
    </detail>
8. What are the differences between research ML and production ML? Can answers from Requirements, Computational priority, data.
    <details><summary> possible answer</summary>

    | Research | Research | Production |
    | --- | --- | --- |
    | Requirements | Achieve SOTA on public benchmark | Different stakeholders have different requirements |
    | Computational priority | Fast training, high throughput | Fast inference, low latency |
    | Data | Static, clean | Changing, dirty, need annotation. |
    | Others |  | Might need fairness, explainability |
    </detail>

9. What are some criticisms of ML leaderboards? 
    <details><summary> possible answer</summary>

    1. Hard steps (collecting, cleaning, labeling) are already done for you. Recently data-centric ML, advocated by Andrew Ng has been a buzz term. 
    2. when you have multiple teams testing on the same hold-out test set, a model can do better than the rest just by chance. This is like you are reporting your evaluation number on the validation set ([ref](https://laurenoakdenrayner.com/2019/09/19/ai-competitions-dont-produce-useful-models/)).
    </detail>
10. What does p99 mean in latency measurement?
    <details><summary> possible answer</summary>

    p99 means the 99-percentile of your service latency. It is useful to check this number because it includes the most distribution of service latency.
    </detail>
11. In terms of system design, how is the ML system different from the traditional software system?
    <details><summary> possible answer</summary>

    In traditional SWE, code, and data are separated. Whereas in ML, code, data, and artifacts (model, features) are intertwined. In traditional SWE you versioned code. Whereas in ML, you might also version the data, and models. Large models with high complexity cause long computation times. Monitoring ML systems is harder because of the model's complexity. Cannot run parallelly for each component (model, features).
    </detail>

### SUMMARY
Machine learning (ML) systems can solve problems with complex patterns that are difficult to code. However, this comes at the price of a more complex data processing, service computation, and continuous integration/continuous deployment (MLOps). This chapter also covers common ML system use cases for both customers and enterprises. Finally, the chapter discusses the differences between ML in research and in production.**
