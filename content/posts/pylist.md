---
title: "Don't use Python List Everywhere"
date: 2022-07-16T22:13:15+02:00
toc: false
images:
tags:
  - python
  - list
---

the common thing that if you wanna add some items to someplace may thinking to use List, that's great python list has a lot of features that you need like

- mutable: you can add/delete what you want from the list
- ordered: the items have a defined order
- allowed duplicates: lists can have items with the same value

so you will ask me a question if lists have something like that so why should I avoid it then?
well the answer key is that performance is the key, YES Lists are not the best choice if you're caring about your program's performance

before beginning we can take this as an example 

```python
l = list(range(100))
```
this a simple code that returns a int list from 0 to 99, so let's talk more about python integers

### what are Python Integers actually
we all know that the python interpreter is written in C, so the python objects are C structures but it not contain the its value only, nope it contains extra information like

- variable type 
- size of the data members
- reference count
- the actual value the variable

```C
struct _longobject {
    long ob_refcnt;
    PyTypeObject *ob_type;
    size_t ob_size;
    long ob_digit[1];
};
```

All this extra information is what gives python more flexibility to lets you code freely
Not just integers but all the data types in Python comes with this cost that becomes significant in structures that combine many of these objects like List


you understand what's python types are, so let's talk about 

### Why List is not the best choice
Python list should be like a C array both are mutable but the difference is C array should be homogeneous not like Python list, yes this makes python list more flexible but it is more costly to be heterogeneous because each of the elements of the list must contain its extra information that I mentioned above
in short, each item is a complete Python object in the List
so when all list items are of the same type with this extra information that becomes redundant



Also if you tried to add more data to your python list you will do something very costly because python creates a new list with more extra space than the original list and push the new data and the old one into the new space
for example


```python
>>> l = [1,2]
>>> l.append(3)
# CREATE ANOTHER ONE len(x) == 3
# APPEND number 3
```


![digram](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3ocr9cx8k54sk2o6qetg.png)


imagine using something like that with the list that contains 100000 items, we will copy 100000 items in every append process

```python
# m = 100000
for i in range(100):
    m.append(i)
```



### Alternatives

#### Tuples

Tuples are immutable so it has a fixed size, Also it's allow duplicates and Ordered
```python
>>> %time l = [0,1,2,3,4,5,6,7,8,9]
1000000 loops, best of 3: 285 ns per loop
>>> %timeit t = (0,1,2,3,4,5,6,7,8,9)
10000000 loops, best of 3: 55.7 ns per loop
```

#### Sets

Sets like Tuples but Unordered and Unchangeable it doesn't allow the duplicates items 

let's take this example code 
```python
def list_unique_names(phonebook):
    unique_names = []
    for name, phonenumber in phonebook: 1
        first_name, last_name = name.split(" ", 1)
        for unique in unique_names: 
            if unique == first_name:
                break
        else:
            unique_names.append(first_name)
    return len(unique_names)
def set_unique_names(phonebook):
    unique_names = set()
    for name, phonenumber in phonebook: 
        first_name, last_name = name.split(" ", 1)
        unique_names.add(first_name) 
    return len(unique_names)
phonebook = [
    ("John Doe", "555-555-5555"),
    ("Albert Einstein", "212-555-5555"),
    ("John Murphey", "202-555-5555"),
    ("Albert Rutherford", "647-555-5555"),
    ("Guido van Rossum", "301-555-5555"),
]
print("Number of unique names from set method:", set_unique_names(phonebook))
print("Number of unique names from list method:", list_unique_names(phonebook))
```
we have two functions here, One used list, the other sets
and after calling them this the performance report

```python
>>> %timeit list_unique_names(large_phonebook)
1 loops, best of 3: 2.56 s per loop
>>> %timeit set_unique_names(large_phonebook)
100 loops, best of 3: 9.57 ms per loop
```



#### Numpy Arrays
numpy arrays are almost certainly a better choice if you are doing anything heavily nu‐
meric, as you get more datatype options and many specialized and fast functions. You
might choose to avoid numpy if you want fewer dependencies for your project, though
Cython and Pythran work equally well with array and numpy arrays; Numba works with
numpy arrays only



## References
- https://www.amazon.com/High-Performance-Python-Performant-Programming/dp/1449361595
- https://www.analyticsvidhya.com/blog/2021/05/why-you-should-avoid-using-python-lists/#h2_3



thanks for reading 
bye :D

![BYE](https://media.giphy.com/media/m9eG1qVjvN56H0MXt8/giphy.gif)
