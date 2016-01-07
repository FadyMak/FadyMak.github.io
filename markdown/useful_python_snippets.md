I put together a list of useful Python snippets that have saved me tons of time and I hope they can save you some time as well. Most of these snippets came out of searching for solutions and finding blog posts and StackOverflow answers addressing similar problems. All the snippets below have been tested for Python 3.
# Calling an External Command in Python

There are times when you need to call an external command via the shell or command prompt and Python makes this quite easy via the `subprocess` module.

To simply run a command:

```python
import subprocess
subprocess.call(['mkdir', 'empty_folder'])
``` 

If you want to run a command and get it's resulting output:

```python
import subprocess
output = subprocess.check_output(['ls', '-l'])
```

It's important to note that the above calls are blocking.

If you are trying to run commands that are built into the shell such as `cd` or `dir`, you will need to specify the `shell=True` flag:

```python
import subprocess
output = subprocess.call(['cd', '/'], shell=True)
```

For more advanced use cases, you can use the [Popen constructor](https://docs.python.org/3/library/subprocess.html#subprocess.Popen).

Python 3.5 introduced a new `run` function which is used in a very similiar manner to `call` and `check_output`. If you're using version 3.5 or greater, have a look at the [run](https://docs.python.org/3/library/subprocess.html#subprocess.run) documentation which has some useful examples. Otherwise, if you're using a version of Python prior to 3.5 or you would like to maintain backwards compatibility, the `call` and `check_output` snippets above are your safest and simplest choice.

# Pretty Printing

You can make your shell output more readable when developing by using the `pprint` module as a replacement for the standard `print` function. This makes dictionaries and nested objects printed to the shell a lot easier to read.

```python
import pprint as pp
animals = [{'animal': 'dog', 'legs': 4, 'breeds': ['Border Collie', 'Pit Bull', 'Huskie']}, {'animal': 'cat', 'legs': 4, 'breeds': ['Siamese', 'Persian', 'Sphynx']}]
pp.pprint(animals, width=1)
```

The width parameter specifies the maximum number of characters on a single line. Setting the width to 1 ensures that the dictionary is printed on separate lines.

# Grouping Data by Attributes

Assume you queried a database and got back data that looks like so:

```python
data = [
    {'animal': 'dog', 'name': 'Roxie', 'age': 5},
    {'animal': 'dog', 'name': 'Zeus', 'age': 6},
    {'animal': 'dog', 'name': 'Spike', 'age': 9},
    {'animal': 'dog', 'name': 'Scooby', 'age': 7},
    {'animal': 'cat', 'name': 'Fluffy', 'age': 3},
    {'animal': 'cat', 'name': 'Oreo', 'age': 5},
    {'animal': 'cat', 'name': 'Bella', 'age': 4}   
    ]
```

You want to group the data by the type of animal to return a list of dogs and a list cats. Thankfully, Python's itertools has a `groupby` function that allows you to do this easily:

```python
from itertools import groupby

data = [
    {'animal': 'dog', 'name': 'Roxie', 'age': 5},
    {'animal': 'dog', 'name': 'Zeus', 'age': 6},
    {'animal': 'dog', 'name': 'Spike', 'age': 9},
    {'animal': 'dog', 'name': 'Scooby', 'age': 7},
    {'animal': 'cat', 'name': 'Fluffy', 'age': 3},
    {'animal': 'cat', 'name': 'Oreo', 'age': 5},
    {'animal': 'cat', 'name': 'Bella', 'age': 4}   
    ]

for key, group in groupby(data, lambda x: x['animal']):
    for thing in group:
        print(thing['name'] + " is a " + key)
```

The output you get is:

```
Roxie is a dog
Zeus is a dog
Spike is a dog
Scooby is a dog
Fluffy is a cat
Oreo is a cat
Bella is a cat
```

`groupby()` takes 2 arguments:
1. the data we want to group, in this case a list of dictionaries
2. the function to group by:
    - `lambda x: x['animal']` is telling the groupby function to group the data by the type of animal in each dictionary

We can now easily construct a list of dogs and a list of cats using a list comprehension:

```python
from itertools import groupby
import pprint as pp

data = [
    {'animal': 'dog', 'name': 'Roxie', 'age': 5},
    {'animal': 'dog', 'name': 'Zeus', 'age': 6},
    {'animal': 'dog', 'name': 'Spike', 'age': 9},
    {'animal': 'dog', 'name': 'Scooby', 'age': 7},
    {'animal': 'cat', 'name': 'Fluffy', 'age': 3},
    {'animal': 'cat', 'name': 'Oreo', 'age': 5},
    {'animal': 'cat', 'name': 'Bella', 'age': 4}   
    ]

grouped_data = {}

for key, group in groupby(data, lambda x: x['animal']):
    grouped_data[key] = [thing['name'] for thing in group]
    
pp.pprint(grouped_data)
```

And you end up with an output grouped by the type of animal:

```
{
    'cat': [
        'Fluffy',
        'Oreo',
        'Bella'
    ],
    'dog': [
        'Roxie',
        'Zeus',
        'Spike',
        'Scooby'
    ]
}
```

The answers in [this StackOverflow question](http://stackoverflow.com/questions/773/how-do-i-use-pythons-itertools-groupby) were immensely helpful and saved me a lot of time when trying to figure out how to group data in the most *Pythonic* way.

# Removing Duplicates in a List

A quick and easy one liner to remove duplicates from a list in Python (order is not maintained):

```python
x = [1, 8, 4, 5, 5, 5, 8, 1, 8]
list(set(x))
```

This method leverages the fact that a set is a collection of **distinct** objects. However, a set does not maintain order, so if the order of the objects matter to you, use the technique below instead:

```python
from collections import OrderedDict
x = [1, 8, 4, 5, 5, 5, 8, 1, 8]
list(OrderedDict.fromkeys(x))
```

# Access the Index of a Python For Loop

This may be common knowledge to a lot of you but it's asked quite regularly. Python's built-in `enumerate` function gives you access to the index and value like so:

```python
x = [1, 8, 4, 5, 5, 5, 8, 1, 8]
for index, value in enumerate(x):
    print(index, value)
```

You can change the start index by specifying the `start` parameter to the `enumerate` function:

```python
x = [1, 8, 4, 5, 5, 5, 8, 1, 8]
for index, value in enumerate(x, start=1):
    print(index, value)
```

Now the index will go from *1 to 9* instead of *0 to 8*

# Iterating Over 2 Collections in Parallel

Use the built-in `zip` function to iterate over 2 lists, dictionaries, etc... at the same time. Here's an example of using the `zip` function on 2 lists:

```python
a = [1, 2, 3]
b = [4, 5, 6]
for (a_val, b_val) in zip(a, b):
    print(a_val, b_val)
```

Will output:

```
1 4
2 5
3 6
```

# Creating a Copy of Objects

You can copy an object in Python using the generic `copy` functions. A shallow copy is done using the `copy.copy` call:

```python
import copy
new_list = copy.copy(old_list)
```

For a deep copy:

```python
import copy
new_list = copy.deepcopy(old_list)
```

This [StackOverflow Question](http://stackoverflow.com/questions/2612802/how-to-clone-or-copy-a-list-in-python) gives a good explanation of the various methods for copying a list. If you're unfamiliar with the differences between a shallow copy and a deep copy have a look at [this explanation](http://stackoverflow.com/questions/17246693/what-exactly-is-the-difference-between-shallow-copy-deepcopy-and-normal-assignm).


# Floating Point Division

You can ensure floating point division by casting either the numerator or denominatior to a float:

```python
answer = a/float(b)
```

# Convert a String to a Date and a Date to a String

A common task is to convert a string to a datetime object. This is easily done using the `strptime` method:

```python
from datetime import datetime
date_obj = datetime.strptime('May 29 2015  2:45PM', '%B %d %Y %I:%M%p')
```

For the inverse operation, that is converting datetime object to a formatted string, use the `strftime` method on a datetime object:

```python
from datetime import datetime
date_obj = datetime.now()
date_string = date_obj.strftime('%B %d %Y %I:%M%p')
```

For a list of format codes and what they do, check out the [official docs](https://docs.python.org/3/library/datetime.html#strftime-and-strptime-behavior)

# Parsing a JSON file and Writing an Object to a JSON file

You can parse a JSON file and construct a Python object out of it using the `load` function. Assume we have a JSON file called *data.json* that contains the following data:

```javascript
{
    "dog": {
        "lives": 1,
        "breeds": [
            "Border Collie",
            "Pit Bull",
            "Huskie"
        ]
    },
    "cat": {
        "lives": 9,
        "breeds": [
            "Siamese",
            "Persian",
            "Sphynx"
        ]
    }
}
```

```python
import json
with open('data.json') as input_file:
    data = json.load(input_file)
```

*data* is now an object that you can manipulate as you normally would with any Python object:

```
print(data['cat']['lives'])
output: 9
```

To write a Python dictionary to a JSON file, you can use the `dump` method:

```python
import json

data = {'dog': {'legs': 4, 'breeds': ['Border Collie', 'Pit Bull', 'Huskie']}, 'cat': {'legs': 4, 'breeds': ['Siamese', 'Persian', 'Sphynx']}}

with open('data.json', 'w') as output_file:
    json.dump(data, output_file, indent=4)
```

The indent parameter pretty prints the JSON string so that the output is easier to read. In this case we're specifying 4 spaces.
