---
layout: post
title: "Python: Matplotlib i seaborn"
categories: Linux
author: "Michał"
---



Matplotlib, Seaborn, Bokeh, Plotly - w Pythonie jest w czym wybierać jeżeli chodzi o biblioteki do wizualizacji.

# Matplotlib

Zapis do pliku:

```python
plt.savefig('./img/sns_year.png')
plt.close() # umożlwia na nadpisanie wcześniej istniejącego pliku
```

[Wizualizacja danych - workshop.ipynb](https://colab.research.google.com/drive/19LjzKWlbnEZh_ibv_ISFjbZGhoXHmAF8?usp=sharing)

### Rotacja opisu na osi X

```python
# 1
fig.autofmt_xdate(rotation=45)

# 2
for label in axes.get_xticklabels():
  label.set_rotation(45)
  label.set_ha('right')
```

[[Rotate axis text in python matplotlib](https://stackoverflow.com/questions/10998621/rotate-axis-text-in-python-matplotlib)](<https://stackoverflow.com/a/56139690/10691771>)
