---
jupyter:
  jupytext:
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.17.3
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
---

```python
"""Эмпирический анализ временной сложности алгоритмов"""
```

```python
"""Асонов С.В ИУ10-36"""
```

```python
"""Задания"""
```

```python
"""Задание 1.1"""
```

```python
import random, usage_time
import matplotlib.pyplot as plt


def get_by_index(v: list):
    return v[random.randint(0, len(v)) - 1]

items = range(1, 10**5 * (20 - 2), 50000)
func = usage_time.get_usage_time()(get_by_index)
times = [
    func([
        random.randint(1, 3) 
        for _ in range(n)
    ]) 
    for n in items
]

fig = plt.plot(items, times, 'bo-')
ax = plt.gca()

plt.title('Время выполнения алгоритма')
ax.set_xlabel('Номера элементов')
ax.set_ylabel('Время, сек')
```

```python
"""Задание 1.3
```

```python
import random, usage_time
import matplotlib.pyplot as plt


def multiplication_nums(v: list):
    multi = 1
    for num in v:
        multi *= num
    return multi


items = range(1, 10**4 * (20 - 2), 10000)
func = usage_time.get_usage_time()(multiplication_nums)
times = [
    func([
        random.randint(1, 3) 
        for _ in range(n)
    ]) 
    for n in items
]

fig = plt.plot(items, times, 'bo-')
ax = plt.gca()

plt.title('Время выполнения алгоритма')
ax.set_xlabel('Номера элементов')
ax.set_ylabel('Время, сек')
```

```python
"""Задание 1.4"""
```

```python
import random, usage_time
import matplotlib.pyplot as plt


def horner_method(v, x):
    result = v[0]
    for i in range(1, len(v)):
        result = result * x + v[i]
    return result

items = range(1, 10**4 * (20 - 2), 10000)
x_val = 1.5 

func = usage_time.get_usage_time()(horner_method)
times = [
    sum([
        func([
            random.randint(1, 10) 
            for _ in range(n)
        ], x_val)  
        for _ in range(20)
    ]) / 20
    for n in items
]


fig = plt.plot(items, times, 'bo-')
ax = plt.gca()

plt.title('Время выполнения алгоритма')
ax.set_xlabel('Номера элементов')
ax.set_ylabel('Время, сек')
```

```python
"""Задание 1.7"""
```

```python
import random, usage_time
import matplotlib.pyplot as plt


def mean(v):
    total = 0.0
    for element in v:
        total += element
    return total / len(v)


items = range(1, 10**4 * (20 - 2), 10000)
func = usage_time.get_usage_time()(mean)
times = [
    sum([
        func([
            random.randint(1, 10) 
            for _ in range(n)
        ])
        for _ in range(20)
    ]) / 20
    for n in items
]

fig = plt.plot(items, times, 'bo-')
ax = plt.gca()

plt.title('Время выполнения алгоритма')
ax.set_xlabel('Номера элементов')
ax.set_ylabel('Время, сек')
```

```python
"""Задание 2"""
```

```python
import numpy as np
import time
import matplotlib.pyplot as plt
from statistics import mean


N = 18  
max_n = 10**2 * N  
step = 100  
num_runs = 5  


n_values = []
times = []


for n in range(1, max_n + 1, step):
    
   
    A = np.random.rand(n, n)
    B = np.random.rand(n, n)
    print(f"Обрабатывается размер матрицы = {n}x{n}")
    run_times = []
    
  
    for run in range(num_runs):
        start_time = time.time()
        
        C = np.dot(A, B)
        
        end_time = time.time()
        run_times.append(end_time - start_time)
    
    n_values.append(n)
    times.append(mean(run_times))


plt.figure(figsize=(12, 6))
plt.plot(n_values, times, 'ro-', linewidth=2, markersize=6)
plt.title('Время выполненения алгоритма')
ax.set_xlabel('Номера элементов')
ax.set_ylabel('Время, сек')
plt.grid(True, alpha=0.3)
plt.show()
```

```python

```
