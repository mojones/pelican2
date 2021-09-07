Title: Python tutorial part six
Date: 2010-12-03 10:20
Modified: 2010-12-05 19:30
Category: tutorial
Tags: python, tutorial
slug: tutorial/functions
Authors: Martin
Summary: Python tutorial part 6: writing functions

## Why do we want to write our own functions?

Here's a piece of code which prints the AT content (the proportion of a DNA sequence which is either A or T) of a given DNA sequence:

    my_dna = "ACTGATCGATTACGTATAGTATTTGCTATCATACATATATATCGATGCGTTCAT" 
    
    length = len(my_dna) 
    a_count = my_dna.count('A') 
    t_count = my_dna.count('T') 
    at_content = (a_count + t_count) / length 
    
    print("AT content is " + str(at_content)) 
    
If we discount the first line (whose job is to store the input sequence) and the last line (whose job is to print the result), we can see that it takes four lines of code to calculate the AT content. This means that every place in our code where we want to calculate the AT content of a sequence, we need these same four lines – and we have to make sure we copy them exactly, without any mistakes. 

It would be much simpler if Python had a built in function (let's call it `get_at_content()`) for calculating AT content. If that were the case, then we could just run `get_at_content()` in the same way we run `print()`, or `len()`, or `open()`. Although, sadly, Python does not have such a built in function, it does have the next best thing – a way for us to create our own functions. 

Creating our own function to carry out a particular job has many benefits. It allows us to reuse the same code many times within a program without having to copy it out each time. Additionally, if we find that we have to make a change to the code, we only have to do it in one place. Splitting our code into functions also allows us to tackle larger problems, as we can work on different bits of the code independently. We can also reuse code across multiple programs. 

### Defining a function

Let's go ahead and create our `get_at_content()` function. Before we start typing, we need to figure out what the inputs (the number and types of the function arguments) and outputs (the type of the return value) are going to be. For this function, that seems pretty obvious – the input is going to be a single DNA sequence, and the output is going to be a decimal number. To translate these into Python terms: the function will take a single argument of type string, and will return a value of type number. Here's the code:

    def get_at_content(dna): 
        length = len(dna) 
        a_count = dna.count('A') 
        t_count = dna.count('T') 
        at_content = (a_count + t_count) / length 
        return at_content 

Reminder: if you're using Python 2 rather than Python 3, include this line at the top of your program:

    from __future__ import division

The first line of the function definition contains several different elements. We start with the word `def`, which is short for **define** (writing a function is called defining it).  Following that we write the name of the function, followed by the names of the argument variables in parentheses. Just like we saw before with normal variables, the function name and the argument names are arbitrary – we can call them whatever we like. 

The first line ends with a colon, just like the first line of the loops that we were looking at in the previous chapter. And just like loops, this line is followed by a block of indented lines – the function body. The function body can have as many lines of code as we like, as long as they all have the same indentation. Within the function body, we can refer to the arguments by using the variable names from the first line. In this case, the variable `dna` refers to the sequence that was passed in as the argument to the function. 

The last line of the function causes it to return the AT content that was calculated in the function body. To return from a function, we simply write `return` followed by the value that the function should output. 

There are a couple of important things to be aware of when writing functions. Firstly, we need to make a clear distinction between defining a function, and running it (we refer to running a function as **calling** it).  The code we've written above will not cause anything to happen when we run it, because we've not actually asked Python to execute the `get_at_content()` function – we have simply defined what it is. The code in the function will not be executed until we call the function like this:

    get_at_content("ATGACTGGACCA")

If we simply call the function like that, however, then the AT content will vanish once it's been calculated. In order to use the function to do something useful, we must either store the result in a variable:

    at_content = get_at_content("ATGACTGGACCA")

Or use it directly:

    print("AT content is " + str(get_at_content("ATGACTGGACCA")))

Secondly, it's important to understand that the argument variable `dna` does not hold any particular value when the function is defined. Instead, its job is to hold whatever value is given as the argument when the function is called. In this way it's analogous to the loop variables we saw in the previous section: loop variables hold a different value each time round the loop, and function argument variables hold a different value each time the function is called. 

Finally, be aware that any variables that we create as part of the function only exist inside the function, and cannot be accessed outside. If we try to use a variable that's created inside❶ the function from outside❷:

    def get_at_content(dna): 
        length = len(dna) 
        a_count = dna.count('A')❶
        t_count = dna.count('T') 
        at_content = (a_count + t_count) / length 
        return at_content 
    
    print(a_count)❷

We'll get an error:

>NameError: name 'a_count' is not defined 

### Calling and improving our function

Let's write a small program that uses our new function, to see how it works. We'll try both storing the result in a variable before printing it❶ and printing it directly❷:

    def get_at_content(dna): 
        ... 
    
    my_at_content = get_at_content("ATGCGCGATCGATCGAATCG")
    print(str(my_at_content))❶
    
    print(get_at_content("ATGCATGCAACTGTAGC"))❷
    print(get_at_content("aactgtagctagctagcagcgta"))

Looking at the output, we can see that the first function call works fine – the AT content is calculated to be 0.45, is stored in the variable `my_at_content`, then printed. However, the output for the next two calls is not so great. The second function call produces a number with way too many figures after the decimal point, and the third function call, with the input sequence in lower case, gives a result of 0.0, which is definitely not correct:

>0.45 
>0.5294117647058824 
>0.0

We'll fix these problems by making a couple of changes to the `get_at_content()` function. We can add a rounding step in order to limit the number of significant figures in the result. Python has a built in `round()` function that takes two arguments – the number we want to round, and the number of significant figures. We'll call the `round()` function on the result before we return it❶. And we can fix the lower case problem by converting the input sequence to uppercase❷ before starting the calculation. Here's the new version of the function, with the same three function calls:

    def get_at_content(dna): 
        length = len(dna) 
        a_count = dna.upper().count('A')❷
        t_count = dna.upper().count('T') 
        at_content = (a_count + t_count) / length 
        return round(at_content, 2)❶
    
    my_at_content = get_at_content("ATGCGCGATCGATCGAATCG")
    print(str(my_at_content))
    print(get_at_content("ATGCATGCAACTGTAGC"))
    print(get_at_content("aactgtagctagctagcagcgta"))

and now the output is just as we want:

>0.45 
>0.53 
>0.52 

We can make the function even better though: why not allow it to be called with the number of significant figures as an argument? In the above code, we've picked two significant figures, but there might be situations where we want to see more. Adding the second argument is easy; we just add it to the argument variable list❶ on the first line of the function definition, and then use the new argument variable in the call to `round()`❷. We'll throw in a few calls to the new version of the function with different arguments to check that it works:

    def get_at_content(dna, sig_figs):❶ 
        length = len(dna) 
        a_count = dna.upper().count('A') 
        t_count = dna.upper().count('T') 
        at_content = (a_count + t_count) / length 
        return round(at_content, sig_figs)❷
    
    test_dna = "ATGCATGCAACTGTAGC"
    print(get_at_content(test_dna, 1))
    print(get_at_content(test_dna, 2))
    print(get_at_content(test_dna, 3))

The output confirms that the rounding works as intended:

>0.5 
>0.53 
>0.529

### Encapsulation with functions

Let's pause for a moment and consider what happened in the previous section. We wrote a function, and then wrote some code that used that function. In the process of writing the code that used the function, we discovered a couple of problems with our original function definition. **We were then able to go back and change the function definition, without having to make any changes to the code that used the function**. 

I've written that last sentence in bold, because it's incredibly important. It's no exaggeration to say that understanding the implications of that sentence is the key to being able to write larger, useful programs. The reason it's so important is that it describes a programming phenomenon that we call **encapsulation**.

Encapsulation just means dividing up a complex program into little bits which we can work on independently. In the example above, the code is divided into two parts – the part where we define the function, and the part where we use it – and we can make changes to one part without worrying about the effects on the other part. 

This is a very powerful idea, because without it, the size of programs we can write is limited to the number of lines of code we can hold in our brain at one time. Some of the example code in the previous chapter were starting to push at this limit already, even for relatively simple problems. By contrast, using functions allows us to build up a complex program from small building blocks, each of which individually is small enough to understand in its entirety. 

Because using functions is so important, future examples will use them when appropriate, even when it's not explicitly mentioned in the text. I encourage you to get into the habit of using functions in your programs too. 

### Functions don't always have to take an argument

There's nothing in the rules of Python to say that your function must take an argument.  It's perfectly possible to define a function with no arguments:

    def get_a_number():
    	return 42
	
but such functions tend not to be very useful. For example, we can write a version of `get_at_content()` that doesn't require any arguments by setting the value of the `dna` variable inside the function:

    def get_at_content(): 
    	dna = "ACTGATGCTAGCTA"
    	length = len(dna) 
    	a_count = dna.upper().count('A') 
    	t_count = dna.upper().count('T') 
    	at_content = (a_count + t_count) / length 
    	return round(at_content, 2) 
	
but that's obviously not very useful, as it calculates the AT content for the exact same sequence every time it's called! 

Occasionally you may be tempted to write a no-argument function that works like this:

    def get_at_content(): 
    	length = len(dna) 
    	a_count = dna.upper().count('A') 
    	t_count = dna.upper().count('T') 
    	at_content = (a_count + t_count) / length 
    	return round(at_content, 2) 
    
    dna = "ACTGATCGATCG"❶
    print(get_at_content())

At first this seems like a good idea – it works because the function gets the value of the `dna` variable that is set before the function call❶.  However, this breaks the encapsulation that we worked so hard to achieve. The function now only works if there is a variable called `dna` set in the bit of the code where the function is called, so the two pieces of code are no longer independent. 

If you find yourself writing code like this, it's usually a good idea to identify which variables from outside the function are being used inside it, and turn them into arguments. 

### Functions don't always have to return a value

Consider this variation of our function – instead of returning the AT content, this function prints it to the screen:

    def print_at_content(dna): 
    	length = len(dna) 
    	a_count = dna.upper().count('A') 
    	t_count = dna.upper().count('T') 
    	at_content = (a_count + t_count) / length 
    	print(str(round(at_content, 2)))
	
When you first start writing functions, it's very tempting to do this kind of thing. You think:

"OK, I need to calculate and print the AT content – I'll write a function that does both."

The trouble with this approach is that it results in a function that is less flexible. Right now you want to print the AT content to the screen, but what if you later discover that you want to write it to a file, or use it as part of some other calculation? You'll have to write more functions to carry out these tasks. 

The key to designing flexible functions is to recognize that the job *calculate and print the AT content* is actually two separate jobs – *calculating* the AT content, and *printing* it. Try to write your functions in such a way that they just do one job. You can then easily write code to carry out more complicated jobs by using your simple functions as building blocks. 

### Functions can be called with named arguments

What do we need to know about a function in order to be able to use it? We need to know what the return value and type is, and we need to know the number and type of the arguments. For the examples we've seen so far, we also need to know the order of the arguments. For instance, to use the `open()` function we need to know that the name of the file comes first, followed by the mode of the file. And to use our two-argument version of `get_at_content()` as described above, we need to know that the DNA sequence comes first, followed by the number of significant figures. 

There's a feature in Python called **keyword arguments** which allows us to call functions in a slightly different way. Instead of giving a list of arguments in parentheses:

    get_at_content("ATCGTGACTCG", 2)

we can supply a list of argument variable names and values like this:

    get_at_content(dna="ATCGTGACTCG", sig_figs=2)

This style of calling functions6 has several advantages. It doesn't rely on the order of arguments, so we can use whichever order we prefer. These two statements behave identically:

    get_at_content(dna="ATCGTGACTCG", sig_figs=2)
    get_at_content(sig_figs=2, dna="ATCGTGACTCG")

It's also clearer to read what's happening when the argument names are given explicitly. 

We can even mix and match the two styles of calling – the following are all identical:

    get_at_content("ATCGTGACTCG", 2)
    get_at_content(dna="ATCGTGACTCG", sig_figs=2)
    get_at_content("ATCGTGACTCG", sig_figs=2)

Although we're not allowed to start off with keyword arguments then switch back to normal – this will cause an error:

    get_at_content(dna="ATCGTGACTCG", 2) 

Keyword arguments can be particularly useful for functions and methods that have a lot of optional arguments, and we'll use them where appropriate in future examples. 

## Function arguments can have defaults

We've encountered function arguments with defaults before, when we were discussing opening files. Recall that the `open()` function takes two arguments – a filename and a mode string – but that if we call it with just a filename it uses a default value for the mode string. We can easily take advantage of this feature in our own functions – we simply specify the default value in the first line of the function definition❶. Here's a version of our `get_at_content()` function where the default number of significant figures is two:

    def get_at_content(dna, sig_figs=2):❶
        length = len(dna) 
        a_count = dna.upper().count('A') 
        t_count = dna.upper().count('T') 
        at_content = (a_count + t_count) / length 
        return round(at_content, sig_figs) 
    
Now we have the best of both worlds. If the function is called with two arguments, it will use the number of significant figures specified; if it's called with one argument, it will use the default value of two significant figures. Let's see some examples:

    get_at_content("ATCGTGACTCG")
    get_at_content("ATCGTGACTCG", 3)
    get_at_content("ATCGTGACTCG", sig_figs=4)

The function takes care of filling in the default value for `sig_figs` for the first function call where none is supplied:

>0.45
>0.455
>0.4545 

Function argument defaults allow us to write very flexible functions which can have varying numbers of arguments. It only makes sense to use them for arguments where a sensible default can be chosen – there's no point specifying a default for the `dna` argument in our example.  They are particularly useful for functions where some of the optional arguments are only going to be used infrequently. 

## Testing functions

When writing code of any type, it's important to periodically check that your code does what you intend it to do. If you look back over the solutions to exercises from the first few sections of this tutorial, you can see that we generally test our code at each step by printing some output to the screen and checking that it looks OK. For example when we were first calculating AT content, we used a very short test sequence to verify that our code worked before running it on the real input. 

The reason we used a test sequence was that, because it was so short, we could easily work out the answer manually and compare it to the answer given by our code. This idea – running code on a test input and comparing the result to an answer that we know to be correct1 – is such a useful one that Python has a built in tool for expressing it: `assert`. An assertion consists of the word `assert`, followed by a call to our function, then two equals signs, then the result that we expect.  

For example, we know that if we run our `get_at_content()` function on the DNA sequence "ATGC" we should get an answer of 0.5. This assertion will test whether that's the case:

    assert get_at_content("ATGC") == 0.5

Notice the two equals signs – we'll learn the reason behind that in the next section. The way that assertion statements work is very simple; if an assertion turns out to be false (i.e. if Python executes our function on the input "ATGC" and the answer isn't 0.5) then the program will stop and we will get an `AssertionError`.

Assertions are useful in a number of ways. They provide a means for us to check whether our functions are working as intended and therefore help us track down errors in our programs. If we get some unexpected output from a program that uses a particular function, and the assertion tests for that function all pass, then we can be confident that the error doesn't lie in the function but in the code that calls it. 

They also let us modify a function and check that we haven't introduced any errors. If we have a function that passes a series of assertion tests, and we make some changes to it, we can rerun the assertion tests and, assuming they all pass, be confident that we haven't broken the function. 

Assertions are also useful as a form of documentation. By including a collection of assertion tests alongside a function, we can show exactly what output is expected from a given input. 

Finally, we can use assertions to test the behaviour of our function for unusual inputs. For example, what is the expected behaviour of `get_at_content()` when given a DNA sequence that includes unknown bases (usually represented as N)? A sensible way to handle unknown bases would be to exclude them from the AT content calculation – in other words, the AT content for a given sequence shouldn't be affected by adding a bunch of unknown bases. We can write an assertion that expresses this:

    assert get_at_content("ATGCNNNNNNNNNN") == 0.5 

This assertions fails for the current version of `get_at_content()`. However, we can easily modify the function to remove all N characters before carrying out the calculation❶:
    def get_at_content(dna, sig_figs=2): 
        dna = dna.replace('N', '')❶ 
        length = len(dna) 
        a_count = dna.upper().count('A') 
        t_count = dna.upper().count('T') 
        at_content = (a_count + t_count) / length 
        return round(at_content, sig_figs) 
        
and now the assertion passes. 

It's common to group a collection of assertions for a particular function together to test for the correct behaviour on different types of input. Here's an example for `get_at_content()` which shows a range of different types of behaviour:

    assert get_at_content("A") == 1
    assert get_at_content("G") == 0
    assert get_at_content("ATGC") == 0.5
    assert get_at_content("AGG") == 0.33
    assert get_at_content("AGG", 1) == 0.3
    assert get_at_content("AGG", 5) == 0.33333

When we have a collection of tests like this, we often refer to it as a **test suite**.

### Recap

In this section, we've seen how packaging code into functions helps us to manage the complexity of large programs and promote code reuse. We learned how to define and call our own functions along with various new ways to supply arguments to functions. We also looked at a couple of things that are possible in Python, but rarely advisable – writing functions without arguments or return values. Finally, we explored the use of assertions to test our functions, and discussed how we can use them to catch errors before they become a problem. 

For a more structured approach to testing functions that's invaluable for larger programming projects, see the chapter on automated testing in Effective Python development for Biologists, which you can find [on the books page][1]. 

[1]: /python-books

The remaining sections of this tutorial will make use of functions in both the examples and the exercise solutions, so make sure you are comfortable with the new ideas from this section before moving on.

This chapter has covered the basics of writing and using functions, but there's much more we can do with them – in fact, there's a whole style of programming (functional programming) which revolves around the manipulation of functions. You'll find a discussion of this in the chapter in Advanced Python for Biologists called, unsurprisingly, functional programming. 

## Exercises

Both parts of the exercise for this section require you to test your answers with a collection of `assert` statements. Rather than typing them out, just copy and paste them from the exercise description into your program. Remember, you can always find solutions and explanations for all the exercises in the [Python for Biologists books][2]. 

[2]: /python-books

### Percentage of amino acid residues, part one

Write a function that takes two arguments – a protein sequence and an amino acid residue code – and returns the percentage of the protein that the amino acid makes up. Use the following assertions to test your function:

    assert my_function("MSRSLLLRFLLFLLLLPPLP", "M") == 5
    assert my_function("MSRSLLLRFLLFLLLLPPLP", "r") == 10
    assert my_function("msrslllrfllfllllpplp", "L") == 50
    assert my_function("MSRSLLLRFLLFLLLLPPLP", "Y") == 0
    
You'll have to change the function name `my_function` in the assert statements to whatever you decide to call your function. 

Reminder: if you're using Python 2 rather than Python 3, include this line at the top of your program:
from __future__ import division

### Percentage of amino acid residues, part two

Modify the function from part one so that it accepts a list of amino acid residues rather than a single one. If no list is given, the function should return the percentage of hydrophobic amino acid residues (A, I, L, M, F, W, Y and V). Your function should pass the following assertions:

    assert my_function("MSRSLLLRFLLFLLLLPPLP", ["M"]) == 5
    assert my_function("MSRSLLLRFLLFLLLLPPLP", ['M', 'L']) == 55
    assert my_function("MSRSLLLRFLLFLLLLPPLP", ['F', 'S', 'L']) == 70
    assert my_function("MSRSLLLRFLLFLLLLPPLP") == 65
    
## SOLUTIONS

You can find solutions to all the exercises, along with explanations of how they work, by signing up for the [online course](/python-for-biologists-online-course).
