---
layout: post
title: Numerical methods for multidimensional SDEs
permalink: stochastic-solvers
---

Simulating multidimensional stochastic differential equations (SDEs)
is hard because there are relatively few tutorials online and there
are so many different numerical methods to choose from. In this article I'll briefly
introduce stiffness, commutative noise, and the Ito vs. Stratonovich
dilemma. Understanding these will enable us to select the best
numerical solver for your SDE.

## Convergence rates for stochastic solvers

The table below should help you to choose between the three most
common explicit numerical solvers for SDEs. Each value
represents the solver convergence (you want to maximise this!). If
none of the information makes sense, read on and return to this table
later.

| Solver | Interpretation | Convergence |
|--------|:-------------:|:-----------------:|
| [Euler-Maruyama](https://en.wikipedia.org/wiki/Euler%E2%80%93Maruyama_method) | Ito | 0.5 |
| [Milstein](https://en.wikipedia.org/wiki/Euler%E2%80%93Maruyama_method) | Ito/Stratonovich | 1.0 |
| [Euler-Heun](http://www.cwscholz.net/projects/da/node37.html) | Stratonovich | 0.5 (or 1.0 if commutative) |

Follow the links for implementations. Note that all of the methods are
explicit and therefore unsuitable for stiff systems of equations. Turn
to
[Kloeden and Platen](http://link.springer.com/book/10.1007%2F978-3-662-12616-5)
for more on implicit solvers

## Ito vs. Stochastic

*Discaimer: mathematicians, avert your eyes.*

A stochastic differential equation is composed of a **drift**
(deterministic) and a **diffusion** (stochastic) represented by
$A(\vec{x},t)$ and $B(\vec{x},t)$ respectively. This is often written
in the familiar form:

$$
\frac{\mathrm{d}\vec{x}}{\mathrm{d}t} = A(\vec{x},t) +
B(\vec{x},t)\vec{\xi}
$$

This has the advantage of being very intuitive, ignoring the fact that
it doesn't exist mathematically. The solution looks like this:

$$
\vec{x}(t) = \vec{x}(t_0) + \int_{t_0}^t A(\vec{x}, \tau)\mathrm{d}\tau +
\int_{t_0}^t B(\vec{x},\tau)\mathrm{d}W(\tau)
$$

where $W(\tau)$ is a $d$-dimensional Wiener process. Here we hit a
problem: how does one interpret an integral over a random process? The
answer's were provided by Ito and Stratonovich providing two
interpretations,
[read more here](https://en.wikipedia.org/wiki/Stratonovich_integral#Comparison_with_the_It.C3.B4_integral). This
leads to our first rule for choosing a solver:

**Rule 1: know the correct interpretation of your SDE**

## Commutative noise

Whilst there are plenty of tutorials on solving scalar SDEs (thanks to
the finance community for these, eg [link]), there isn't much info on
multi-dimensional. The diffusion matrix $B$ describes how the
noise affects our system. If this matrix has any of the following
structures, it is said to be **commutative** and allows simplified
forms of solvers:

 - Additive noise
 - Symmetric noise
 - Commutativity condition
   ([definition](http://www.sciencedirect.com/science/article/pii/S037704270300829X) eq. 13)

**Rule 2: know the structure of your diffusion term**

## Stiffness

A stiff SDE is a system that exhibits dynamics across a broad range of
timescales. In other words it's largest eigenmode is significantly
larger than it's smallest. You might be familiar with the concept from
ODE numerical methods, and the interpretation for SDEs is
[the same](). If your system is stiff, you'll need to use an implicit
solver rather than the explicit solvers given at the top.

**Rule 3: know the stiffness of your system**

Once you know these three properties of your SDE, go back to the top
and start investigating your options.
