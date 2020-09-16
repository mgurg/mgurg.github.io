---
layout: post
title: "Numpy i Pandas"
categories: Python
author: "Michał"
math: true
---



Najciekawsze rzeczy, które pomagają mi w pracy z bibliotekami Numpy i Pandas.



# Powtórka

Macierz:

$$
M_{2 \times 3} = \left( \begin{array}{ccc}
x_{11} & x_{12} & x_{12} \\
x_{21} & x_{22} & x_{23} \\
\end{array} \right)
$$
Dodawanie skalaru do macierzy:


$$
2 + \left( \begin{array}{ccc}
1 & 2 \\
3 & 4\\
\end{array} \right) = 
\left( \begin{array}{ccc}
2+1 & 2+2 \\
2+3 & 2+4\\
\end{array} \right) =
\left( \begin{array}{ccc}
3 & 4 \\
5 & 6\\
\end{array} \right)
$$

Dodawanie dwóch macierzy:


$$
\left( \begin{array}{ccc}
1 & 3 \\
5 & 7\\
\end{array} \right) + 
\left( \begin{array}{ccc}
2 & 4 \\
6 & 8\\
\end{array} \right) =
\left( \begin{array}{ccc}
3 & 7 \\
11 & 5\\
\end{array} \right)
$$
Iloczyn skalarny:

```python
# numpy:
np.matmul(a,a)
np.dot(a,a)
```


$$
\left[ \begin{array}{ccc}
0 & 2 & 4 & 6 \\
8 & 10 & 12 & 14\\
\end{array} \right] \cdot  
\left[ \begin{array}{ccc}
1 & 3 & 5 \\
7 & 9 & 11 \\
13 & 15 & 17 \\
19 & 21 & 23\\
\end{array} \right] =
\left[ \begin{array}{ccc}
180 & 204 & 228 \\
500 & 588 & 676\\
\end{array} \right]
$$

$$
[\begin{matrix} 0 & 2 & 4 & 6\end{matrix} ] \cdot [\begin{matrix} 1 & 7 & 13 & 19\end{matrix} ] = 0 \times 1 + 2 \times 7 + 4 \times13 +\ldots = 180
$$

$$
[\begin{matrix} 0 & 2 & 4 & 6\end{matrix} ] \cdot [\begin{matrix} 3 & 9 & 15 & 21\end{matrix} ] = 0 \times 3 + 2 \times 9 + 4 \times 15 +\ldots = 204
$$

$$
[\begin{matrix} 0 & 2 & 4 & 6\end{matrix} ] \cdot [\begin{matrix} 5 & 11 & 17 & 23\end{matrix} ] = 0 \times 5 + 2 \times 11 + 4 \times 17 + \ldots = 228
$$

$$
[\begin{matrix} 8 & 10 & 12 & 14\end{matrix} ] \cdot [\begin{matrix} 1 & 7 & 13 & 19\end{matrix} ] = 8 \times 1 + 10 \times 7 + 12 \times 13 +\ldots = 500
$$

- **Liczba kolumn** w **lewej** macierzy musi być równa **liczbie wierszy** w **prawej** macierzy.

$$
M_{2 \times 3} \cdot M_{3 \times 2} = M_{2 \times 2}
$$

$$
M_{4 \times 3} \cdot M_{3 \times 2} = M_{4 \times 2}
$$



- Matryca wyników ma zawsze taką samą liczbę wierszy jak lewa i taką samą liczbę kolumn jak prawa.
- Kolejność ma znaczenie. Mnożenie A-B nie jest tym samym co mnożenie B-A.

- Dane w lewej macierzy powinny być ułożone jako wiersze, natomiast dane w prawej macierzy jako kolumny.

# Numpy

```python
import numpy as np

s = np.array(5) # scalar

v = np.array([1,2,3]) #vector

m = np.array([[1,2,3], [4,5,6], [7,8,9]]) #matrix 3x3

t = np.array([[[[1],[2]],[[3],[4]],[[5],[6]]],[[[7],[8]],\
    [[9],[10]],[[11],[12]]],[[[13],[14]],[[15],[16]],[[17],[17]]]]) # tensor 3x3x2x1
```



**Reshape:**

```
v = np.array([1,2,3,4])
x = v.reshape(1,4)
x = v.reshape(4,1)
```

[Indexing and slicing numpy arrays](https://www.pythoninformer.com/python-libraries/numpy/index-and-slice/)

[The Ultimate NumPy Tutorial for Data Science Beginners](https://www.analyticsvidhya.com/blog/2020/04/the-ultimate-numpy-tutorial-for-data-science-beginners/)

## Pandas

### Replace

```python
cleanup_nums = {"num_doors":     {"four": 4, "two": 2},
                "num_cylinders": {"four": 4, "six": 6, "five": 5, "eight": 8,
                                  "two": 2, "twelve": 12, "three":3 }}
                                  
obj_df.replace(cleanup_nums, inplace=True)
```