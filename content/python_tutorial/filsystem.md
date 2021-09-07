Title: Python tutorial part nine
Date: 2010-12-03 10:20
Modified: 2010-12-05 19:30
Category: tutorial
Tags: python, tutorial
slug: tutorial/filesystem
Authors: Martin
Summary: Python tutorial part 9: working with the filesystem

## File contents and manipulation 

Reading from and writing to files was one of the first things we looked at.  For some programs, however, we're not just concerned with the contents of files, but with files and folders themselves. This is especially likely to be the case for programs that have to operate as part of a work flow involving other tools and software. For example, we may need to copy, move, rename and delete files, or we may need to process all files in a certain folder. 

Although it seems like a simple task (after all, the file manager tools that come with your operating system can carry most of them out), file manipulation in a language like Python is actually quite tricky. That's because the code that we write has to function identically on different operating systems – including Windows, Linux and Mac machines – which may handle files quite differently. 

Thankfully, Python includes a couple of modules that take care of these differences for us and provide us with a set of useful functions for manipulating files. The modules' names are `os` (short for Operating System) and `shutil` (short for SHell UTILities). In the next section we'll see how they can be used to carry out various common (but important) tasks.

### A note on the code examples

Since the code examples in this page unavoidably involve interaction with the operating system, some of the details will be operating system specific. In other words, the examples in this page might not run on your computer without some tweaking. In addition to the paths being different, the success of the code examples for many functions relies on the files and folders actually being present on the computer on which the examples are run. The code examples in this page will use Linux style paths, and will refer to folders and files on my computer, so if you want to try running them, you'll probably need to change the paths to refer to files on your own computer. 

### Basic file manipulation

To rename an existing file, we import the `os` module, then use the `os.rename()` function. The `os.rename()` function takes two arguments, both strings. The first is the current name of the file, the second is the new name:

    import os
    os.rename("old.txt", "new.txt")

The above code assumes that the file `old.txt` is in the folder where we are running our Python program (also known as the working directory). If it's elsewhere in the filesystem, then we have to give the complete path:

    os.rename("/home/martin/biology/old.txt", "/home/martin/biology/new.txt")

If we specify a different folder, but the same filename in the second argument, then the function will move the file from one folder to another:

    os.rename("/home/martin/biology/old.txt", "/home/martin/python/old.txt")
    
Of course, we can move and rename a file in one step if you like:

    os.rename("/home/martin/biology/old.txt", "/home/martin/python/new.txt")
    
`os.rename()` works on folders as well as files:

    os.rename("/home/martin/old_folder", "/home/martin/new_folder")
    
If we try to move a file to a folder that doesn't exist we'll get an error. We need to create the new folder first with the `os.mkdir()` function:

    os.mkdir("/home/martin/python")

If we need to create a bunch of directories all in one go, we can use the `os.mkdirs()` function (note the s on the end of the name):

    os.mkdirs("/a/long/path/with/lots/of/folders")
    
To copy a file or folder we use the `shutil` module. We can copy a single file with `shutil.copy()`:

    shutil.copy("/home/martin/original.txt", "/home/martin/copy.txt")

or a folder with `shutil.copytree()`:

    shutil.copytree("/home/martin/original_folder", "/home/martin/copy_folder")

To test whether a file or folder exists, use `os.path.exists()`. This function returns `True` or `False`, so we can use it as a condition:

    if os.path.exists("/home/martin/email.txt"):
    	print("You have mail!")
    	
### Deleting files and folders

There are different functions for deleting files, empty folders, and non-empty folders. To delete a single file, `use os.remove()`:

    os.remove("/home/martin/unwanted_file.txt")
    
To delete an empty folder, use `os.rmdir()`:

    os.rmdir("/home/martin/empty")

To delete a folder and all the files in it, `use shutil.rmtree()`:

    shutil.rmtree("home/martin/full")
    
### Listing folder contents
The `os.listdir()` function returns a list of files and folders. It takes a single argument which is a string containing the path of the folder whose contents you want to search. To get a list of the contents of the current working directory, use the string `"."` for the path: 

    for file_name in os.listdir("."):
    	print("one file name is " + file_name)

To list the contents of a different folder, we just give the path as an argument:

    for file_name in os.listdir("/home/martin"):
	    print("one file name is " + file_name)

## Running external programs

Another feature of Python that involves interaction with the operating system is the ability to run external programs. Just like file and folder manipulation, the ability to run other programs is very useful when using Python as part of a work flow. It allows us to use existing tools that would be very time consuming to recreate in Python, or that would run very slowly. 

Running external programs from within your Python code can be a tricky business, and this feature wouldn't normally be covered in an introductory programming course. However, it's so useful for biology (and science in general) that we're going to cover it here, albeit in a simplified form. 

As with the above section on file operations, the exact details of how external programs are run will vary with your operating system and the way your computer is set up. On UNIX-based systems, the program that you want to run might already be in your path, in which case you can simply use the name of the executable as the string to be executed. On Windows, you'll probably have to supply the full path to the program you want to run.

### Running a program

The functions for running external program reside in the `subprocess()` module. The reasoning behind the name is slightly convoluted: when talking about operating systems, a running program is called a **process**, and a process that is started by another process is called a **subprocess**.

To run an external program, use the `subprocess.call()` function. This function takes a single string argument containing the path to the executable you want to run:

    import subprocess
    subprocess.call("/bin/date")

Any output that is produced by the external program is printed straight to the screen – in this case, the output from the Linux `date` program:

>Fri Jul 26 15:15:26 BST 2013 

If we want to supply command line options to the external program then we just include them in the string, and set the optional shell argument to `True`. Here we call the Linux `date` program with the options which cause it to just print the month:

    subprocess.call("/bin/date +%B", shell=True)

>July

### Capturing program output

Often, we want to run some external program and then store the output in a variable so that we can do something useful with it. For this, we use `subprocess.check_output()`, which takes exactly the same arguments as `subprocess.call()`:

    current_month = subprocess.check_output("/bin/date +%B", shell=True)

Just like when reading file contents, the output from an external program can run over multiples lines, so the string that's returned by `subprocess.check_output()` might contain multiple lines separated by newline characters. 

## User input makes our programs more flexible

The exercises and examples that we've seen so far have used two different ways of getting data into a program. For small bits of data, like short DNA sequences, restriction enzyme motifs, and gene accession names, we've simply stored the data directly in a variable like this:

    dna = "ATCGATCGTGACTAGCTACG"
    
When data is mixed in with the code in this manner, it is said to be **hard-coded**. 

For larger pieces of data, like longer DNA sequences and tabular data, we've typically read the information from an external text file. For many purposes, this is a better solution than hard-coding the data, as it allows the separation of data and code, making our programs easier to read. However, in all the examples we've seen so far, the names of the files from which the data are read are still hard-coded. 

Both of these approaches to getting data into our program have the same shortcomings – if we want to change the input data, we have to open the code and edit it. In the case of hard-coded variables, we have to edit the statement where the variables are created. In the case of files, we have two choices – we can either edit the contents of the file, or edit the hard-coded filename. 

Real life useful programs don't generally work that way. Instead, they allow us to specify input files and options at the time when we run the program, rather than when we're writing it. This allows programs to be much more flexible and easier to use, especially for a person who didn't write the code in the first place.

In the next couple of sections we're going to see tools for getting user input, but more importantly we're going to talk about the transition from writing a program that's only useful to you, to writing one that can be used by other people. This involves starting to think about the experience of using a program from the perspective of a user. 

There are many reasons why you might need your programs to be usable by somebody who's not familiar with the code. If you write a program that solves a problem for you, chances are that it could solve a problem for your colleagues and collaborators as well. If you write a program that forms a significant part of a piece of work which you later want to publish, you may have to make sure that whoever is peer reviewing your paper can get your program working as well. Of course, making your program easier to use for other people means that it will also be easier to use for you, a few months after you have written it when you have completely forgotten how the code works!

### Interactive user input

To get interactive input from the user in our programs, we can use the `raw_input()` function. `raw_input()` takes a single string argument, which is the prompt to be displayed to the user, and returns the value typed in as a string:

    accession = raw_input("Enter the accession name")
    # do something with the accession variable
    
Note: in Python 3, this function has been renamed to `input()`.

The `raw_input()` function behaves a little differently to other functions and methods we've seen, because it has to wait for something to happen before it can return a value – the user has to type in a string and press enter. The user input will be returned as a string (so if we need to use is as something else – e.g. a number – we'll have to do the conversion manually) and will end with a newline (so we might want to use `rstrip()` to remove it).

Capturing user input in this way requires us to think quite carefully about how our program behaves. Programs that we write to carry out analysis of large datasets will often take a considerable amount of time to run, so it's important that we minimize the chances of the user having to rerun them. When using the `raw_input()` function, there are two situations in particular that we want to avoid. 

One is the situation where we have a long-running program that requires some user input, but doesn't make this fact clear to the user. What can happen in this scenario is that the user starts the program running and then switches their attention to something else, assuming that the program will continue to make progress in the background. If the user doesn't notice (or is not at their computer) when the program reaches the point where it requires input and halts, the program may be stuck waiting for input for a long time. 

The other scenario to avoid is where a program runs for some time before asking the user for input, then fails to work due to an incorrect input or typo, requiring the user to restart the program from scratch. 

A good way to avoid both of these problems is to design our programs so that they collect all necessary user input at the start, before any long-running tasks are carried out. We can also reduce the chances of incorrect input on the part of the user by offering clear instructions and documentation. 

An important part of user input is input validation – checking that the input supplied by the user makes sense. For example, you might require that a particular input is a number between some minimum and maximum values, or that it's a DNA sequence without ambiguous bases, or that it's the name of a file that must exist. A good strategy for input validation is to check the input as soon as it's received, and give the user a second chance to enter their input if it's found to be invalid. Here's an example which uses a while loop to give the user multiple attemps to enter a number between 1 and 10:

    answer = "" 
    while answer < 1 or answer > 10: 
        answer = int(raw_input("enter a number between 1 and 10\n")) 
    print("final answer is " + str(answer))


A better way to do this is to use Python exception system: see the chapter on exceptions in Advanced Python for Biologists for details. 

One big drawback of getting user input interactively is that it makes it harder to run a program unsupervised as part of a work flow. For most biological analyses, specifying program options when it's run using command line arguments is a better approach.

### Command line arguments

If you're used to using existing programs that have a command line user interface (as opposed to a graphical one) then you're probably familiar with command line arguments. These are the strings that you type on the command line after the name of a program you want to run:

>myprogram one two three

In the above command line, `one`, `two` and `three` are the command line options. To use command line arguments in our Python scripts, we import the `sys` module. We can then access the command line arguments by using the special list `sys.argv`. Running the following code:

    import sys 
    print(sys.argv) 

with the command line:

>python myprogram.py one two three

shows how the elements of `sys.argv` are made up of the arguments given on the command line:

>['myprogram.py', 'one', 'two', 'three']

Note that the first element of `sys.argv` is always the name of the program itself, so the first command line argument is at index one, the second at index two, etc. 

Just like with `raw_input()`, options and filenames given on the command line are stored as strings so if, for example, we want to use a command line argument as a number, we'll have to convert it with `int()`. 

Command line arguments are a good way of getting input for your Python programs for a number of reasons. All the data your program needs will be present at the start of your program, so you can do any necessary input validation (like checking that files are present) before starting any processing. Also, your program will be able to be run as part of a shell script, and the options will appear in the user's shell history. 

## Recap
We started this page by examining two features of Python that allow your programs to interact with the operating system – file manipulation and external processes. We learned which functions to use for common file system operations, and which modules they belong to. We also saw two ways to call external programs from within your Python program. 

When using these techniques to solve real life problems, or when working on the exercises, remember that you may encounter errors that are nothing to do with your program. For instance, when trying to rename a file you may get an error if a specified file doesn't exist or you don't have the necessary permissions to rename it. Similarly, if you get unexpected output when running an external program the problem may lie with the external program or with the way that you're calling it, rather than with your Python program. This is in contrast to the rest of the exercises in this book, which are mostly self contained. If you run into difficulties when using the tools in this chapter, check the external factors as well as checking your program code. 

In the last portion, we saw two different ways to get user input when your program runs. Using command line arguments is generally better for the type of programming that forms part of scientific research. In real world scientific programming, we often need to make a command line interface that's usable by non-programmers. For a detailed discussion of building user interfaces, with many more examples, see the chapter in Effective Python development for Biologists (which you can find on [the books page][1]).

[1]: /python-books

## Exercises

Both of these exercises use the same set of files - [click here to download them][2], then extract the zip file to a convenient folder. Remember, you can always find solutions and explanations for all exercises in the [Python for Biologists books][3].

[3]: /python-books
[2]: /s/sequence_files.zip

### Binning DNA sequences

Write a program which creates nine new folders – one for sequences between 100 and 199 bases long, one for sequences between 200 and 299 bases long, etc. Write out each DNA sequence in the input files to a separate file in the appropriate folder. 

Your program will have to:

- iterate over the files in the folder
- iterate over the lines in each file
- figure out which bin each DNA sequence should go in based on its length
- write out each DNA sequence to a new file in the right folder

### Kmer counting

Write a program that will calculate the number of all kmers of a given length across all DNA sequences in the input files and display just the ones that occur more than a given number of times. You program should take two command line arguments – the kmer length, and the cutoff number. 

## SOLUTIONS

You can find solutions to all the exercises, along with explanations of how they work, by signing up for the [online course](/python-for-biologists-online-course).
