Title: Python tutorial part two
Date: 2010-12-03 10:20
Modified: 2010-12-05 19:30
Category: tutorial
Tags: python, tutorial
slug: tutorial/text
Authors: Martin
Summary: Python tutorial part 2: manipulating text

## Why are we so interested in working with text?

Open the first page of a book about learning Python, and the chances are that the first examples of code you'll see will involve numbers. There's a good reason for that: numbers are generally simpler to work with than text – there are not too many things you can do with them (once you've got basic arithmetic out of the way) and so they lend themselves well to examples that are easy to understand.  It's also a pretty safe bet that the average person reading a programming book is doing so because they need to do some number-crunching. 

So what makes this website different – why is this first page about text rather than numbers? The answer is that, as biologists, we have a particular interest in dealing with text rather than numbers (though of course, we'll need to learn how to manipulate numbers too). Specifically, we're interested in particular types of text that we call sequences – the DNA and protein sequences that constitute much of the data that we deal with in biology.

There are other reasons that we have a greater interest in working with text than the average novice programmer. As scientists, the programs that we write often need to work as part of a pipeline, alongside other programs that have been written by other people. To do this, we'll often need to write code that can understand the output from some other program (we call this parsing) or produce output in a format that another program can operate on. Both of these tasks require manipulating text.

I've hinted above that computers consider numbers and text to be different in some way. That's an important idea, and one that we'll return to in more detail later. For now, I want to introduce an important piece of jargon – the word **string**. String is the word we use to refer to a piece of text in a computer program (it just means a string of characters). From this point on we'll use the word string when we're talking about computer code, and we'll reserve the word sequence for when we're discussing biological sequences like DNA and protein. 

## Printing a message to the screen

The first thing we're going to learn is how to print a message to the screen. Here's a line of Python code that will cause a friendly message to be printed. 

    print("Hello world")

Let's take a look at the various bits of this line of code, and give some of them names:

The whole line is called a **statement**.

`print()` is the name of a **function**. The function tells Python, in vague terms, what we want to do – in this case, we want to print some text. The function name is always followed by parentheses. 

The bits of text inside the parentheses are called the **arguments** to the function. In this case, we just have one argument (later on we'll see examples of functions that take more than one argument, in which case the arguments are separated by commas). 

The arguments tell Python what we want to do more specifically – in this case, the argument tells Python exactly what it is we want to print: the message "Hello World".

Assuming you've followed the instructions in the previous page and set up your Python environment, type the line of code above into your favourite text editor, save it, and run it. You should see a single line of output like this:

>Hello world

## Quotes are important

In normal writing, we only surround a bit of text in quotes when we want to show that they are being spoken. In Python, however, strings are always surrounded by quotes. That is how Python is able to tell the difference between the instructions (like the function name) and the data (the thing we want to print). We can use either single or double quotes for strings – Python will happily accept either. The following two statements behave exactly the same:

    print("Hello world")
    print('Hello world')

Let's take a look at the output to prove it:

>Hello world
>Hello world

You'll notice that the output above doesn't contain quotes – they are part of the code, not part of the string itself. If we do want to include quotes in the output, the easiest thing to do is use the other type of quotes for surrounding the string:

    print("She said, 'Hello world'")
    print('He said, "Hello world"')


The above code will give the following output:

>She said, 'Hello world'
>He said, "Hello world"

Be careful when writing and reading code that involves quotes – you have to make sure that the quotes at the beginning and end of the string match up. 

## Use comments to annotate your code

Occasionally, we want to write some text in a program that is for humans to read, rather than for the computer to execute. We call this type of line a comment. To include a comment in your source code, start the line with a hash symbol:

    # this is a comment, it will be ignored by the computer
    print("Comments are very useful!")

You're going to see a lot of comments in the source code examples in this book, and also in the solutions to the exercises. Comments are a very useful way to document your code, for a number of reasons:

- You can put the explanation of what a particular bit of code does right next to the code itself. This makes it much easier to find the documentation for a line of code that is in the middle of a large program, without having to search through a separate document.
- Because the comments are part of the source code, they can never get mixed up or separated. In other words, if you are looking at the source code for a particular program, then you automatically have the documentation as well. In contrast, if you keep the documentation in a separate file, it can easily become separated from the code.
- Having the comments right next to the code acts as a reminder to update the documentation whenever you change the code. The only thing worse than undocumented code is code with old documentation that is no longer accurate!

Don't make the mistake, by the way, of thinking that comments are only useful if you are planning on showing your code to somebody else. When you start writing your own code, you will be amazed at how quickly you forget the purpose of a particular section or statement. If you are working on a program on Friday afternoon, then come back to it on Monday morning, it will probably take you quite a while to pick up where you left off. 

Comments can help with this problem by giving you hints about the purpose of code, meaning that you spend less time trying to understand your old code, thus speeding up your progress. A side benefit is that writing a comment for a bit of code reinforces your understanding at the time you are doing it. A good habit to get into is writing a quick one-line comment above any line of code that does something interesting:

    # print a friendly greeting
    print("Hello world")

You'll see this technique used a lot in the code examples on this website, and I encourage you to use it for your own code as well.

## Error messages and debugging

It may seem depressing to be talking about errors so soon! However, it's worth pointing out at this early stage that computer programs almost never work correctly the first time. Programming languages are not like natural languages – they have a very strict set of rules, and if you break any of them, the computer will not attempt to guess what you intended, but instead will stop running and present you with an error message. You're going to be seeing a lot of these error messages in your programming career, so let's get used to them as soon as possible. 

### Forgetting quotes

Here's one possible error we can make when printing a line of output – we can forget to include the quotes:

    print(Hello world)
This is easily done, so let's take a look at the output we'll get if we try to run the above code:

>  File "error.py", line 1❶
>    print(Hello world) 
>                    ^❷
>SyntaxError❸: invalid syntax 

Looking at the output, we see that the error occurs on the first line of the file❶.  Python's best guess at the location of the error is just before the close parentheses❷. Depending on the type of error, this can be wrong by quite a bit, so don't rely on it too much! 

The type of error is a `SyntaxError`❸, which mean that Python can't understand the code – it breaks the rules in some way (in this case, the rule that strings must be surrounded by quotation marks). We'll see different types of errors later.

### Spelling mistakes

What happens if we miss-spell the name of the function?:

    prin("Hello world")

We get a different type of error – a `NameError` – and the error message is a bit more helpful:

    Traceback (most recent call last): 
       File "error.py", line 1, in <module> 
        prin("Hello world")❶ 
    NameError: name 'prin'❷ is not defined 

This time, Python doesn't try to show us where on the line the error occurred, it just shows us the whole line❶ . The error message tells us which word Python doesn't understand❷, so in this case, it's quite easy to fix. 

## Splitting a statement over two lines

What if we want to print some output that spans multiple lines? For example, we want to print the word "Hello" on one line and then the word "World" on the next line – like this:

>Hello
>World

We might try putting a new line in the middle of our string like this:

    print("Hello
    World")

but that won't work and we'll get the following error message:

>File "error.py", line 1
>print("Hello ❶
>                ^ 
>SyntaxError: EOL while scanning string literal❷
    
Python finds the error when it gets to the end of the first line of code❶. The error message❷ is a bit more cryptic than the others. EOL stands for End Of Line, and string literal means a string in quotes. So to put this error message in plain English: "I started reading a string in quotes, and I got to the end of the line before I came to the closing quotation mark".

If splitting the line up doesn't work, then how do we get the output we want.....?

## Printing special characters

The reason that the code above didn't work is that Python got confused about whether the new line was part of the string (which is what we wanted) or part of the source code (which is how it was actually interpreted). What we need is a way to include a new line as part of a string, and luckily for us, Python has just such a tool built in. To include a new line, we write a backslash followed by the letter n – Python knows that this is a special character and will interpret it accordingly. Here's the code which prints "Hello world" across two lines:

    # how to include a newline in the middle of a string
    print("Hello\nworld")

Notice that there's no need for a space before or after the newline. 

There are a few other useful special characters as well, all of which consist of a backslash followed by a letter. The only ones which you are likely to need are the tab character (\t) and the carriage return character (\r).  The tab character can sometimes be useful when writing a program that will produce a lot of output. The carriage return character works a bit like a newline in that it puts the cursor back to the start of the line, but doesn't actually start a new line, so you can use it to overwrite output – this is sometimes useful for long-running programs. 

## Storing strings in variables

OK, we've been playing around with the `print()` function for a while; let's introduce something new. We can take a string and assign a name to it using an equals sign – we call this a variable:

    # store a short DNA sequence in the variable my_dna
    my_dna = "ATGCGTA"

The variable `my_dna` now points to the string "ATGCGTA". We call this assigning a variable, and once we've done it, we can use the variable name instead of the string itself – for example, we can use it in a `print()` statement:

    # store a short DNA sequence in the variable my_dna
    my_dna = "ATGCGTA"
    
    # now print the DNA sequence
    print(my_dna)

Notice that when we use the variable in a `print()` statement, we don't need any quotation marks – the quotes are part of the string, so they are already "built in" to the variable `my_dna`. Also notice that this example includes a blank line to separate the different bits and make it easier to read. We are allowed to put as many blank lines as we like in our programs when writing Python – the computer will ignore them. 

A common error is to include quotes around a variable name:

    my_dna = "ATGCGTA"
    print("my_dna")
    
but if we do this, then Python prints the name of the variable rather than its contents:

>my_dna

We can change the value of a variable as many times as we like once we've created it:

    my_dna = "ATGCGTA"
    print(my_dna)
    
    # change the value of my_dna
    my_dna = "TGGTCCA" 
    
Here's a very important point that trips many beginners up: variable names are **arbitrary** – that means that we can pick whatever we like to be the name of a variable. So our code above would work in exactly the same way if we picked a different variable name:

    # store a short DNA sequence in the variable banana
    banana = "ATGCGTA"
    
    # now print the DNA sequence
    print(banana)
    
What makes a good variable name? Generally, it's a good idea to use a variable name that gives us a clue as to what the variable refers to. In this example, `my_dna` is a good variable name, because it tells us that the content of the variable is a DNA sequence. Conversely, `banana` is a bad variable name, because it doesn't really tell us anything about the value that's stored.  As you read through the code examples in these pages, you'll get a better idea of what constitutes good and bad variable names. 

This idea – that names for things are arbitrary, and can be anything we like – is a theme that will occur many times, so it's important to keep it in mind. Occasionally you will see a variable name that looks like it has some sort of relationship with the value it points to:

    my_file = "my_file.txt"

but don't be fooled! Variable names and strings are separate things.

I said above that variable names can be anything we want, but it's actually not quite that simple – there are some rules we have to follow. We are only allowed to use letters, numbers, and underscores, so we can't have variable names that contain odd characters like £, ^ or %. We are not allowed to start a name with a number (though we can use numbers in the middle or at the end of a name).  Finally, we can't use a word that's already built in to the Python language like "print". 

It's also important to remember that variable names are case sensitive, so `my_dna`, `MY_DNA`, `My_DNA` and `My_Dna` are all different variables. Technically this means that you could use all four of those names in a Python program to store different values, but please don't do this – it is very easy to become confused when you use very similar variable names. 

## Tools for manipulating strings

Now we know how to store and print strings, we can take a look at a few of the facilities that Python has for manipulating them. Python has many built in tools for carrying out common operations, and in this next section we'll take a look at them one-by-one. 

### Concatenation
We can concatenate (stick together) two strings using the `+` symbol. This symbol will join together the string on the left with the string on the right:

    my_dna = "AATT" + "GGCC"
    print(my_dna)

Let's take a look at the output:

>AATTGGCC

In the above example, the things being concatenated were strings, but we can also use variables that point to strings:

    upstream = "AAA"
    my_dna = upstream + "ATGC"
    # my_dna is now "AAAATGC"
    
We can even join multiple strings together in one go:

    upstream = "AAA"
    downstream = "GGG"
    my_dna = upstream + "ATGC" + downstream
    # my_dna is now "AAAATGCGGG"
    
It's important to realize that the result of concatenating two strings together is itself a string. So it's perfectly OK to use a concatenation inside a `print` statement:

    print("Hello" + " " + "world")

As we'll see in the rest of these pages, using one tool inside another is quite a common thing to do in Python.

### Finding the length of a string

Another useful built in tool in Python is the `len()` function (len is short for length). Just like the `print()` function, the `len()` function takes a single argument (take a quick look back at when we were discussing the `print()` function for a reminder about what arguments are) which is a string. However, the behaviour of `len()` is quite different to that of `print()`. Instead of outputting text to the screen, `len()` outputs a value that can be stored – we call this the **return value**. In other words, if we write a program that uses `len()` to calculate the length of a string, the program will run but we won't see any output:

    # this line doesn't produce any output
    len("ATGC")

If we want to actually use the return value, we need to store it in a variable, and then do something useful with it (like printing it):

    dna_length = len("AGTC")
    print(dna_length)

There's another interesting thing about the `len()` function: the result (or return value) is not a string, it's a number. This is a very important idea so I'm going to write it out in bold: **Python treats strings and numbers differently.**

We can see that this is the case if we try to concatenate together a number and a string. Consider this short program which calculates the length of a DNA sequence and then prints a message telling us the length:

    # store the DNA sequence in a variable
    my_dna = "ATGCGAGT"
    
    # calculate the length of the sequence and store it in a variable
    dna_length = len(my_dna)
    
    # print a message telling us the DNA sequence lenth
    print("The length of the DNA sequence is " + dna_length)
    
When we try to run this program, we get the following error:

>    print("The length of the DNA sequence is " + dna_length) 
>TypeError: cannot concatenate 'str' and 'int' objects❶

The error message❶ is short but informative: "cannot concatenate 'str' and 'int' objects". Python is complaining that it doesn't know how to concatenate a string (which it calls `str` for short) and a number (which it calls `int` – short for integer).  Strings and numbers are examples of types – different kinds of information that can exist inside a program.

Happily, Python has a built in solution – a function called `str()` which turns a number into a string so that we can print it. Here's how we can modify our program to use it – I've removed the comments from this version to make it a bit more compact:

    my_dna = "ATGCGAGT"
    dna_length = len(my_dna)
    
    print("The length of the DNA sequence is " + str(dna_length))

The only thing we have changed is that we've replace `dna_length` with `str(dna_length)` inside the `print()` statement. Notice that because we're using one function (`str()`) inside another function (`print()`), our statement now ends with two closing parentheses. 

Let's take a moment to refresh our memory of all the new terms we've learned by writing out what we need to know about the `str()` function: 

*`str()` is a function which takes one argument (whose type is number), and returns a value (whose type is string) representing that number.* 

If you're unsure about the meanings of any of the words in italics, skip back to the earlier parts of this page where we discussed them. Understanding how types work is key to avoiding many of the frustrations which new programmers typically encounter, so make sure the idea is clear in your mind before moving on with the rest of this page.

Sometimes we need to go the other way – we have a string that we need to turn into a number. The function for doing this is called `int()`, which is short for integer. It takes a string as its argument and returns a number:

    number = 3 + int('4')
    # number is now 7
    
We won't need to use `int()` for a while, but once we start reading information from files later on it will become very useful. 

### Changing case

We can convert a string tolower case by using a new type of syntax – a **method** that belongs to strings. A method is like a function, but instead of being built in to the Python language, it belongs to a particular type. The method we are talking about here is called `lower()`, and we say that it belongs to the string type. Here's how we use it:

    my_dna = "ATGC"
    # print my_dna in lower case
    print(my_dna.lower())

Notice how using a method looks different to using a function. When we use a function like `print()` or `len()`, we write the function name first and the arguments go in parentheses:

    print("ATGC")
    len(my_dna)

When we use a method, we write the name of the variable first, followed by a period, then the name of the method, then the method arguments in parentheses. For the example we're looking at here, `lower()`, there is no argument, but we still need to put the opening and closing parentheses. 

It's important to notice that the `lower()` method does not actually change the variable; instead it returns a copy of the variable in lower case. We can prove that it works this way by printing the variable before and after running `lower()`. Here's the code to do so:

    my_dna = "ATGC"
    
    # print the variable
    print("before: " + my_dna)

    # run the lower method and store the result
    lowercase_dna = my_dna.lower()
    
    # print the variable again
    print("after: " + my_dna)
    
and here's the output we get:

>before: ATGC 
>after: ATGC 

Just like the `len()` function, in order to actually do anything useful with the `lower()` method, we need to store the result (or print it right away).

Because the `lower()` method belongs to the string type, we can only use it on variables that are strings. If we try to use it on a number:

    my_number = len("AGTC")
    # my_number is 4
    print(my_number.lower())
    
we will get an error that looks like this:

>AttributeError: 'int' object has no attribute 'lower'

The error message is a bit cryptic, but hopefully you can grasp the meaning: something that is a number (an int, or integer) does not have a `lower()` method. This is a good example of the importance of types in Python code: we can only use methods on the type that they belong to.

Before we move on, let's just mention that there is another method that belongs to the string type called `upper()` – you can probably guess what it does! 

## Replacement

Here's another example of a useful method that belongs to the string type: `replace()`. `replace()` is slightly different from anything we've seen before – it takes two arguments (both strings) and returns a copy of the variable where all occurrences of the first string are replaced by the second string. That's quite a long-winded description, so here are a few examples to make things clearer:

    protein = "vlspadktnv"
    
    # replace valine with tyrosine
    print(protein.replace("v", "y"))
    
    # we can replace more than one character
    print(protein.replace("vls", "ymt"))
    
    # the original variable is not affected
    print(protein)

And this is the output we get:

>ylspadktny<br/> 
>ymtpadktnv<br/>
>vlspadktnv <br/>

Notice that in the first line out output, both "v" characters have been replaced with "y". We'll take a look at more tools for carrying out string replacement when we look at regular expressions.

## Extracting part of a string

What do we do if we have a long string, but we only want a short portion of it? This is known as taking a **substring**, and it has its own notation in Python. To get a substring, we follow the variable name with a pair of square brackets which enclose a start and stop position, separated by a colon. Again, this is probably easier to visualize with a couple of examples – let's reuse our protein sequence from before:

    protein = "vlspadktnv"
    
    # print positions three to five
    print(protein[3:5])
    
    # positions start at zero, not one
    print(protein[0:6])
    
    # if we miss out the last number, it goes to the end of the string
    print(protein[2:])

and here's the output:

>pa
>vlspad
>spadktnv

There are two important things to notice here. Firstly, we actually start counting from position zero, rather than one – in other words, position 3 is actually the fourth character.  This explains why the first character of the first line of output is `p` and not `s` as you might think.  Secondly, the positions are inclusive at the start, but exclusive at the stop. In other words, the expression `protein[3:5]` gives us everything starting at the fourth character, and stopping just before the sixth character (i.e. characters four and five). 
If we just give a single number in the square brackets, we'll just get a single character:

    protein = "vlspadktnv"
    first_residue = protein[2]

We'll learn a lot more about this type of notation, and what we can do with it, in the next section. 

## Counting and finding substrings

A very common job in biology is to count the number of times some pattern occurs in a DNA or protein sequence. In computer programming terms, what that translates to is counting the number of times a substring occurs in a string. The method that does the job is called `count()`. It takes a single argument whose type is string, and returns the number of times that the argument is found in the variable. The return type is a number, so be careful about how you use it!

Let's use our protein sequence one last time as an example. Remember that we have to use our old friend `str()` to turn the counts into strings so that we can print them. 

    protein = "vlspadktnv"
    # count amino acid residues
    valine_count = protein.count('v')
    lsp_count = protein.count('lsp')
    tryptophan_count = protein.count('w')
    
    # now print the counts
    print("valines: " + str(valine_count))
    print("lsp: " + str(lsp_count))
    print("tryptophans: " + str(tryptophan_count))

The output shows how the `count()` method behaves:

>valines: 2 
>lsp: 1 
>tryptophans: 0 

A closely related problem to counting substrings is finding their location. What if instead of counting the number of proline residues in our protein sequence we want to know where they are? The `find()` method will give us the answer, at least for simple cases. `find()` takes a single string argument, just like `count()`, and returns a number which is the position at which that substring first appears in the string (in computing, we call that the **index** of the substring). 

Remember that in Python we start counting from zero rather than one, so position 0 is the first character, position 4 is the fifth character, etc. A couple of examples:

    protein = "vlspadktnv" 
    print(str(protein.find('p'))) 
    print(str(protein.find('kt'))) 
    print(str(protein.find('w'))) 

And the output:

>3 
>6
>-1 

Notice the behaviour of `find()` when we ask it to locate a substring that doesn't exist – we get -1 as the answer. 

Both `count()` and `find()` have a pretty serious limitation: you can only search for exact substrings. If you need to count the number of occurrences of a variable protein motif, or find the position of a variable transcription factor binding site, they will not help you. Take a look at the page on regular expressions for a set of tools that can do those kinds of jobs. 

Of the tools we've discussed in this section, three – `replace()`, `count()` and `find()` – require at least two strings to work, so be careful that you don't get confused about the order – remember that:

    my_dna.count(my_motif) 

is not the same as:

    my_motif.count(my_dna)

## Splitting up a string into multiple bits

An obvious question which biologists often ask when learning to program is "how do we split a string (e.g. a DNA sequence) into multiple pieces?" That's a common job in biology, but unfortunately we can't do it yet using the tools from this chapter. We'll talk about various different ways of splitting strings soon. I mention it here just to reassure you that we will learn how to do it eventually!

### Recap

We started this page talking about strings and how to work with them, but along the way we had to take a lot of diversions, all of which were necessary to understand how the different string tools work. Thankfully, that means that we've covered most of the nuts and bolts of the Python language, which will make future sections go much more smoothly.

We've learned about some general features of the Python programming language like

- the difference between functions, statements and arguments
- the importance of comments and how to use them
- how to use Python's error messages to fix bugs in our programs
- how to store values in variables
- the way that types work, and the importance of understanding them
- the difference between functions and methods, and how to use them both
- And we've encountered some tools that are specifically for working with strings:
- concatenation
- different types of quotes
- special characters
- changing the case of a string
- finding and counting substrings 
- replacing bits of a string with something new
- extracting bits of a string to make a new string

Many of the above topics will crop up again in future sections, and will be discussed in more detail, but you can always return to this page if you want to brush up on the basics. The exercises for this section will allow you to practice using the string manipulation tools and to become familiar with them. They'll also give you the chance to practice building bigger programs by using the individual tools as building blocks.

## Exercises

Reminder: the descriptions of the exercises are brief and may be kind of ambiguous – just like requirements for programs you will write in real life! You can always find complete solutions in the [Python for Biolgoists books][1]. 

[1]: /python-books

### Calculating AT content
Here's a short DNA sequence:

    ACTGATCGATTACGTATAGTATTTGCTATCATACATATATATCGATGCGTTCAT

Write a program that will print out the AT content of this DNA sequence (i.e. the proportion of bases that are either A or T). Hint: you can use normal mathematical symbols like add (+), subtract (-), multiply (\*), divide (/) and parentheses to carry out calculations on numbers in Python. 

Reminder: if you're using Python 2 rather than Python 3, include this line at the top of your program:

    from __future__ import division

### Complementing DNA 

Here's a short DNA sequence:

    ACTGATCGATTACGTATAGTATTTGCTATCATACATATATATCGATGCGTTCAT

Write a program that will print the complement of this sequence. To find the complement we replace each base with its pair: A with T, T with A, C with G and G with C. 

### Restriction fragment lengths

Here's a short DNA sequence:

    ACTGATCGATTACGTATAGTAGAATTCTATCATACATATATATCGATGCGTTCAT

The sequence contains a recognition site for the EcoRI restriction enzyme, which cuts at the motif `G*AATTC` (the position of the cut is indicated by an asterisk). Write a program which will calculate the size of the two fragments that will be produced when the DNA sequence is digested with EcoRI.

### Splicing out introns, part one

Here's a short section of genomic DNA:

    ATCGATCGATCGATCGACTGACTAGTCATAGCTATGCATGTAGCTACTCGATCGATCGATCGATCGATCGATCGATCGATCGATCATGCTATCATCGATCGATATCGATGCATCGACTACTAT

It comprises two exons and an intron. The first exon runs from the start of the sequence to base number 63 (starting counting from zero), and the second exon runs from base 91 (also counting from zero) to the end of the sequence. Write a program that will print just the coding regions of the DNA sequence. 

### Splicing out introns, part two

Using the data from part one, write a program that will calculate what percentage of the DNA sequence is coding.

Reminder: if you're using Python 2 rather than Python 3, include this line at the top of your program:
from __future__ import division

### Splicing out introns, part three

Using the data from part one, write a program that will print out the original genomic DNA sequence with coding bases in uppercase and non-coding bases in lowercase. 

## Solutions

You can find solutions to all the exercises, along with explanations of how they work, by signing up for the [online course](/python-for-biologists-online-course).
