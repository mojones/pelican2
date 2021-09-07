Title: Python tutorial part five
Date: 2010-12-03 10:20
Modified: 2010-12-05 19:30
Category: tutorial
Tags: python, tutorial
slug: tutorial/conditions
Authors: Martin
Summary: Python tutorial part 5: conditions

## Programs need to make decisions

If we look back at the examples in previous sections, something that stands out is the lack of decision making. We've gone from doing simple calculations on individual bits of data to carrying out more complicated procedures on collections of data, but each individual piece of data (a sequence, a base, a species name, an exon) has been treated identically. 

Real life problems, however, often require our programs to act as decision makers: to examine a property of some bit of data and decide what to do with it. In this chapter, we'll see how to do that using conditional statements. Conditional statements are features of Python that allow us to build decision points in our code. They allow our programs to decide which out of a number of possible courses of action to take – instructions like "print the name of the sequence if it's longer than 300 bases" or "group two samples together if they were collected less than 10 metres apart". 

Before we can start using conditional statements, however, we need to understand conditions. 

### Conditions, True and False

A condition is simply a bit of code that can produce a true or false answer. The easiest way to understand how conditions work in Python is to try out a few examples. The following example prints out the result of testing (or evaluating) a bunch of different conditions – some mathematical examples, some using string methods, and one for testing if a value is included in a list:

    print(3 == 5)
    print(3 > 5)
    print(3 <=5)
    print(len("ATGC") > 5)
    print("GAATTC".count("T") > 1)
    print("ATGCTT".startswith("ATG"))
    print("ATGCTT".endswith("TTT"))
    print("ATGCTT".isupper())
    print("ATGCTT".islower())
    print("V" in ["V", "W", "L"])

If we look at the output, we can see that each of the conditions gives a true/false answer:

>False 
>False 
>True 
>False 
>True 
>True 
>False 
>True 
>False 
>True 

But what's actually being printed here? At first glance, it looks like we're printing the strings "True" and "False", but those strings don't appear anywhere in our code. What is actually being printed is the special built in values that Python uses to represent true and false – they are capitalized so that we know they're these special values.

Incidentally, we can show that these values are special by trying to print them. The following code runs without errors (note the absence of quotation marks):

    print(True)
    print(False)

whereas trying to print arbitrary unquoted words:

    print(Hello)

always causes a `NameError`.

There's a wide range of things that we can include in conditions, and it would be impossible to give an exhaustive list here. The basic building blocks are:

- equals (represented by `==`)
- greater and less than (represented by `>` and `<`)
- greater and less than or equal to (represented by `>=` and `<=`)
- not equal (represented by `!=`)
- is a value in a list (represented by `in`)
- are two objects the same1 (represented by `is`)

Many data types also provide methods that return `True` or `False` values, which are often a lot more convenient to use than the building blocks above. We've already seen a few in the code sample above: for example, strings have a `startswith()` method that returns `True` if the string on which the method is called starts with the string given as an argument. We'll mention these true/false methods when they come up. 

Notice that the test for equality is two equals signs, not one. Forgetting the second equals sign will cause an error.

Now that we know how to express tests as conditions, let's see what we can do with them. 

### `if` statements

The simplest kind of conditional statement is an `if` statement. Hopefully the syntax is fairly simple to understand:

    expression_level = 125
    if expression_level > 100:
    	print("gene is highly expressed")
	
We write the word `if`, followed by a condition, and end the first line with a colon. There follows a block of indented lines of code (the body of the `if` statement), which will only be executed if the condition is true. This colon-plus-block pattern should be familiar to you from the sections on loops and functions. 

Most of the time, we want to use an `if` statement to test a property of some variable whose value we don't know at the time when we are writing the program. The example above is obviously useless, as the value of the `expression_level` variable is not going to change! 

Here's a slightly more interesting example – we'll define a list of gene accession names and print out just the ones that start with "a":

    accs = ['ab56', 'bh84', 'hv76', 'ay93', 'ap97', 'bd72']
    for accession in accs:
    	if accession.startswith('a'):
		    print(accession)

Looking at the output allows us to check that this works as intended:

>ab56 
>ay93 
>ap97 

If you take a close look at the code above, you'll see something interesting – the lines of code inside the loop are indented (just as we've seen before), but the line of code inside the `if` statement is indented twice – once for the loop, and once for the `if`.  This is the first time we've seen multiple levels of indentation, but it's very common once we start working with larger programs. Whenever we have one loop or `if` statement nested inside another, we'll have this type of indentation. 

Python is quite happy to have as many levels of indentation as needed, but you'll need to keep careful track of which lines of code belong at which level. If you find yourself writing a piece of code that requires more than three levels of indentation, it's generally an indication that that piece of code should be turned into a function. 

### else statements

Closely related to the `if` statement is the `else` clause. The examples above use a yes/no type of decision-making: should we print the gene accession number or not? Often we need an either/or type of decision, where we have two possible actions to take. To do this, we can add an `else` clause after the end of the body of an if statement:

    expression_level = 125
    if expression_level > 100:
    	print("gene is highly expressed")
    else:
    	print("gene is lowly expressed")
	
The `else` clause doesn't have any condition of its own – rather, the else statement body is executed when the condition of the `if` statement is false. 

Here's an example which uses `if` and `else` to split up a list of accession names into two different files – accessions that start with "a" go into the first file, and all other accessions go into the second file:

    file1 = open("one.txt", "w")
    file2 = open("two.txt", "w")
    
    accs = ['ab56', 'bh84', 'hv76', 'ay93', 'ap97', 'bd72']
    
    for accession in accs:
    	if accession.startswith('a'):
		    file1.write(accession + "\n")
	    else:
		    file2.write(accession + "\n")

Notice how there are multiple indentation levels as before, but that the `if` and `else` statements are at the same level. 

### elif statements

What if we have more than two possible branches? For example, say we want three files of accession names: ones that start with "a", ones that start with "b", and all others. We could have a second `if` statement nested inside the `else` clause of the first `if` statement:

    file1 = open("one.txt", "w")
    file2 = open("two.txt", "w")
    file3 = open("three.txt", "w")
    
    accs = ['ab56', 'bh84', 'hv76', 'ay93', 'ap97', 'bd72']
    
    for accession in accs:
    	if accession.startswith('a'):
		    file1.write(accession + "\n")
	    else:
    		if accession.startswith('b'):
			    file2.write(accession + "\n")
		    else:
    			file3.write(accession + "\n")

This works, but is difficult to read – we can quickly see that we need an extra level of indentation for every additional choice we want to include. To get round this, Python has an `elif` statement, which merges together `else` and `if` and allows us to rewrite the above example in a much more elegant way:

    accs = ['ab56', 'bh84', 'hv76', 'ay93', 'ap97', 'bd72']
    for accession in accs:
    	if accession.startswith('a'):
		    file1.write(accession + "\n")
	    elif accession.startswith('b'):
    		file2.write(accession + "\n")
    	else:
		    file3.write(accession + "\n")

Notice how this version of the code only needs two levels of indention. In fact, using `elif` we can have any number of branches without adding any extra indentation:

    for accession in accs:
    	if accession.startswith('a'):
		    file1.write(accession + "\n")
	    elif accession.startswith('b'):
    		file2.write(accession + "\n")
    	elif accession.startswith('c'):
		    file3.write(accession + "\n")
	    elif accession.startswith('d'):
    		file4.write(accession + "\n")
    	elif accession.startswith('e'):
		    file5.write(accession + "\n")
	    else:
    		file6.write(accession + "\n")
    		
Note the order of the statements in the example above; we always start with an `if` and end with an `else`, and all the `elif` statements go in the middle. This kind of `if/elif/else` structure is very useful when we have several mutually-exclusive options. In the example above, only one branch can be true for each accession number – a string can't start with both "a" and "b". If we have a situation where the branches are not mutually exclusive – i.e. where more than one branch can be taken – then we simply need a series of `if` statements:

    for accession in accs:
    	if accession.startswith('a'):
		    file1.write(accession + "\n")
	    if accession.endswith('z'):
    		file2.write(accession + "\n")
    	if len(accession) == 4:
		    file3.write(accession + "\n")
	    if accession.count('j') > 5:
    		file4.write(accession + "\n")
		
In the example above, a single accession can satisfy more than one condition – a string can start with "a" and end with "z" – so it makes sense to use multiple `if` statements. 

### while loops

Here's one final thing we can do with conditions: use them to determine when to exit a loop. Previously we learned about loops that iterate over a collection of elements (like a list, a string or a file). Python has another type of loop called a `while` loop. Rather than running a set number of times, a `while` loop runs until some condition is met. For example, here's a bit of code that increments a `count` variable by one each time round the loop, stopping when the `count` variable reaches ten:

    count = 0
    while count<10:
    	print(count)
    	count = count + 1

Because normal loops in Python are so powerful, `while` loops are used much less frequently than in other languages, so we won't discuss them further. 

## Building up complex conditions

What if we wanted to express a condition that was made up of several parts? Imagine we want to go through our list of accessions and print out only the ones that start with "a" and end with "3".  We could use two nested `if` statements:

    accs = ['ab56', 'bh84', 'hv76', 'ay93', 'ap97', 'bd72']
    for accession in accs:
    	if accession.startswith('a'):
		    if accession.endswith('3'):
    			print(accession)
			
but this brings in an extra, unneeded level of indention. A better way is to join the two conditions with `and` to make a complex expression:

    accs = ['ab56', 'bh84', 'hv76', 'ay93', 'ap97', 'bd72']
    for accession in accs:
    	if accession.startswith('a') and accession.endswith('3'):
		    print(accession)

This version is nicer in two ways: it doesn't require the extra level of indentation, and the condition reads in a very natural way.  We can also use `or` to join up two conditions, to produce a complex condition that will be true if either of the two simple conditions are true:

    accs = ['ab56', 'bh84', 'hv76', 'ay93', 'ap97', 'bd72']
    for accession in accs:
    	if accession.startswith('a') or accession.startswith('b'):
		    print(accession)

We can even join up complex conditions to make more complex conditions – here's an example which prints accessions if they start with either "a" or "b" and end with "4":

    accs = ['ab56', 'bh84', 'hv76', 'ay93', 'ap97', 'bd72']
    for acc in accs:
    	if (acc.startswith('a') or acc.startswith('b')) and acc.endswith('4'):
		    print(acc)

Notice how we have to include parentheses in the above example to avoid ambiguity. If we have three simple conditions represented by X, Y and Z, then the complex condition 

    (X or Y) and Z 

is not the same as the complex condition 

    X and (Y or Z)
    
Finally, we can negate any type of condition by prefixing it with the word `not`. This example will print out accessions that start with "a" and don't end with 6:

    accs = ['ab56', 'bh84', 'hv76', 'ay93', 'ap97', 'bd72']
    for acc in accs:
    	if acc.startswith('a') and not acc.endswith('6'):
		    print(acc)

By using a combination of `and`, `or` and `not` (along with parentheses where necessary) we can build up arbitrarily complex conditions. 

Note: This kind of use for conditions – identifying elements in a list – can often be done better using either the `filter()` function, or a list comprehension. You'll find examples of each in the chapters on functional programming and comprehensions respectively in Advanced Python for Biologists.

These three words are collectively known as **boolean operators** and crop up in a lot of places. For example, imagine you want to search a protein sequence database for full length cytochrome oxidase subunit one proteins. You could simply search using the query 

>COX1

but you would encounter two big problems: any sequences that were labelled as COI rather than COX1 would be missing from the results list, and the results list would contain partial sequences. To get around these problems, you might construct a query like this:

>COX1 or COI and not partial

which uses the same tools and logic as we've just seen in Python.

## Writing true/false functions

Sometimes we want to write a function that can be used in a condition. This is very easy to do – we just make sure that our function always returns either `True` or `False`.  Remember that `True` and `False` are built in values in Python, so they can be passed around, stored in variables, and returned, just like numbers or strings. 

Here's a function that determines whether or not a DNA sequence is AT-rich (we'll say that a sequence is AT-rich if it has an AT content of more than 0.65):

    def is_at_rich(dna): 
    	length = len(dna) 
    	a_count = dna.upper().count('A') 
    	t_count = dna.upper().count('T') 
    	at_content = (a_count + t_count) / length 
    	if at_content > 0.65:
		    return True
	    else:
    		return False

We'll test this function on a few sequences to see if it works:

    print(is_at_rich("ATTATCTACTA"))
    print(is_at_rich("CGGCAGCGCT"))
    
The output shows that the function returns `True` or `False` just like the other conditions we've been looking at:

>True
>False

Therefore we can use our function in an `if` statement:

    if is_at_rich(my_dna):
    	# do something with the sequence
    	
Because the last four lines of our function are devoted to evaluating a condition and returning `True` or `False`, we can write a slightly more compact version. In this example we evaluate the condition, and then return the result right away❶:

    def is_at_rich(dna): 
    	length = len(dna) 
    	a_count = dna.upper().count('A') 
    	t_count = dna.upper().count('T') 
    	at_content = (a_count + t_count) / length 
    	return at_content > 0.65❶
	
This is a little more concise, and also easier to read once you're familiar with the idiom.

## Recap

In this short section, we've dealt with two things: conditions, and the statements that use them. 
We've seen how simple conditions can be joined together to make more complex ones, and how the concepts of truth and falsehood are built in to Python on a fundamental level. We've also seen how we can incorporate True and False in our own functions in a way that allows them to be used as part of conditions. 

We've been introduced to four different tools that use conditions – `if`, `else`, `elif`, and `while` – in approximate order of usefulness. You'll probably find, in the programs that you write and in your solutions to the exercises, that you use `if` and `else` very frequently, `elif` occasionally, and `while` almost never.

## Exercises

[Click here to download][1] a text file called *data.csv*, containing some made-up data for a number of genes.  Each line contains the following fields for a single gene in this order: species name, sequence, gene name, expression level. The fields are separated by commas (hence the name of the file – csv stands for Comma Separated Values). Think of it as a representation of a table in a spreadsheet – each line is a row, and each field in a line is a column. All the exercises below use the data in this file. 

[1]: /s/data.csv

This is a multi part exercise which involves extracting and printing data from the file. The nature of this type of problem means that it's quite easy to get a program that runs without errors, but doesn't quite produce the correct output, so be sure to check your solutions manually. Remember, you can always find solutions and explanations for all the exercises in the [Python for Biologists books][2]. 

[2]: /python-books

Reminder: if you're using Python 2 rather than Python 3, include this line at the top of your programs:
from __future__ import division

### Several species

Print out the gene names for all genes belonging to Drosophila melanogaster or Drosophila simulans. 

### Length range
Print out the gene names for all genes between 90 and 110 bases long. 

### AT content
Print out the gene names for all genes whose AT content is less than 0.5 and whose expression level is greater than 200.

### Complex condition
Print out the gene names for all genes whose name begins with "k" or "h" except those belonging to Drosophila melanogaster.

### High low medium
For each gene, print out a message giving the gene name and saying whether its AT content is high (greater than 0.65), low (less than 0.45) or medium (between 0.45 and 0.65).

## SOLUTIONS

You can find solutions to all the exercises, along with explanations of how they work, by signing up for the [online course](/python-for-biologists-online-course).
