Title: Python tutorial part four
Date: 2010-12-03 10:20
Modified: 2010-12-05 19:30
Category: tutorial
Tags: python, tutorial
slug: tutorial/lists
Authors: Martin
Summary: Python tutorial part 4: lists and loops


## Why do we need lists and loops?

Think back over the examples that we've seen in the previous two pages – they've all involved dealing with one bit of information at a time. In the first section, we used string manipulation tools to process single sequences, and in the second section, we practised reading and writing files one at a time. 

If that's all that Python allowed us to do, it wouldn't be a very helpful tool for biology. In fact, there's a good chance that you're reading this website because you want to be able to write programs to help you deal with large datasets. A very common situation in biological research is to have a large collection of data (DNA sequences, SNP positions, gene expression measurements) that all need to be processed in the same way. In this section, we'll learn about the fundamental programming tools that will allow our programs to do this. 

So far we have learned about several different data types (strings, numbers, and file objects), all of which store a single bit of information. When we've needed to store multiple bits of information (for example, several DNA sequences) we have simply created more variables to hold them:

    # set the values of all the sequence variables 
    seq_1 = "ATCGTACGATCGATCGATCGCTAGACGTATCG" 
    seq_2 = "actgatcgacgatcgatcgatcacgact" 
    seq_3 = "ACTGAC-ACTGT—ACTGTA----CATGTG" 

The limitations of this approach became clear quite quickly as we looked at the solution code – it only worked because the number of sequences were small, and we knew the number in advance. If we were to repeat the exercise with three hundred or three thousand sequences, the vast majority of the code would be given over to storing variables and it would become completely unmanageable.  And if we were to try and write a program that could process an unknown number of input sequences (for instance, by reading them from a file), we wouldn't be able to do it. To make our programs able to process multiple pieces of data, we need an entirely new type of structure which can hold many pieces of information at the same time – a **list**. 

We've also dealt exclusively with programs whose statements are executed from top to bottom in a very straightforward way. This has great advantages when first starting to think about programming – it makes it very easy to follow the flow of a program. The downside of this sequential style of programming, however, is that it leads to very redundant code - for instance, if we need to create three output files it will look like this:

    # make three files to hold the output 
    output_1 = open(header_1 + ".fasta", "w") 
    output_2 = open(header_2 + ".fasta", "w") 
    output_3 = open(header_3 + ".fasta", "w") 

This only works if we knew in advance the number of output files we were going to need. Looking at the code, it's clear that these three lines consist of essentially the same statement being executed multiple times, with some slight variations. This idea of repetition-with-variation is incredibly common in programming problems, and Python has built in tools for expressing it – **loops**.

## Creating lists and retrieving elements

To make a new list, we put several strings or numbers inside square brackets, separated by commas:

    apes = ["Homo sapiens", "Pan troglodytes", "Gorilla gorilla"]
    conserved_sites = [24, 56, 132]

Each individual item in a list is called an **element**. To get a single element from the list, write the variable name followed by the index of the element you want in square brackets:

    apes = ["Homo sapiens", "Pan troglodytes", "Gorilla gorilla"]
    conserved_sites = [24, 56, 132]
    print(apes[2])
    first_site = conserved_sites[2]

If we want to go in the other direction – i.e. we know which element we want but we don't know the index – we can use the `index()` method:

    apes = ["Homo sapiens", "Pan troglodytes", "Gorilla gorilla"]
    chimp_index = apes.index("Pan troglodytes")
    # chimp_index is now 1
    
Remember that in Python we start counting from zero rather than one, so the first element of a list is always at index zero. If we give a negative number, Python starts counting from the end of the list rather than the beginning – so it's easy to get the last element from a list:

    last_ape = apes[-1]

What if we want to get more than one element from a list? We can give a start and stop position, separated by a colon, to specify a range of elements:

    ranks = ["kingdom","phylum", "class", "order", "family"]
    lower_ranks = ranks[2:5]
    # lower ranks are class, order and family

Numbers are inclusive at the start and exclusive at the end. 

### Working with list elements

To add another element onto the end of an existing list, we can use the `append()` method:

    apes = ["Homo sapiens", "Pan troglodytes", "Gorilla gorilla"]
    print(apes)
    apes.append("Pan paniscus")
    print(apes)
    
`append()` is an interesting method because it actually changes the variable on which it's used – in the above example, the `apes` list goes from having three elements to having four:

>['Homo sapiens', 'Pan troglodytes', 'Gorilla gorilla'] 
>['Homo sapiens', 'Pan troglodytes', 'Gorilla gorilla', 'Pan paniscus'] 

We can get the length of a list by using the `len()` function, just like we did for strings:

    apes = ["Homo sapiens", "Pan troglodytes", "Gorilla gorilla"]
    print("There are " + str(len(apes)) + " apes")
    apes.append("Pan paniscus")
    print("Now there are " + str(len(apes)) + " apes")

The output shows that the number of elements in `apes` really has changed:

    There are 3 apes 
    Now there are 4 apes 

We can concatenate two lists just as we did with strings, by using the plus symbol:

    apes = ["Homo sapiens", "Pan troglodytes", "Gorilla gorilla"]
    monkeys = ["Papio ursinus", "Macaca mulatta"]
    primates = apes + monkeys
    
    print(str(len(apes)) + " apes")
    print(str(len(monkeys)) + " monkeys")
    print(str(len(primates)) + " primates")

As we can see from the output, this doesn't change either of the two original lists – it makes a brand new list which contains elements from both:

>3 apes 
>2 monkeys 
>5 primates 

If we want to add elements from a list onto the end of an existing list, changing it in the process, we can use the `extend()` method. `extend()` behaves like `append()` but takes a list as its argument rather than a single element. 

Here are two more list methods that change the variable they're used on: `reverse()` and `sort()`. Both `reverse()` and `sort()` work by changing the order of the elements in the list. If we want to print out a list to see how this works, we need to used `str()` (just as we did when printing out numbers):

    ranks = ["kingdom","phylum", "class", "order", "family"]
    print("at the start : " + str(ranks))
    
    ranks.reverse()
    print("after reversing : " + str(ranks))
    
    ranks.sort()
    print("after sorting : " + str(ranks))

If we take a look at the output, we can see how the order of the elements in the list is changed by these two methods:

>at the start : ['kingdom', 'phylum', 'class', 'order', 'family'] 
>after reversing : ['family', 'order', 'class', 'phylum', 'kingdom'] 
>after sorting : ['class', 'family', 'kingdom', 'order', 'phylum'] 

By default, Python sorts strings in alphabetical order and numbers in ascending numerical order. 

## Writing a loop

Imagine we wanted to take our list of apes:

    apes = ["Homo sapiens", "Pan troglodytes", "Gorilla gorilla"]

and print out each element on a separate line, like this:

>Homo sapiens is an ape
>Pan troglodytes is an ape
>Gorilla gorilla is an ape

One way to do it would be to just print each element separately:

    print(apes[0] + " is an ape")
    print(apes[1] + " is an ape")
    print(apes[2] + " is an ape")
    
but this is very repetitive and relies on us knowing the number of elements in the list. What we need is a way to say something along the lines of 

*For each element in the list of apes, print out the element, followed by the words "is an ape".* 

Python's loop syntax allows us to express those instructions like this:

    for ape in apes:
	    print(ape + " is an ape")

Let's take a moment to look at the different parts of this loop. We start by writing `for x in y`, where `y` is the name of the list we want to process and `x` is the name we want to use for the current element each time round the loop. 

`x` is just a variable name (so it follows all the rules that we've already learned about variable names), but it behaves slightly differently to all the other variables we've seen so far. In all previous examples, we create a variable and store something in it, and then the value of that variable doesn't change unless we change it ourselves. In contrast, when we create a variable to be used in a loop, we don't set its value – the value of the variable will be automatically set to each element of the list in turn, and it will be different each time round the loop. 

This first line of the loop ends with a colon, and all the subsequent lines (just one, in this case) are indented. Indented lines can start with any number of tab or space characters, but they must all be indented in the same way. This pattern – a line which ends with a colon, followed by some indented lines – is very common in Python, and we'll see it in several more places throughout this book.  A group of indented lines is often called a **block** of code.

In this case, we refer to the indented block as the **body** of the loop, and the lines inside it will be executed once for each element in the list. To refer to the current element, we use the variable name that we wrote in the first line. The body of the loop can contain as many lines as we like, and can include all the functions and methods that we've learned about, with one important exception: we're not allowed to change the list while inside the body of the loop. 

Here's an example of a loop with a more complicated body:

    apes = ["Homo sapiens", "Pan troglodytes", "Gorilla gorilla"]    
    
    for ape in apes:
    	name_length = len(ape)
    	first_letter = ape[0]
    	print(ape + " is an ape. Its name starts with " + first_letter)
    	print("Its name has " + str(name_length) + " letters")

The body of the loop in the code above has four statements, two of which are `print()` statements, so each time round the loop we'll get two lines of output. If we look at the output we can see all six lines:

>Homo sapiens is an ape. Its name starts with H
>Its name has 12 letters 
>Pan troglodytes is an ape. Its name starts with P
>Its name has 15 letters 
>Gorilla gorilla is an ape. Its name starts with G
>Its name has 15 letters 

Why is the above approach better than printing out these six lines in six separate statements? Well, for one thing, there's much less redundancy – here we only needed to write two `print()` statements. This also means that if we need to make a change to the code, we only have to make it once rather than three separate times. Another benefit of using a loop here is that if we want to add some elements to the list, we don't have to touch the loop code at all. Consequently, it doesn't matter how many elements are in the list, and it's not a problem if we don't know how many are going to be in it at the time when we write the code.


### Indentation errors

Unfortunately, introducing tools like loops that require an indented block of code also introduces the possibility of a new type of error – an `IndentationError`. Notice what happens when the indentation of one of the lines in the block does not match the others❶:

    apes = ["Homo sapiens", "Pan troglodytes", "Gorilla gorilla"]
    
    for ape in apes:
    	name_length = len(ape)
      first_letter = ape[0]❶
	    print(ape + " is an ape. Its name starts with " + first_letter)
    	print("Its name has " + str(name_length) + " letters")

When we run this code, we get an error message before the program even starts to run:

>IndentationError: unindent does not match any outer indentation level

When you encounter an `IndentationError`, go back to your code and check that all the lines in the block match up. Also check that you are using either tabs or spaces for indentation, not both. The easiest way to do this, as mentioned at the start of this course, is to enable tab emulation in your text editor.

## Using a string as a list
We've already seen how a string can pretend to be a list – we can use list index notation to get individual characters or substrings from inside a string. Can we also use loop notation to process a string as though it were a list? Yes – if we write a loop statement with a string in the position where we'd normally find a list, Python treats each character in the string as a separate element. This allows us to very easily process a string one character at a time:

    name = "python"
    for character in name:
    	print("one character is " + character)


In this case, we're just printing each individual character:

>one character is p
>one character is y 
>one character is t 
>one character is h 
>one character is o 
>one character is n 

The process of repeating a set of instructions for each element of a list (or character in a string) is called **iteration**, and we often talk about **iterating over** a list or string. 

### Splitting a string to make a list

So far in this chapter, all our lists have been written manually. However, there are plenty of functions and methods in Python that produce lists as their output. One such method that is particularly interesting to biologists is the `split()` method which works on strings. `split()` takes a single argument, called the **delimiter**, and splits the original string wherever it sees the delimiter, producing a list. Here's an example:

    names = "melanogaster,simulans,yakuba,ananassae"
    species = names.split(",")
    print(str(species))

We can see from the output that the string has been split wherever there was a comma leaving us with a list of strings:

['melanogaster', 'simulans', 'yakuba', 'ananassae']

Of course, once we've created a list in this way we can iterate over it using a loop, just like any other list.

### Iterating over lines in a file

Another very useful thing that we can iterate over is a file. Just as a string can pretend to be a list for the purposes of looping, a file object can do the same trick. When we treat a string as a list, each character becomes an individual element, but when we treat a file as a list, each line becomes an individual element. This makes processing a file line by line very easy:

    file = open("some_input.txt")
    for line in file:
    	# do something with the line
    	
Notice that in this example we are iterating over the file object, not over the file contents. If we iterate over the file contents like this:

    file = open("some_input.txt")
    contents = file.read()
    for line in contents:
    	# warning: line contains just a single character!
    	
then each time round the loop we will be dealing with a single character, which is probably not what we want. A good way to avoid this mistake is to ask yourself, whenever you open a file, whether you want to get the contents as one big string (in which case you should use `read()`) or line-by-line (in which case you should iterate over the file object).

Another common pitfall is to iterate over the same file object twice:

    file = open("some_input.txt")
    
    # print the length of each line
    for line in file:
    	print("The length is " + str(len(line)))

    # print the first character of each line
    for line in file:
    	print("The first character is " + line[2])
    	
If we run this code, we'll find that the second `for` loop never gets executed. The reason for this is that file objects are **exhaustible**. Once we have iterated over a file object, Python "remembers" that it is already at the end of the file, so when we try to iterate over it again, there are no lines remaining to be read. One way round this problem is to close and reopen the file each time we want to iterate over it:

    # print the length of each line
    file = open("some_input.txt")
    for line in file:
    	print("The length is " + str(len(line)))
    file.close()
    
    # print the first character of each line
    file = open("some_input.txt")
    for line in file:
    	print("The first character is " + line[2])
    file.close()
    
A better approach is to read the lines of the file into a list, then iterate over the list (which we can safely do multiple times). The file object `readlines()` method returns a list of all the lines in a file, and we can use it like this:

    # first store a list of lines in the file
    file = open("some_input.txt")
    all_lines = file.readlines()
    
    # print the lengths
    for line in all_lines:
    	print("The length is " + str(len(line)))
    
    # print the first characters
    for line in all_lines:
    	print("The first character is " + line[2])

### Looping with ranges

Sometimes we want to iterate over a list of numbers. Imagine we have a protein sequence:

    protein = "vlspadktnv" 

and we want to print out the first three residues, then the first four residues, etc. etc.:

>vls
>vlsp
>vlspa
>vlspad
>...etc...

We can use the substring notation that we learned on the previous page to extract the bit of the name we want to print. If we try writing it without a loop, then we get very repetitive code:

    print(protein[0:1])
    print(protein[0:2])
    print(protein[0:3])
    ...

Looking at this code, the structure of the problem becomes clear: each time we print out a line, the end position of the substring needs to increase by one. Obviously we need a loop to do this, but what are we going to iterate over? We can't just iterate over the protein string, because that will give us individual residues, which is not what we want. We can manually assemble a list of stop positions, and iterate over that:

    stop_positions = [3,4,5,6,7,8,9,10]
    for stop in stop_positions:
    	substring = protein[0:stop]
    	print(substring)
	
but this seems cumbersome, and only works if we know the length of the protein sequence in advance. 
A better solution is to use the `range()` function. `range()` is a built in Python function that generates lists of numbers. The behaviour of `range()` depends on how many arguments we give it. Below are a few examples, with the output following directly after the code. 

With a single argument, `range()` will count up from zero to that number, excluding the number itself: 

    for number in range(6):
    	print(number)

    
>0
>1
>2
>3
>4
>5

With two numbers, `range()` will count up from the first number (inclusive) to the second (exclusive):

    for number in range(3, 8):
    	print(number)


>3
>4
>5
>6
>7

With three numbers, `range()` will count up from the first to the second with the step size given by the third:

    for number in range(2, 14, 4):
    	print(number)

>2
>6
>10

## Recap

In this section we've seen several tools that work together to allow our programs to deal elegantly with multiple pieces of data. Lists let us store many elements in a single variable, and loops let us process those elements one by one. In learning about loops, we've also been introduced to the block syntax and the importance of indentation in Python.

We've also seen several useful ways in which we can use the notation we've learned for working with lists with other types of data. Depending on the circumstances, we can treat strings, files, and ranges as if they were lists. This is a very helpful feature of Python, because once we've become familiar with the syntax for working with lists, we can use it in many different place. Learning about these tools has also helped us make sense of some interesting behaviour that we observed in earlier sections. 

Lists are the first example we've encountered of structures that can hold multiple pieces of data. We'll encounter another such structure – the dict – in a future sectino. In fact, Python has several more such data types – you'll find a full survey of them in the chapter on complex data structures in Advanced Python for Biologists (which you can find [on the books page][1]). 

[1]: /python-books

## Exercises
Both of these exercises require input files; [click here to download them][2]. Remember, you can always find solutions and explanations for all the exercises in the Python for Biologists books. 

[2]: /s/lists_and_loops_exercise_files.zip

### Processing DNA in a file
The file *input.txt* contains a number of DNA sequences, one per line. Each sequence starts with the same 14 base pair fragment – a sequencing adapter that should have been removed. Write a program that will (a) trim this adapter and write the cleaned sequences to a new file and (b) print the length of each sequence to the screen. 

### Multiple exons from genomic DNA

The file *genomic_dna.txt* contains a section of genomic DNA, and the file exons.txt contains a list of start/stop positions of exons. Each exon is on a separate line and the start and stop positions are separated by a comma. The start and stop positions follow Python conventions; they start from zero and are inclusive at the start and exclusive at the end. Write a program that will extract the exon segments, concatenate them, and write them to a new file.

This is a tricky exercise with several parts: your program will have to:
- read the exon file line by line
- split each exon line into two numbers
- turn those numbers into integers
- extract the matching part of the genomic DNA sequence
- concatenate all the exon sequences together

## SOLUTIONS

You can find solutions to all the exercises, along with explanations of how they work, by signing up for the [online course](/python-for-biologists-online-course).
