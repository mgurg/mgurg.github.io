---
layout: post
title: "Python: args kwargs"
categories: python
author: "Michał"
---

## arg

Operator * przekazuje do funkcji po jednym argumencie z listy:

```python
args = [1, 2]
my_func(*args)
```

Można to rozpisać jako:

```python
args = [1, 2]
my_func(args[0], args[1])
```

## kwarg

Dla słowników używamy zapisu z dwoma gwiazdkami **  (ang. *keyword argument*)

```python
xgb_params = {
    'max_depth' : 5,
    'n_estimators' : 100,
    'learning_rate' : 0.3,
    'random_state' : 0
}

model = xgb.XGBRegressor(**xgb_params)
```

