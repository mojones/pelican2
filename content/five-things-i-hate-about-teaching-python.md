Title: Five things I hate about teaching Python
Date: 2010-12-03 10:20
Modified: 2010-12-05 19:30
Category: article
Tags: python, teaching
slug: five-things-i-hate-about-python
Authors: Martin

**NOTE: this article was originally written around 2015, when I had recently started teaching Python. Point number 1 is now largely irrelevant due to the prevalence of Python 3, and the others have become less annoying. I leave this article here for historical reaons!**

I've just finished teaching an intensive Python programming course and, as usual, spending a week thinking about how best to introduce my students to programming has given me something to write about. I realised that, while I've spent a lot time talking about why Python is a great language, I have a number of pet peeves that I've never written down.

I'm not talking about the usual problems, like Python's relative lack of performance or lack of compile-time type checking – these things are deliberate design tradeoffs and changing them would involve making Python not-Python. I'm talking about the small things that cause friction, especially in a teaching environment.

Note: I realize that there are good reasons for all these things to be the way they are, so don't take this too seriously. Also, let me make it clear that these are things I dislike about **teaching** Python, generally to novice programmers. None of these things particularly annoy me when **writing** Python code. 

## 1. Floating point vs. integer division

Anyone who's written in Python for any length of time probably types this line automatically without really thinking about it:
    
```
from __future__ import division
```
    
but take a moment to consider how you would explain what's going on in this piece of code to a beginner. In order to really understand what's happing here, you have to know about:

* Python's system for importing modules
* Python's system for grouping modules into packages
* the fact that there are different versions of Python with slightly different behaviour
* the difference between floating-point and integer numbers
* the mechanisms of operator overloading, whereby we can define the behaviour of things like + and / for different types
* the concept of polymorphic functions and operators, which allow us to treat different classes the same, some of the time

Explaining all this to someone who has never written a line of code before is unlikely to be productive, but none of the alternatives are particularly attractive either. We can just present this as a magic piece of code and save the explanation for later (this is normally what I do). We can instruct students to use explicit floating point numbers:
    
    answer = float(4)/3
    answer = 4.0/3
    

but eventually they will forget and use integers and find that it works some of the time. We can carefully craft our examples and exercises to avoid the need for floating point division, but this is setting students up for pain further down the line. We can use the command-line argument `-Q` to force floating-point division, or just use Python 3 for teaching, but both of these options will cause confusion once the student goes back to their own environment.


## 2. `split()` vs. `join()`

**Teacher:** "OK class, this is how we take a string and split it up into a list of strings using a fixed delimiter:"
    
    sentence = "The all-England summarize Proust competition"
    words = sentence.split(" ")
    

**Student:** "So I guess, logically, to put the words back together again we just say:
    
    
    
    sentence = words.join(" ")
    

right? Look at that elegant symmetry…… Wait a minute, you're telling me it doesn't work like that? The list and the delimiter actually go the other way around, so that we have to write this ugly line?
    
    sentence = " ".join(words)

Wow, that just looks wrong."

Yes, I know that there are good reasons for collection classes to only have methods that are type-agnostic, but would it really be so bad to just `str()` everything?

## 3. Exhaustible files

It's perfectly logical that you shouldn't be able to iterate through a file object twice without re-opening it... once you know a fair bit about how iteration is actually implemented in Python. As a beginner, thought, it's a bit like Python is giving with one hand and taking away with the other – you can use an opened file object just like a list, except in this one specific but very important way:
    

    my_list = [1,2,3,4]
    for number in my_list:
        do_something(number)
    # second loop works just as you'd expect
    for number in my_list:
        do_something_else(number)
    
    my_file = open("some.input")
    for line in my_file:
        do_something(line)
    # second loop silently never runs
    for line in my_file:
        do_something_else(line)
    
This problem also rears its ugly head when students try to iterate over a file having already consumed its contents using `read()`:
    
    
    my_file = open("some.input")
    my_contents = my_file.read()
    ....
    # this loop silently never runs
    for line in my_file:
        do_something(line)

That second line can be difficult to spot for student and teacher alike when there are many intervening lines between it and the loop.

## 4. Lambda expressions

OK, this one is more annoying when writing code than when teaching it, since I rarely get round to talking about functional programming in introductory courses. I totally get why there should be a big, obvious flag when we are doing something clever (which lambda expressions generally are). Nevertheless, it seems a shame to have a style of coding that lends itself to elegant brevity marred by so many unnecessary keystrokes.

I think that the reason this bugs me so much is that I first got into functional programming by way of Groovy, which has (to me) a very pleasing syntax for anonymous functions (actually closures):
    
    
    {x,y -> x**y}
    
compared to Python:

    lambda x,y : x**y
  
Of course, Python lessens the sting of having to type `lambda` with its various comprehensions:
    
  
    squares = map(lambda x : x**2, range(10))
    squares = [x**2 for x in range(10)]
    
so I can't complain too loudly.

## 5. Variables aren't declared

It's just way too easy for beginners to make a typo that brings their progress to a screeching halt. Consider this real-life example from my most recent course:
    
    
    positions = [0]
    for pos in [12,54,76,103]:
        postions  = positions + [pos]
    print(positions) # prints [0] rather than [0,12,54,76,103]
    

Leaving aside that this particular example could have been salvaged by using `positions.append()`, it took way to long for us to track down the typo. In real-life code, this is the kind of thing that would ideally be caught by unit testing. This is one (rare!) case in which I pine for the old days of teaching Perl – `use strict` and `my` would have taken care of this type of problem.

