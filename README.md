
<!-- 
## Table of Contents

[TOC] -->

## Discovering governing equations from data by sparse identification of nonlinear dynamical systems


## Problem Definition
To discover the govrning equation of (possibly noisy) measurement data of nonlinear dynamical systems. Mathematically speaking, we consider systems that evolve as 

$$
\begin{equation}
\frac{d}{dt} x(t) = f(x(t))
\label{eq:1}
\tag{1}
\end{equation}
$$

The vector  $x(t) \in \mathbb{R}^n$ denotes the state of a system at time $t$, and the function $f(x(t))$ defines equations of motion of the system. Our goal is to identify this function $f$.


## Motivation
A non linear system is a system in which the change in input is not proportional to the change in output. This means that the relationship between the input and output is some unknown function with polynomial, sinusoidal, and logarithmic terms. In case of systems described by multiple variables, the system will be described by a set of such non linear equations. 

Most real-world systems are described by non linear equations, a few examples being financial markets, the spread of disease, and climate patterns. Gaining a clear picture of how these systems evolve and behave is clearly important - in giving us a better picture of what to expect, and in making decisions that better prepare us for the predicted outcomes. 


## Methodology
The paper introduces a method known as Sparse Identification of Nonlinear Dynamics (SINDy). The methodology leverages the fact that most physical systems have only a few relevant terms that govern the system and tries to find these terms using sparsity methods in dynamical systems. 

$f(x(t))$ (refer to $\ref{eq:1}$) is constructed by using $x(t)$ and derivative of $x(t)$ (represented as $\dot{x}(t)$). $x(t)$ is collected over a time interval, and $\dot{x}(t)$ is either measured or approximated numerically using the values of $x(t)$ from the data.

The matrices $X$ and $\dot{X}$ are given by
$$
X = 
\begin{bmatrix}
    x_1(t_1) & x_2(t_1) & \dots & x_n(t_1)\\
    x_1(t_2) & x_2(t_2) & \dots & x_n(t_2)\\
    \vdots & \vdots & \ddots & \vdots \\
    x_1(t_m) & x_2(t_m) & \dots & x_n(t_m)\\
\end{bmatrix}
$$
$$
\dot{X} = 
\begin{bmatrix}
    \dot{x_1}(t_1) & \dot{x_2}(t_1) & \dots & \dot{x_n}(t_1)\\
    \dot{x_1}(t_2) & \dot{x_2}(t_2) & \dots & \dot{x_n}(t_2)\\
    \vdots & \vdots & \ddots & \vdots \\
    \dot{x_1}(t_m) & \dot{x_2}(t_m) & \dots & \dot{x_n}(t_m)\\
\end{bmatrix}
$$
Here, $n$ is the number of features and the data is sampled over time instances from $1$ to $m$.

Next, they take abritrary sets of non linear functions and construct $\theta(X)$. These functions include constant, polynomial, and trignometric terms.
    
$$
\theta(X) = 
\begin{bmatrix}
    \vert & \vert & \vert & \vert &  & \vert & \vert\\
    1 & X  & X^{P_2} & X^{P_3}  & \dots & sin(X) & cos(X)\\
    \vert & \vert & \vert & \vert & & \vert &\vert\\
\end{bmatrix}
$$

$X^{p_2}, X^{p_3}, \dots$ are matrices of higher degree polynomials with non linear terms like $x_1(t_1)\times x_2(t_1), \dots$. The columns of $\theta$ are potential candidates for $f(x(t))$. The authors then setup a sparse regression problem to solve:

$$
\begin{equation}
\dot{X} = \theta(X)\Sigma
\label{eq:2}
\tag{2}
\end{equation}
$$

Here, $\Sigma = \begin{bmatrix} \zeta_1 & \zeta_2 & \dots & \zeta_n\end{bmatrix}$ are the sparse vectors of coefficients which activates non linear terms in $\theta$ for each row ($\dot{X}$ entries).

For each column of $\Sigma$, an optimization algorithm is run to find the suitable activations for corresponding non linear terms.

Often, only $X$ is avaible and $\dot{X}$ has to be derived by taking derivative of $X$. This adds noise to the derivative. The authors suggest different ways to tackle this issue. One such way is by adding an $\eta Z$ term to equation $\ref{eq:2}$.

$$
\begin{equation}
\dot{X} = \theta(X)\Sigma + \eta Z
\label{eq:3}
\tag{3}
\end{equation}
$$

$Z$ is modeled as a matrix of $i.i.d.$ Gaussian entries with zero mean, and noise magnitude $\eta$. 

Generally, physical systems are represented by partial differential equations (PDE). If data is collected from numerical discretization or from experimental measurements, the dimension $n$ of the state may increase significantly. This leads to a factorial growth in the columns of $\theta$. In such cases, dimensionality reduction techniques are used that approximates the system well.

## Summary of results
The authors demonstrated the algorithm on canonical systems, ranging from simple linear and nonlinear oscillator, to noisy measurements of the chaotic Lorenz system, also extending this method to nonlinear Partial Differential Equations and high-dimensional data. 

In each example, this paper explores the ability to identify the dynamics from state measurements alone, without access to derivatives. 

The images below show the model prediction for simple dynamic systems

![](https://i.imgur.com/xhRouJe.png)![](https://i.imgur.com/7PM3KY3.png)


![](https://i.imgur.com/22ke4H3.png)![](https://i.imgur.com/8CjREEf.png)


In the Lorenz example, the ability to capture dynamics on the attractor is more important than the ability to predict an individual trajectory, because chaos will quickly cause any small variations in initial conditions or model coefficients to diverge exponentially. The algorithm not only identifies the correct terms in the dynamics, but it accurately determines the coefficients to within .03% of the true values. The correct dynamics are identified, and the attractor is preserved for surprisingly large noise values. 

![](https://i.imgur.com/6NZd3MB.png)     ![](https://i.imgur.com/dNUjrY1.png)


 
Limitations:
* The sparse identification method relies on a fortunate choice of coordinates and function basis that facilitate sparse representation of the dynamics.
* The algorithm fails to identify dynamics of Lorenz system in non linear coordinates.
* The algorithm identified dynamics of only some of the variables of glycolytic oscillator model.



## Discussion and conclusion
SINDy is a powerful technique to identify nonlinear dynamical systems from data without assumptions on the form of the governing equations. Building on prior work in symbolic regression but with innovations related to sparse regression, algorithms can be scaled to high-dimensional systems. There are numerous fields where this method may be applied, where there are ample data and the absence of governing equations, including neuroscience, climate science, epidemiology, and financial markets. Further, SINDy algorithms can even be appreciated in chemical kinetic models in the human body, in plasma dynamics, non-linear optic systems, or even complex mechanical systems.

A few challenges to the successful implementation of the SINDy algorithm are as listed below:
1.	It relies on having enough clean data of high quality, that is well sampled in time. 
2.	Correct choice of measurement coordinates; how do we know that the variables we measure admit a spare dynamical system?
3.	Choice of sparsifying function basis; given the proper collection of data and the selection of the right coordinates, how do we define a library of functions that provides a system that’s sparse and accurate? 
4.	Out of the available sparse optimization algorithms available, which one do we use to get the sparsest possible model? 

Given that the algorithm fails to identify an accurate sparse model when the measurement coordinates and function basis are not amenable to sparse representation, there may be some hope of obtaining the correct coordinate system and function basis without knowing the solution ahead of time on the belief that we often know something about the physics that guide the choice of function space. The successes of this approach discussed in the paper are mainly in systems, the physics of which, were known beforehand. Regardless, this method has a lot of scope given its robustness and if we are able to get past its challenges, we will have the key to decode systems where data is abundant, but physical laws remain elusive.

