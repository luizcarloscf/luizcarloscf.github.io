---
title: "Durand-Kerner method"
description: "Root-finding algorithm for solving polynomial equations."
date: 2022-09-02
categories: ["numerical-methods"]
tags: ["python", "algorithm"]
draft: false
---

{{< katex >}}

The **Durand–Kerner method**, also known as the **Weierstrass method**, is a powerful algorithm for numerically finding **all roots of a polynomial simultaneously**, including complex roots. Originally introduced by **Karl Weierstrass (1891)** and rediscovered decades later by **Durand (1960)** and **Kerner (1966)**, the method remains important in computational algebra and scientific computing.

## Algorithm

Given a polynomial \( p(x) \) of degree \( n \), e.g.

$$ p(x) = ax^4 + bx^3 + cx^2 + dx + e $$

where, the values \(a, b, c, d, e\) are known coefficients. Although polynomials can be expressed in terms of their roots,

$$p(x) = (x-P)(x-Q)(x-R)(x-S) $$

where, the root values \(P, Q, R, S\) are unknown and must be computed. The Durand–Kerner method starts from the idea of isolating each root:

$$ P = x - \frac{p(x)}{(x-Q)(x-R)(x-S)} $$
$$ Q = x - \frac{p(x)}{(x-P)(x-R)(x-S)} $$
$$ R = x - \frac{p(x)}{(x-P)(x-Q)(x-S)} $$
$$ S = x - \frac{p(x)}{(x-P)(x-Q)(x-R)} $$

Using fixed-point iteration, we update each root estimate:

$$ P_{n+1} = x - \frac{p(x)}{(x-Q_n)(x-R_n)(x-S_n)} $$
$$ Q_{n+1} = x - \frac{p(x)}{(x-P_n)(x-R_n)(x-S_n)} $$
$$ R_{n+1} = x - \frac{p(x)}{(x-P_n)(x-Q_n)(x-S_n)} $$
$$ S_{n+1} = x - \frac{p(x)}{(x-P_n)(x-Q_n)(x-R_n)} $$

Because each expression is independent, each root uses its own value as the next iteration point:

$$ P_{n+1} = P_n - \frac{p(P_n)}{(P_n-Q_n)(P_n-R_n)(P_n-S_n)} $$
$$ Q_{n+1} = Q_n - \frac{p(Q_n)}{(Q_n-P_n)(Q_n-R_n)(Q_n-S_n)} $$
$$ R_{n+1} = R_n - \frac{p(R_n)}{(R_n-P_n)(R_n-Q_n)(R_n-S_n)} $$
$$ S_{n+1} = S_n - \frac{p(S_n)}{(S_n-P_n)(S_n-Q_n)(S_n-R_n)} $$

This produces a simultaneous root-refinement scheme: each iteration updates all root estimates based on the current ones. A practical challenge, however, is the choice of initial guesses. The method converges rapidly if roots start near their true values, but how should they be chosen? In the following implementation, we are going to explore a solution that implements the algorithm described with a strategy to select good start points.

## Python implementation

The following python implementation can be downloaded with a Jupyter Notebook [here](durand_kerner.ipynb).

### Start point selection

A common strategy is to distribute initial values evenly around a circle in the complex plane. In some approaches, some authors discuss methods to estimate the circle radius. Here, we will simplify and use only a circle of radius always equal to 1.

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

This comes from the polar representation of complex numbers:

$$z = re^{i \varphi}$$

<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/7/7a/Complex_number_illustration_modarg.svg" />
  <figcaption align="center"> Figure 1 - Complex number illustration.</figcaption>
</p>

Knowing the degree of the polynomial, we know how many roots there are. Then, we compute the step size we should take between \(0\) and \(2\pi\) to evenly space the initial roots.

### Fixed point iteration

The implementation of Durand-Kerner method using fixed point iteration can be seen below. The following steps can be highlighted:

1. Initialize root guesses around the complex unit circle.
2. For each iteration:
    * Update each root using the Durand–Kerner formula.
    *  Replace old roots with new approximations.
3. Stop when convergence tolerance or maximum iterations is reached.

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

Below, the computed roots are plotted as red points on the complex plane on top of the magnitude surface of the polynomial:

<p align="center">
  <img src="result.png" />
  <figcaption align="center"> Figure 2 - Polynomial results in complex plane.</figcaption>
</p>

With the selected initialization strategy, the method converges correctly. Some polynomials, especially those with clustered or multiple roots, may require refined initialization strategies for reliable convergence.

## Conclusion

The Durand–Kerner method is a simple, elegant algorithm for locating all roots of a polynomial simultaneously, including complex ones. With appropriate initial values, it converges rapidly and can be implemented in only a few lines of Python. However, to make the algorithm more robust good solutions to distribute to initial roots in the complex plane are required.

## References

  * VELIZ, Oscar. Durand-kerner method. Youtube, 2019. Available at: [link](https://www.youtube.com/watch?v=5JcpOj2KtWc)
  * Complex numbers. Wikipedia, 2022. Available at: [link](https://en.wikipedia.org/wiki/Complex_number)