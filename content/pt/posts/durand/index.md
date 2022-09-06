---
title: "Algoritmo de Durand-Kerner"
description: "Algoritmo para encontrar as raízes de equações polinomiais."
toc: true
authors:
  - luizcarloscf
tags: ["python", "algoritmos"]
categories: ["algoritmos-numéricos"]
series:
date: 2022-09-02T18:57:51-03:00
featuredImage:
draft: false
---

Neste tutorial, iremos explorar um método numérico que pode ser utilizado para solucionar equações polinomiais numericamente, até mesmo com soluções complexas. Foi originalmente descoberto por Karl Weierstrass em 1891, redescoberto independentemente por Durand em 1960 e Kerner em 1966.

## O algoritmo

Dado um polinômio $p(x)$,

$$p(x) = ax^4 + bx^3 + cx^2 + dx + e$$

Os valores $a$, $b$, $c$, $d$ e $e$ são os coeficientes do polinômio. Quando solucionando uma equação polinomial, estes valores são conhecidos e é possível de se computar $p(x)$ para todo $x$. Pode-se também reescrever $p(x)$ em função de suas raízes,

$$p(x) = (x-P)(x-Q)(x-R)(x-S) $$

Entretando, você não conhece os valores $P$, $Q$, $R$ e $S$ (raízes do polinômio). O algoritmo propõe isolar a equação acima em função de cada raíz,

$$ P = x - \frac{p(x)}{(x-Q)(x-R)(x-S)} $$
$$ Q = x - \frac{p(x)}{(x-P)(x-R)(x-S)} $$
$$ R = x - \frac{p(x)}{(x-P)(x-Q)(x-S)} $$
$$ S = x - \frac{p(x)}{(x-P)(x-Q)(x-R)} $$

Se usado o método de iteração de ponto fixo, podemos reescrever como,

$$ P_{n+1} = x - \frac{p(x)}{(x-Q_n)(x-R_n)(x-S_n)} $$
$$ Q_{n+1} = x - \frac{p(x)}{(x-P_n)(x-R_n)(x-S_n)} $$
$$ R_{n+1} = x - \frac{p(x)}{(x-P_n)(x-Q_n)(x-S_n)} $$
$$ S_{n+1} = x - \frac{p(x)}{(x-P_n)(x-Q_n)(x-R_n)} $$

Cada termo $x$ é independente um dos outros em cada uma das equações acima. Então, podemos escolher diferentes $x$'s para cada equação,

$$ P_{n+1} = x_p - \frac{p(x_p)}{(x_p-Q_n)(x_p-R_n)(x_p-S_n)} $$
$$ Q_{n+1} = x_q - \frac{p(x_q)}{(x_q-P_n)(x_q-R_n)(x_q-S_n)} $$
$$ R_{n+1} = x_r - \frac{p(x_r)}{(x_r-P_n)(x_r-Q_n)(x_r-S_n)} $$
$$ S_{n+1} = x_s - \frac{p(x_s)}{(x_s-P_n)(x_s-Q_n)(x_s-R_n)} $$

Depois de cada iteração, $P_{n+1}$ (e também as outras raízes) está mais próximo da solução. Então, podemos reescrever $x_p$ como o seu valor anterior. O que nos deixa com, 

$$ P_{n+1} = P_n - \frac{p(P_n)}{(P_n-Q_n)(P_n-R_n)(P_n-S_n)} $$
$$ Q_{n+1} = Q_n - \frac{p(Q_n)}{(Q_n-P_n)(Q_n-R_n)(Q_n-S_n)} $$
$$ R_{n+1} = R_n - \frac{p(R_n)}{(R_n-P_n)(R_n-Q_n)(R_n-S_n)} $$
$$ S_{n+1} = S_n - \frac{p(S_n)}{(S_n-P_n)(S_n-Q_n)(S_n-R_n)} $$

O problema final aqui é: como escolher os pontos iniciais para $P_n$, $Q_n$, $R_n$ e $S_n$ que converge para uma solução? Na seguinte implementação, iremos explorar uma solução que implementa um algoritmo com uma estratégia para selecionar bons pontos iniciais para as raízes.

## Implementação em Python

A seguinte implementação em python pode ser baixada como um Jupyter Notebook [aqui](/content/posts/durand/durand_kerner.ipynb).

### Selecionando os pontos iniciais

Para selecionar bons pontos iniciais, distribuímos os pontos igualmente espaçados em um circulo unitário ao redor da origem do plano complexo. Em algumas abordagens, alguns autores discutem métodos para também estimar o raio deste circulo ao redor da origem. Aqui, iremos simplificar e usar apenas um circulo de raio sempre igual a 1.

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
Para entender como distribuímos os pontos no plano complexo, é necessário lembrar da definição de número complexo na forma polar:

$$z = re^{i \varphi}$$

<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/7/7a/Complex_number_illustration_modarg.svg" />
  <figcaption align="center"> Figura 1 - Ilustração de um número complexo.</figcaption>
</p>

Conhecendo o grau do polinômio, sabemos quantas raízes este possui. Então, computamos o tamanho do passo necessário que deve ser dado entre $0$ e $2\pi$ para espaçar igualmente as raízes iniciais.

### Iteração de ponto fixo

A implementação do método de Durand-Kerner usando iteração de ponto fixo pode ser visualizada abaixo. Os seguintes passos podem ser destacados:

* Inicialmente, as raízes são distribuídas em um círculo unitário ao redor da origen do plano complexo;
* Um novo valor para cada raíz é computado usando os chutes iniciais;
* Então, as raízes assumem os valores calculados no passo anterior e o processo se repete.

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

### Resultados

Para visualizar os resultados, graficamos o módulo do polinômio e as raízes encontradas como pontos vermelhos. O algoritmo na forma proposta aqui conseguiu convergir corretamente para as raízes do polinômio. Entretanto, alguns polinômios requerem um melhor método de distribuição das raízes inicialmente para convergir.

<p align="center">
  <img src="/content/posts/durand/result.png" />
  <figcaption align="center"> Figure 2 - Raízes do polinômio no plano complexo.</figcaption>
</p>

## Conclusão

O método de Durand-Kerner forneceu uma solução simples para resolver equações polinomiais e a implementação em Python foi bem direta. Entretanto, para tornar o algoritmo mais robusto soluções melhores para distribuir as raízes inicialmente no plano complexo é essencial.

## Referências

  * VELIZ, Oscar. Durand-kerner method. Youtube, 2019. Disponível em: [link](https://www.youtube.com/watch?v=5JcpOj2KtWc)
  * Complex numbers. Wikipedia, 2022. Disponível em: [link](https://en.wikipedia.org/wiki/Complex_number)