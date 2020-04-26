---
layout: post
title: "Python: Matplotlib i seaborn"
categories: Linux
author: "Michał"
---



Rozwiązanie problemu z wieszającym się Numpy na serwerze VPS webh.pl:

```bash
export OPENBLAS_CORETYPE=Sandybridge
```
Powyższa linijka wymusza detekcję procesora na starszą rodzinę niż w rzeczywistości.



# Matplotlib

Zapis do pliku:

```python
plt.savefig('./img/sns_year.png')
plt.close() # umożlwia na nadpisanie wcześniej istniejącego pliku
```

