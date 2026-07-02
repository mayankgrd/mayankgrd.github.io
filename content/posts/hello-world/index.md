---
title: "Hello, world"
date: 2026-07-01
draft: false
tags: ["meta"]
math: true
---

Welcome to the rebuilt site. This post exists to demonstrate that math,
diagrams, and code all render correctly.

## Math

The loss for a linear regression model:

$$
\mathcal{L}(\theta) = \frac{1}{n} \sum_{i=1}^{n} \left( y_i - \theta^\top x_i \right)^2
$$

Inline math like $e^{i\pi} + 1 = 0$ works too.

## Diagram

```mermaid
graph LR
    A[Write markdown] --> B[Preview locally]
    B --> C[Publish]
    C --> D[Live on GitHub Pages]
```

## Code

```python
def mse(y_true, y_pred):
    return ((y_true - y_pred) ** 2).mean()
```
