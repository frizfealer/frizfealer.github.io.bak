---
title: "Kalman filter - introduction"
categories:
  - Blog
tags:
  - Statistical learning
  - Algorithm
  - Probabilistic Graphical Models
  - Matrix Algebra
---

# What is Kalman filter?

Kalman filter is an algorithm that models a sequence of measurements. For example, consider a radar system tracking a car. We want to know the position (lets say x and y coordinate) of this car by using the radar system to track. Suppose the radar reports a position each sec, then we are getting a sequence of the car’s position measurements. This is the data we want to model.

However, it is not the end of the story. If the car is moving, it is really useful to predict the car’s position in the next second. Even if the car is not moving, it is still really useful to know the real position of the car because measurements usually have errors. Kalman filter has two variables: states and measurements to distinguish the two. In our example, state means the really x, y location of the car, whereas the measurement means the x, y location reported from the radar system.

high-levelly, there are two phases in the Kalman filter algorithm. One phase predicts the state of the next time step. Another phase estimates the true state of the target. More specifically, in the prediction phase, the algorithm has a function $S_{t+1}' = f_{p}(S_t)$, which takes in the current state and predicts the next one. In the estimation phase, it has a function $S_t = f_e(S'_{t}, M_{t})$, which takes the state prediction from the previous state and measurement in the current time to update the state. The Kalman filter algorithm iterates between the two phases. until it processes all the data.

![Kalman filter process]({{ site.url }}/assets/images/what-is-kalman-fiter/kalman_filter_process.png)

# Relation with Hidden Markov Model

If you are familiar with Hidden Markov Model (HMM), you probably think it sounds like HMM. Indeed, in terms of modeling, they are very similar. They both assume there is a hidden state at each time that generates the measurement. They also both assume the current state prediction depends only on the state in the previous time step. That is the Markov assumption stating$S_{t+1} \perp S_{0:t-1} | S_{t}$. 

![The graphical model for Kalman filter and HMM is the same. X is the hidden state and Z is the measurement. (Image credit: wiki )]({{ site.url }}/assets/images/what-is-kalman-fiter/graphical_model.png)

The graphical model for Kalman filter and HMM is the same. X is the hidden state and Z is the measurement. (Image credit: wiki )

The difference is the hidden state in Kalman filter is continuous instead of discrete. The states are assumed to be normally distributed. Also in Kalman filter, the measurements are assumed to be normally distributed.

# Update and modeling details

The Kalman filter algorithm uses these two equations in the prediction phase.

$$
\begin{aligned}
\hat{\mathbf{x}}_{t+1} &= F \mathbf{x}_{t} \\
\hat{C}_{t+1} &= F P_{t} F^T + Q
\end{aligned}
$$

$\hat{\mathbf{x}}_{t+1}$ means the predicted next state. The first equation shows the next state is a linear transformation (matrix F) from the previous state. The second equation shows the next variance is a respected transformation F, added by a term Q. Q is the noise term, and how to construct it depends on modeling. For example, if you think variables in $\mathbf{x}_t$ are independent, you can make Q a diagonal matrix.

The Kalman filter algorithm uses these two equations in the estimation phase.

$$
\begin{aligned}
\hat{\mathbf{z}}_{t} &= H \hat{\mathbf{x}}_t\\
\mathbf{x}_t &= \mathbf{x}_{t-1} + K_{t} (\mathbf{z}_t - \hat{\mathbf{z}}_{t} ) \\
M_{t} &= (I - K_{t}H)\hat{C}_{t}(I - K_{t}H)^T + K_t R K_t^T
\end{aligned}
$$

Though it looks a bit complex, the things they do is simple (You can refer to this [page](https://www.kalmanfilter.net/kalman1d.html) for 1D version that removes the complexity of the matrix algebra.). First, we use the predicted state from the previous state (hat version) to predict the measurement, $\hat{\mathbf{z}}_t$. Secondly, A Kalman gain, $K_t$, is introduced that can be viewed as a weight between state and measurement. For example, If the measurement, $\mathbf{z}_t$, is more credible, $K_t$ is larger. Please refer to reference 1. for how to compute Kalman gain. Thirdly. a measurement variance, $M_t$, is computed with the similar manner of $\hat{C}_t$. There is also a noise term R. Also the construction of R is a modeling choice.

# Reference

1. [https://www.kalmanfilter.net/modeling.html](https://www.kalmanfilter.net/modeling.html)
2. [https://www.quora.com/How-are-Neural-Networks-and-Kalman-Filters-related](https://www.quora.com/How-are-Neural-Networks-and-Kalman-Filters-related)
3. [https://en.wikipedia.org/wiki/Kalman_filter](https://en.wikipedia.org/wiki/Kalman_filter)