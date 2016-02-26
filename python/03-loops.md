---
layout: page
title: Programming with Python
subtitle: Repeating Actions with Loops
minutes: 30
---
> ## Learning Objectives {.objectives}
>
> *   Explain what a for loop does.
> *   Correctly write for loops to repeat simple calculations.
> *   Trace changes to a loop variable as the loop runs.
> *   Trace changes to other variables as they are updated by a for loop.

Deep down, most people want to learn how to program so they can automate things. To do that, we’ll have to teach the computer how to repeat actions.

An example task that we might want to repeat is printing each item in a list. One way to do this would be to use a series of print statements:


~~~ {.python}
odds = [1, 3, 5, 7]
print odds[0]
print odds[1]
print odds[2]
print odds[3]
~~~
~~~ {.output}
1
3
5
7
~~~

This is a bad approach for two reasons:

It doesn’t scale: if we want to print the characters in a string or items in a list that’s hundreds of values long, it would take a very, very long time.

It’s fragile: if we give it a longer list, it only prints part of the data, and if we give it a shorter one, it produces an error because we’re asking for characters that don’t exist.


~~~ {.python}
odds = [1, 3, 5]
print odds[0]
print odds[1]
print odds[2]
print odds[3]
~~~
~~~ {.output}
1
3
5
~~~
~~~ {.error}
---------------------------------------------------------------------------
IndexError                                Traceback (most recent call last)
<ipython-input-20-543712197ba5> in <module>()
      3 print odds[1]
      4 print odds[2]
----> 5 print odds[3]

IndexError: list index out of range
~~~

A better approach is to automate it with a **for loop**:


~~~ {.python}
word = 'lead'
for char in word:
    print char
~~~
~~~ {.output}
l
e
a
d
~~~

This is shorter than writing individual print statements for each character, and more robust as well:


~~~ {.python}
word = 'oxygen'
for char in word:
    print char
~~~
~~~ {.output}
o
x
y
g
e
n
~~~

The improved version uses a for loop to repeat an operation—in this case, printing—once for each thing in a collection. The general form of a loop is:

~~~ {.python}
for variable in collection:
    do things with variable
~~~
~~~ {.error}

  File "<ipython-input-23-07fac83181f1>", line 2
    do things with variable
            ^
SyntaxError: invalid syntax
~~~


We can call the loop variable anything we like, but there must be a colon at the end of the line starting the loop, and we must indent anything we want to run inside the loop. Unlike many other languages, there is no command to end a loop (e.g. end for); what is indented after the for statement belongs to the loop.

Here’s another loop that repeatedly updates a variable:


~~~ {.python}
length = 0
for vowel in 'aeiou':
    length = length + 1
print 'There are', length, 'vowels'
~~~
~~~ {.output}
There are 5 vowels
~~~

It’s worth tracing the execution of this little program step by step. Since there are five characters in 'aeiou', the statement on line 3 will be executed five times. The first time around, length is zero (the value assigned to it on line 1) and vowel is 'a'. The statement adds 1 to the old value of length, producing 1, and updates length to refer to that new value. The next time around, vowel is 'e' and length is 1, so length is updated to be 2. After three more updates, length is 5; since there is nothing left in 'aeiou' for Python to process, the loop finishes and the print statement on line 4 tells us our final answer.

Note that a loop variable is just a variable that’s being used to record progress in a loop. It still exists after the loop is over, and we can re-use variables previously defined as loop variables as well:


~~~ {.python}
letter = 'z'
for letter in 'abc':
    print letter
print 'after the loop, letter is', letter
~~~
~~~ {.output}
a
b
c
after the loop, letter is c
~~~

> ## Built-in functions {.callout}
> 
> Finding the length of a string is such a common operation that Python actually has a built-in function to do it called `len`:
> 
> ~~~ {.python}
> print len('aeiou')
> ~~~
> ~~~ {.output}
> 5
> ~~~
> 
> `len` is much faster than any function we could write ourselves, and much easier to read than a two-line loop; it will also give us the length of many other things that we haven’t met yet, so we should always use it when we can.


> ## Computing powers with loops {.challenge}
>
> Exponentiation is built into Python:
>
> ~~~ {.python}
> print 5**3
> 125
> ~~~
>
> Write a loop that calculates the same result as `5 ** 3` using just
> multiplication (without exponentiation).

> ## Reverse a string {.challenge}
>
> Write a loop that takes a string,
> and produces a new string with the characters in reverse order,
> so `'Newton'` becomes `'notweN'`.


# Making Choices

When analyzing data, we'll often want to automatically recognize differences between values and take different actions on the data depending on some conditions. Here, we'll learn how to write code that runs only when certain conditions are true.

## Conditionals

We can ask Python to take different actions, depending on a condition, with an **if statement**:


~~~ {.python}
num = 42

if num > 100:
    print 'greater'
else:
    print 'not greater'
    
print 'done'
~~~
~~~ {.output}
not greater
done
~~~

The second line of this code uses the keyword if to tell Python that we want to make a choice. If the test that follows the if statement is true, the body of the if (i.e., the lines indented underneath it) are executed. If the test is false, the body of the else is executed instead. Only one or the other is ever executed.

![Executing a Conditional](fig/python-flowchart-conditional.svg)\

Conditional statements don’t have to include an else. If there isn’t one, Python simply does nothing if the test is false:

~~~ {.python}
num = 42
print 'before conditional...'
if num > 100:
    print num, 'is greater than 100'
print '...after conditional'
~~~
~~~ {.output}
before conditional...
...after conditional
~~~

We can also chain several tests together using `elif`, which is short for “else if”. The following Python code uses `elif` to print the sign of a number:

~~~ {.python}
num = -3

if num > 0:
    print num, "is positive"
elif num == 0:
    print num, "is zero"
else:
    print num, "is negative"
~~~
~~~ {.output}
-3 is negative
~~~

One important thing to notice in the code above is that we use a double equals sign `==` to test for equality rather than a single equals sign because the latter is used to mean assignment.

We can also combine tests using `and` and `or`. `and` is only true if both parts are true:

~~~ {.python}
if (1 > 0) and (-1 > 0):
    print 'both tests are true'
else:
    print 'at least one test is false'
~~~
~~~ {.output}
at least one test is false
~~~

while `or` is true if at least one part is true:

~~~ {.python}
if (1 > 0) or (-1 > 0):
    print 'at least one test is true'
else:
    print 'neither test is true'
~~~
~~~ {.output}
at least one test is true
~~~

> ## How many paths? {.challenge}
>
> Which of the following would be printed if you were to run this code? Why did you pick this answer?
>
> 1.  A
> 2.  B
> 3.  C
> 4.  B and C
>
> ~~~ {.python}
> if 4 > 5:
>     print('A')
> elif 4 == 5:
>     print('B')
> elif 4 < 5:
>     print('C')
> ~~~

> ## What is truth? {.challenge}
>
> `True` and `False` are special words in Python called `booleans` which represent true
and false statements. However, they aren't the only values in Python that are true and false.
> In fact, *any* value can be used in an `if` or `elif`.
> After reading and running the code below,
> explain what the rule is for which values are considered true and which are considered false.
>
> ~~~ {.python}
> if '':
>     print('empty string is true')
> if 'word':
>     print('word is true')
> if []:
>     print('empty list is true')
> if [1, 2, 3]:
>     print('non-empty list is true')
> if 0:
>     print('zero is true')
> if 1:
>     print('one is true')
> ~~~

> ## Close enough {.challenge}
>
> Write some conditions that print `True` if the variable `a` is within 10% of the variable `b`
> and `False` otherwise.
> Compare your implementation with your partner's:
> do you get the same answer for all possible pairs of numbers?


## Combining loops

Imagine that (for some strange reason) you want to write some code that:

- produces a list of 100 random integers between -10 and 10
- sums the positive and negative numbers in the list separately

Let's start by producing the list of random integers. Python has a library for producing random numbers, `random`, we can use for this task. One of the functions in this library is `randint`, which produces random integers between some bounds:


~~~ {.python}
import random

print random.randint(-10,10)
~~~
~~~ {.output}
7
~~~

This command produces a single random number, so we can run it repeatedly by writing a `for` loop and building a list:

~~~ {.python}
randomNumbers = [] # creates an empty list

for num in range(100):
    randomNumbers.append(random.randint(-10,10))
    
print len(randomNumbers)
~~~
~~~ {.output}
100
~~~

> ## From 0 to N-1 {.callout}
> 
> Python has a built-in function called `range` that creates a sequence of numbers. `range` can accept 1-3 parameters. If one parameter is input, `range` creates an array of that length, starting at zero and incrementing by 1. If 2 parameters are input, `range` starts at the first and ends at the second, incrementing by one. If `range` is passed 3 parameters, it stars at the first one, ends at the second one, and increments by the third one. For example, `range(3)` produces the numbers 0, 1, 2, while `range(2, 5)` produces 2, 3, 4, and `range(3, 10, 3)` produces 3, 6, 9.


We can now write a `for` loop that steps through the list of random integers and determines if the values are positive or negative:


~~~ {.python}
sumPositive = 0
sumNegative = 0

for num in randomNumbers:
    
    if num > 0:        
        sumPositive = sumPositive + num
        
    else:        
        sumNegative = sumNegative + num
        
print sumPositive, sumNegative
~~~
~~~ {.output}
262 -290
~~~

> ## Generalize your code {.challenge}
> 
> The code we wrote can be even shorter than it currently is! Rewrite the code with only one for loop and using in-place operators for the sums. Python (and most other languages in the C family) provides in-place operators that work like this:
> 
> ~~~ {.python}
> x = 1  # original value
> x += 1 # add one to x, assigning result back to x
> x *= 3 # multiply x by 3
> print(x)
> ~~~
> ~~~ {.output}
>     6
> ~~~
