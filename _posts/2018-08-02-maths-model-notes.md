---
title:  "Basics of mathematical modeling-class notes"
excerpt: "7.30 to 8.2 class notes at SIAT"
toc: true
toc_label: "目录"
toc_icon: "gear"
categories:
  - Math
tags:
  - 数学
  - 微积分
---
## Numerical methods for ODEs
### finite difference for initial value problems
example:initial-value Cauchy ODE problem

explicit(forward)/inexplicit(backward) euler method

Lax-Richtmayer equivalence theorem

Runge-Kutta methods

Jacobi method, Newton method

analysis in the space of undetermined coefficients
### convergence, consistency and stability
multistep method

stiff equations
### boundary value problems
#### Crout Algorithm
$$
u = C_1u' + C2u'' + \hat u
$$

(common solution = partial solution of inhomogeneous equation + linear independent solutions of homogeneous equation)

#### Variation methods, Ritz method.
symbols:

$$
(\vec a, \vec g) = a_1g_1 + ... + a_ng_n\\
(u(x),v(x)) = \int_u u(x)v(x)dx\\
\{\phi^n_i\} = \{\phi_1^n , ... ,\phi_n^n\}
$$

n is the number of space's demensions, phi are functions in Hilbert space

functional, linear operator, self-conjugated, positive, Hilbert space

$$
L[u_n] = (A\sum y \phi, \sum y \phi) - 2(\sum y \phi,f)\\
=\sum\sum a_(j,k)y_iy_k-2\sum \beta_1 y_1 \\(a_j,_k = (A \phi_j, \phi_k),\beta_1 = (\phi_1,f))
$$

problem to be solved:

$$
\frac{\partial L[u_n]}{\partial y_i} = 0s
$$

Au = f, L[u_n] is minimal, get u
A is operator, u and f are functions

$$
u_n = \sum^n y \phi
$$

phi is base function, y is coefficient(unknown)

example(2 demension):

$$
(Au,u)-2(u,f) = A(\phi_1y_1+y_2\phi_2)-2(y_1\phi_1+y_2\phi_2,f) \\
=(y_1Af_1+h_2Af_2,y_1\phi_1+y_2\phi_2)-2y_1(\phi_1,f)-2y_2(\phi_2,f)\\
=y_1y_1(A\phi_1,\phi_1)+y_1y_2(A\phi_1,\phi_2)+y_2y_1(A\phi_2,\phi_1)\\+y_2y_2(A\phi_2,phi_2)-2y_1(\phi_1,f)-2y_2(\phi_2,f)
$$
#### Bubnov-Galerkin method
### Nonlinear Systems

Discrete dynamical systems and bifurcations

example: 

$$
u_n+1 = \lambda u_n(1-u_n) = f_u
$$

theorem:if transform y=f(u) is contracted, u=f(u) has a solution v, and v is the target point of the discrete transform u_n+1 = f(u_n) and

$$
|u_n-v| \lt \frac{q^n}{1-q}|u_1-u_0|
$$
## Numerical methods for mathematical models based on the hyperbolic problems
Upwind-scheme(Courant-Issacson-Rees scheme)

Lax-Wendroff scheme

Lax-Friedrichs scheme

example:
## Mathematical models based on the hyperbolic problems
shallow water model.

gas dynamics model.

shock tube problem

impurity propagation in ventilation networks.

transport in the networks.

road traffic

moscow "Sadovoe" ring

waterflood in rivers

PC networks

blood flow
## reference books
1. Runge-Kutta methods wikipedia

2. Numerical Recipes Books On-Line numerical.recipes

3. Leveque R.J. Finite-difference methods for ordinary and partial differential equations

4. Dalziel S. Numerical Methods www.damtp.com.ac.uk/user/fdl/people/sd103

5. Finite element method wikipedia

6. finite volume method wikipedia

