---
title: "Durand-Kerner method"
description: "Root-finding algorithm for solving polynomial equations."
toc: true
authors:
  - luizcarloscf
tags: ["python", "algorithm"]
categories: ["numerical-methods"]
series:
date: 2022-09-02T18:57:51-03:00
featuredImage:
draft: false
---

Is this tutorial, we are going to explore a method that can be used to solve numerically equations, even with complex solutions. It was originally discovered by Karl Weierstrass in 1891, rediscovered independently by Durand in 1960 and Kerner in 1966.

## The algorithm

Given a polynomial $p(x)$,

$$p(x) = ax^4 + bx^3 + cx^2 + dx + e$$


The values $a$, $b$, $c$, $d$ and $e$ are the polynomial coefficients. When solving a polynomial, you know these coefficients and can compute $p(x)$ for all $x$. You can also rewrite it in terms of its roots,

$$p(x) = (x-P)(x-Q)(x-R)(x-S) $$

However, at this point you don't know the values $P$, $Q$, $R$ and $S$ (roots). The algorithm proposes to isolate the equation above in terms of each root,

$$ P = x - \frac{p(x)}{(x-Q)(x-R)(x-S)} $$
$$ Q = x - \frac{p(x)}{(x-P)(x-R)(x-S)} $$
$$ R = x - \frac{p(x)}{(x-P)(x-Q)(x-S)} $$
$$ S = x - \frac{p(x)}{(x-P)(x-Q)(x-R)} $$

If used a fixed point iteration, you can rewrite it as,

$$ P_{n+1} = x - \frac{p(x)}{(x-Q_n)(x-R_n)(x-S_n)} $$
$$ Q_{n+1} = x - \frac{p(x)}{(x-P_n)(x-R_n)(x-S_n)} $$
$$ R_{n+1} = x - \frac{p(x)}{(x-P_n)(x-Q_n)(x-S_n)} $$
$$ S_{n+1} = x - \frac{p(x)}{(x-P_n)(x-Q_n)(x-R_n)} $$

Each $x$ terms is independent from each other in each of the equations above. So, you can choose different $x$'s for each equation,

$$ P_{n+1} = x_p - \frac{p(x_p)}{(x_p-Q_n)(x_p-R_n)(x_p-S_n)} $$
$$ Q_{n+1} = x_q - \frac{p(x_q)}{(x_q-P_n)(x_q-R_n)(x_q-S_n)} $$
$$ R_{n+1} = x_r - \frac{p(x_r)}{(x_r-P_n)(x_r-Q_n)(x_r-S_n)} $$
$$ S_{n+1} = x_s - \frac{p(x_s)}{(x_s-P_n)(x_s-Q_n)(x_s-R_n)} $$

After each iteration, e.g. $P_{n+1}$ is close to the solution. So, we can write $x_p$ as its previous iteration. Which leave us with,

$$ P_{n+1} = P_n - \frac{p(P_n)}{(P_n-Q_n)(P_n-R_n)(P_n-S_n)} $$
$$ Q_{n+1} = Q_n - \frac{p(Q_n)}{(Q_n-P_n)(Q_n-R_n)(Q_n-S_n)} $$
$$ R_{n+1} = R_n - \frac{p(R_n)}{(R_n-P_n)(R_n-Q_n)(R_n-S_n)} $$
$$ S_{n+1} = S_n - \frac{p(S_n)}{(S_n-P_n)(S_n-Q_n)(S_n-R_n)} $$

The final problem here is: how to set the start point to $P_n$, $Q_n$, $R_n$ and $S_n$ that converges to a solution? In the following implementation, we are going to explore a solution that implements the algorithm described with a strategy to select good start points.

## Python implementation

The following python implementation can be downloaded with a Jupyter Notebook [here](/content/posts/durand/durand_kerner.ipynb).

### Start point selection

To select good start points, we will distribute evenly spaced points in a unit circle around the complex plane origin. In some approaches, some authors discuss methods to estimate the circle radius. Here, we will simplify and use only a circle of radius always equal to 1.

```python
def distribute_points(degree: np.ndarray) -> np.ndarray:
  """Distribute initial roots in an unit circle in the complex plane.
  
  Parameters
  ----------
  degree: nd.ndarray
    Polynomial degree.

  Returns
  -------
  result: nd.ndarray
    Complex array with initial roots distributed.
  """
  roots = np.zeros(degree, dtype=complex)
  img = complex(0, 1)
  rad = 1
  phi = 2.0 * np.pi / degree
  for k in range(degree):
    roots[k] = complex(rad, 0) * np.exp(img*complex(phi*k, 0.0))
  return roots
```

To understand how we distribute the points in a complex plane, it is necessary to remember the definition of complex number in the Polar form:

$$z = re^{i \varphi}$$

<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/7/7a/Complex_number_illustration_modarg.svg" />
  <figcaption align="center"> Figure 1 - Complex number illustration.</figcaption>
</p>

Knowing the degree of the polynomial, we know how many roots there are. Then, we compute the step size we should take between $0$ and $2\pi$ to evenly space the initial roots.


### Fixed point iteration

The implementation of Durand-Kerner method using fixed point iteration can be seen below. The following steps can be highlighted:

* Initially, the roots are distributed in a unit circle around the complex plane origin;
* A new value is computed for each root using the initial guess;
* Then, the roots assume the value of the previous step and the process repeats.

```python
def durand_kerner(func,
                  degree: int,
                  iterations: int = 10,
                  epsilon: float = 1.0e-16) -> np.ndarray:
  """Durand-kerner root finding algorithm.

  Parameters
  ----------
  func:
    Callback function to evaluate polynomial.

  degree: int 
    Polynomial degree.

  iterations: int
    Maximmum number of iterations.

  epsilon: float
    Limit to accept convergence.

  Returns
  -------
  np.ndarray
    Founded roots.
  """
  roots = distribute_points(degree=degree)
  n = degree
  new_roots = np.zeros(n, dtype=complex)
  for iterations in range(iterations):
    for k in range(n):
      denominator = complex(1,0)
      for i in range(n):
        if i != k:
          denominator *= (roots[k] - roots[i])
      new_roots[k] = roots[k] - (func(roots[k])/denominator)
    roots[:] = new_roots
  return new_roots

```

### Results

To visualize the results, we plot the polynomial modulus on a graph and the roots found as points highlighted in red. The algorithm in the form proposed here managed to correctly converge the roots of the polynomial. However, some polynomials require better root-initial distribution methods.

<p align="center">
  <img src="/content/posts/durand/result.png" />
  <figcaption align="center"> Figure 2 - Polynomial results in complex plane.</figcaption>
</p>

## Conclusion

The Durand-kerner method provided a simple solution to solve polynomials and the python implementation was pretty much straight forward. However, to make the algorithm more robust good solutions to distribute to initial roots in the complex plane are required.

## References

  * VELIZ, Oscar. Durand-kerner method. Youtube, 2019. Available at: [link](https://www.youtube.com/watch?v=5JcpOj2KtWc)
  * Complex numbers. Wikipedia, 2022. Available at: [link](https://en.wikipedia.org/wiki/Complex_number)