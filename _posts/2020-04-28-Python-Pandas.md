---
layout: post
title: "Python: Pandas"
categories: Python
author: "Michał"
math: true
---



Tips & Tricks, najciekawsze rzeczy, które pomagają mi w pracy z biblioteką Pandas

### Replace

```python
cleanup_nums = {"num_doors":     {"four": 4, "two": 2},
                "num_cylinders": {"four": 4, "six": 6, "five": 5, "eight": 8,
                                  "two": 2, "twelve": 12, "three":3 }}
                                  
obj_df.replace(cleanup_nums, inplace=True)
```

