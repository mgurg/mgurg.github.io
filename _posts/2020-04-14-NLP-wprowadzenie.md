---
layout: post
title: "NLP: Wprowadzenie"
categories: ML
author: "Michał"
---

Wprowadzenie do Machine learning wyglada na razie słabo, może z NLP (Natural Language Processing) bedzie lepiej :)

Podstawowe pojęcia:
Tokenizacja - podział zdań na słowa

## Bag-of-words model

Term frequency (TF) - liczba wystąpień słowa "S" w dokumencie / liczba wszystkich słów w dokumencie

$$
tf(t,d) = \frac{\mbox{frequency of the token in the doc}}{\mbox{total number of tokens in the doc}}
$$

Inverse document frequency (IDF) - logarytm z liczby dokumentów / liczbę dokumentów w których występuje słowo "S"
