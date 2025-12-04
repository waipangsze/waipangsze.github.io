---
layout: post
title: ML | Automatic Differentiation 
categories: [ML]
tags: [ML, AI, PINN, DeepONet]
author: wpsze
date: 2024-10-22 14:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://upload.wikimedia.org/wikipedia/commons/thumb/3/3c/AutomaticDifferentiationNutshell.png/450px-AutomaticDifferentiationNutshell.png
banner_img: https://upload.wikimedia.org/wikipedia/commons/thumb/3/3c/AutomaticDifferentiationNutshell.png/450px-AutomaticDifferentiationNutshell.png
---

# Automatic Differentiation

In mathematics and computer algebra, automatic differentiation (auto-differentiation, autodiff, or AD), also called algorithmic differentiation, computational differentiation, is a set of techniques to **evaluate the partial derivative of a function specified** by a computer program.

Automatic differentiation exploits the fact that every computer calculation, no matter how complicated, executes a sequence of elementary arithmetic operations (addition, subtraction, multiplication, division, etc.) and elementary functions (exp, log, sin, cos, etc.). By applying the chain rule repeatedly to these operations, partial derivatives of arbitrary order can be computed automatically, accurately to working precision, and using at most a small constant factor of more arithmetic operations than the original program.

- 計算一個函數**導數**的方法
- 自動微分是利用電腦自動化**求導**的技術

# Modes

## Forward Mode

Let's absolutely break down the **Forward Mode** of Automatic Differentiation with a concrete example. This is the simpler of the two main modes, as it follows the natural flow of the function.

We'll use a simple function and trace both the numerical values and the derivatives simultaneously.

### Forward Mode Example

Let's find the derivative of the function $f(x) = \exp(x^2)$ at the point $x=2$.

The function can be broken down into three **elementary operations** that form a computational graph:

$$v_1 = x$$
$$v_2 = v_1^2$$
$$v_3 = \exp(v_2) = f(x)$$

In Forward Mode, for every intermediate variable $v_i$, we calculate two things in parallel:

1.  The **Primal Value** ($v_i$): The actual numerical result of the calculation.
2.  The **Tangent** ($\dot{v}_i$): The derivative of that result with respect to the input variable ($x$), which is $\frac{\partial v_i}{\partial x}$.

We start with our input **seed** value $\dot{v}_1 = \frac{\partial x}{\partial x} = 1$.

---

### **The Computational Trace**

| Step | Operation ($v_i$) | Primal Value ($v_i$ at $x=2$) | Tangent ($\dot{v}_i = \frac{\partial v_i}{\partial x}$) | Tangent Value ($\dot{v}_i$ at $x=2$) |
| :---: | :---: | :---: | :---: | :---: |
| 1 | $v_1 = x$ | $v_1 = 2$ | $\dot{v}_1 = 1$ | **1** |
| 2 | $v_2 = v_1^2$ | $v_2 = 2^2 = 4$ | $\dot{v}_2 = \frac{\partial v_2}{\partial v_1} \cdot \dot{v}_1 = 2v_1 \cdot \dot{v}_1$ | $2(2) \cdot 1 = \mathbf{4}$ |
| 3 | $v_3 = \exp(v_2)$ | $v_3 = \exp(4) \approx 54.6$ | $\dot{v}_3 = \frac{\partial v_3}{\partial v_2} \cdot \dot{v}_2 = \exp(v_2) \cdot \dot{v}_2$ | $\exp(4) \cdot 4 \approx \mathbf{218.4}$ |

The final result is the derivative we were looking for: $$\frac{df}{dx} \bigg|_{x=2} = \dot{v}_3 = 4\exp(4)$$

Notice how at every single step, we used the derivative of the local elementary operation ($v_1^2$, $\exp$) and multiplied it by the derivative of the *previous* step ($\dot{v}_{i-1}$) to apply the **chain rule** exactly. This is what makes AD precise. 

- **Forward Mode** is great when you have few inputs (small $n$) and many outputs, but it's inefficient for training neural networks (which have millions of inputs/parameters).
- The technique that makes deep learning possible is **Reverse Mode AD**, also known as **Backpropagation**.

---

## Reverse Mode

The main reason Reverse Mode is used to train huge neural networks is **efficiency**.

### The Two-Pass System

Reverse Mode works in two distinct passes:

1.  **Forward Pass (Primal Trace):** This is identical to what we did before. We calculate the numerical values ($v_i$) for every intermediate step and store them in memory. We need these intermediate values to calculate the local derivatives in the second pass.
2.  **Reverse Pass (Adjoint Trace):** This is where the magic happens. Starting from the final output ($f$), we apply the chain rule *backwards* through the computational graph to calculate the derivatives.

### The Adjoint and Efficiency

In Reverse Mode, we are calculating the **adjoint** $\bar{v}_i$, which is the derivative of the final output ($f$) with respect to the intermediate variable $v_i$:

$$\bar{v}_i = \frac{\partial f}{\partial v_i}$$

The core benefit is this: regardless of how many input variables ($N$) your function has (a neural network might have millions of $N$), Reverse Mode calculates **all $N$ gradients** in a single backward pass whose complexity is proportional only to the original computation.

Let's trace the reverse pass for our previous function: $f(x) = \exp(x^2)$ at $x=2$.

---

### **The Reverse Computational Trace**

We start at the final output, $v_3$, and set its adjoint (derivative with respect to itself) to 1.

| Step | Intermediate Variable | Local Derivative $\frac{\partial \text{output}}{\partial \text{input}}$ | Adjoint ($\bar{v}_i = \frac{\partial f}{\partial v_i}$) | Adjoint Value (at $x=2$) |
| :---: | :---: | :---: | :---: | :---: |
| **Start** | $\bar{v}_3$ (Output $f$) | N/A | $\bar{v}_3 = 1$ | **1** |
| 2 | $\bar{v}_2$ (Input to $\exp$) | $\frac{\partial v_3}{\partial v_2} = \exp(v_2)$ | $\bar{v}_2 = \bar{v}_3 \cdot \frac{\partial v_3}{\partial v_2}$ | $1 \cdot \exp(4) = \mathbf{\exp(4)}$ |
| **End** | $\bar{v}_1$ (Input $x$) | $\frac{\partial v_2}{\partial v_1} = 2v_1$ | $\bar{v}_1 = \bar{v}_2 \cdot \frac{\partial v_2}{\partial v_1}$ | $\exp(4) \cdot (2 \cdot 2) = \mathbf{4\exp(4)}$ |



The final calculated adjoint, $\bar{v}_1$, gives us the exact derivative of $f$ with respect to the input $x$!

---

## The Efficiency Difference

Here is the efficiency breakdown for a neural network with $N=1$ million parameters (inputs) and $M=1$ output (the loss function):

| Mode | Passes Required | Computational Cost |
| :---: | :---: | :---: |
| **Forward Mode** | $N$ passes (1 million) | $O(N \cdot \text{computation})$ |
| **Reverse Mode** | $M$ passes (1) | $O(\text{computation})$ |

This difference is massive. Forward Mode is $1$ million times slower because it requires a separate pass for every parameter. Reverse Mode calculates the derivative with respect to **all** $N$ parameters in a single backward sweep.

This efficiency is why Reverse Mode AD is the implementation technique used by all modern machine learning frameworks (like PyTorch, TensorFlow, and JAX).

---

## AD Meets Optimization

Now that we have this incredibly efficient, precise gradient (a vector of 1 million partial derivatives) in hand, what does the machine learning model actually **do** with it?

The goal of training is to adjust the 1 million parameters to minimize the error, or **loss function**.

# References

1. [What's Automatic Differentiation?](https://huggingface.co/blog/andmholm/what-is-automatic-differentiation)
2. [自动微分](https://fancyerii.github.io/books/autodiff/)
3. [自动微分](https://indico.ihep.ac.cn/event/17315/attachments/64130/75771/%E8%87%AA%E5%8A%A8%E5%BE%AE%E5%88%86.pdf)
4. [曲率与自动微分](https://vlight.me/2018/04/01/curvature-and-automatic-differentiation/)
5. [自动微分（AutoDiff） - AI4Science的核心基础技术](https://mp.weixin.qq.com/s/s92f217JcU1AiqxC_nMkCA)
