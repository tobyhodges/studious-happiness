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
print(first_names)
print(last_names)
names = [('Paul','Wilson'), ('Patrick','Shriwise'), ('Sarah','Stevens')]
print(names[0])
~~~
{: .python}

## Dictionaries add semantic value

*    Once you start binding data together, it is almost always useful to add
     semantic meaning
*    Dictionaries allow you to name the entries that our bound together
*    Dictionaries are mutable, you can change them
     *   Because of semantic meaning, this is less dangerous

~~~
name = {'first': 'Paul', 'last': 'Wilson'}
print(name['first'], name['last'])
~~~
{: .python}

*   The names of each entry are called `keys`
*   The values of each entry are called `values`

~~~
name = {'first': 'Paul', 'last': 'Wilson'}
print(name.keys())
print(name.values())
~~~
{: .python}
~~~
dict_keys(['first', 'last'])
dict_values(['Paul', 'Wilson'])
~~~
{: .output}


## All container types can be nested

*   Lists, tuples and dictionaries may all contain lists, tuples and dictionaries

~~~
instructors = [{'first':'Paul', 'last':'Wilson'},
               {'first':'Sarah','last':'Stevens'},
               {'first':'Patrick','last':'Shriwise'}]
axes_tics = {'x': [0,0.1,0.2,0.3,0.4,0.5],
             'y': [0,10,20,30,40,50]}
curve_points = [(0,0), (2,3), (4,5), (6,-3), (8,-10)]
~~~
{: .python}

