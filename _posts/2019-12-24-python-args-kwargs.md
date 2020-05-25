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
Pozwala przekazywać większą ilość argumentów do funkcji:

```python
def add(a, b, *args):
    total = a + b
    for n in args:
        total += n
    return total

# The length of `args` is 3
print(add(1, 2, 3, 4, 5))
 
# The length of `args` is 0
print(add(1, 2))
```

```python
def will_survive(*names):
    for name in names:
        print("Will", name, "survive?")
 
 
will_survive("Daenerys Targaryen", "Arya Stark", "Brienne of Tarth")
```

```python
print(*"fun")        # f u n
print(*[5, 10, 15])  # 5 10 15
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

Notice the difference: `*args` provides access to a **tuple** of remaining values, while `**kwargs` collects remaining key-value pairs in a **dictionary**.