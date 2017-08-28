---
title: "Tuples & Dictionaries"
teaching:
exercises:
questions:
- "foo"
objectives:
- "obj 1"
keypoints:
- "kp 1"
---
## Tuples are immutable lists

*   Sometimes it is useful to "bind" different pieces of data together.
*   Tuples can be indexed like lists

~~~
name = ('Paul', 'Wilson')
first_name = name[0]
last_name = name[1]
print(first_name,last_name)
~~~
{: .python}

*   Individual entries in tuples cannot be changed

~~~
name = ('Paul', 'Wilson')
name[1] = 'Nagus-Wilson'
~~~
{: .python}
~~~
TypeError                                 Traceback (most recent call last)
<ipython-input-85-8f02ace9e6af> in <module>()
      1 name = ('Paul','Wilson')
----> 2 name[1] = 'Nagus-Wilson'

TypeError: 'tuple' object does not support item assignment
~~~
{: .output}


*   Lists of tuples are better than parallel lists
*   Can't accidentally change data that is presumed to correspond

~~~
first_names = ['Paul', 'Patrick', 'Sarah']
last_names = ['Wilson', 'Shriwise', 'Stevens']
last_names[1] = 'Nagus-Wilson'
names = [('Paul','Wilson'), ('Patrick','Shriwise'), ('Sarah','Stevens')]
print(names[0])
~~~
{: .python}

## Dictionaries add semantic value to this

~~~
PW = {'first': 'Paul', 'last': 'Wilson'}
PS = {'first': 'Patrick', 'last': 'Shriwise'}
SS = {'first': 'Sarah', 'last': 'Stevens'}
instructors = { 'PW' : PW, 'PS' : PS, 'SS' : SS }
~~~
{: .python}

