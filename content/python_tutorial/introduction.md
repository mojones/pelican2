Title: Python tutorial part one
Date: 2010-12-03 10:20
Modified: 2010-12-05 19:30
Category: tutorial
Tags: python, tutorial
Slug: tutorial/introduction
Authors: Martin
Summary: Python tutorial part 1: introduction to the language


## Why Python?

The importance of programming languages is often overstated. What I mean by that is that people who are new to programming tend to worry far too much about what language to learn. The choice of programming language does matter, of course, but it matters far less than most people think it does. To put it another way, choosing the "wrong" programming language is very unlikely to mean the difference between failure and success when learning. Other factors (motivation, having time to devote to learning, helpful colleagues) are far more important, yet receive less attention.

The reason that people place so much weight on the "what language should I learn?" question is that it's a big, obvious question, and it's not difficult to find people who will give you strong opinions on the subject. It's also the first big question that beginners have to answer once they've decided to learn programming, so it assumes a great deal of importance in their minds. 

There are three main reasons why choice of programming language is not as important as most people think it is. Firstly, nearly everybody who spends any significant amount of time programming as part of their job will eventually end up using multiple languages. Partly this is just down to the simple constraints of various languages – if you want to write a web application you'll probably do it in Javascript, if you want to write a graphical user interface you'll probably use something like Java, and if you want to write low-level algorithms you'll probably use C.  

Secondly, learning a first programming language gets you 90% of the way towards learning a second, third, and fourth one. Learning to think like a programmer in the way that you break down complex tasks into simple ones is a skill that cuts across all languages – so if you spend a few months learning Python and then discover that you really need to write in C, your time won't have been wasted as you'll be able to pick it up much quicker. 

Thirdly, the kinds of problems that we want to solve in biology are generally amenable to being solved in any language, even though different programming languages are good at different things. In other words, as a beginner, your choice of language is vanishingly unlikely to prevent you from solving the problems that you need to solve. 

Having said all of the above, when learning to program we do need to pick a language to work in, so we might as well pick one that's going to make the job easier. Python is such a language for a number of reasons:

- It has a consistent syntax, so you can generally learn one way of doing things and then apply it in multiple places
- It has a sensible set of built in libraries for doing lots of common tasks
- It is designed in such a way that there's an obvious way of doing most things
- It's one of the most widely used languages in the world, and there's a lot of advice, documentation and tutorials available on the web
- It's designed in a way that lets you start to write useful programs as soon as possible 
- Its use of indentation, while annoying to people who aren't used to it, is great for beginners as it enforces a certain amount of readability

Python also has a couple of points to recommend it to biologists and scientists specifically:

- It's widely used in the scientific community
- It has a couple of very well designed libraries for doing complex scientific computing (although we won't encounter them in this book)
- It lend itself well to being integrated with other, existing tools
- It has features which make it easy to manipulate strings of characters (for example, strings of DNA bases and protein amino acid residues, which we as biologists are particularly fond of)

## Python vs. Perl

For biologists, the question "what language should I learn" often really comes down to the question "should I learn Perl or Python?", so let's answer it head on. Perl and Python are both perfectly good languages for solving a wide variety of biological problems. However, after extensive experience teaching both Perl and Python to biologists, I've come the conclusion that Python is an easier language to learn by virtue of being more consistent and more readable. 

An important thing to understand about Perl and Python is that they are incredibly similar (despite the fact that they look very different), so the point above about learning a second language applies doubly. Many Python and Perl features have a one-to-one correspondence, and so if you find that you have to work in Perl after learning Python you'll find it quite familiar.

## Formatting

When discussing programming, we use lots of special types of text – we'll need to look at examples of Python code and output, the contents of files, and technical terms. Take a minute to note the typographic conventions we'll be using.

In the main text of this tutorial, **bold type** is used to emphasize important points and *italics* for technical terms and filenames. Where code is mixed in with normal text it's written in a monospaced font with a red tint `like this`. 

Example Python code looks like this:

    Some example code goes here

Sometimes it's useful to refer to a specific line of code inside an example. For this, we'll use numbered circles like this❶:

    a line of example code
    another line of example code
    this is the important line❶
    here is another line

Example output (i.e. what we see on the screen when we run the code) looks like this:

>Some output goes here

Often we want to look at the code and the output it produces together. In these situations, you'll see a block of code immediately followed by its output. 

Other blocks of text (usually file contents or typed command lines) look the same as code output - hopefully it'll be clear from context what they are. 

Often when looking at larger examples, or when looking at large amounts of output, we don't need to see the whole thing. In these cases, I'll use ellipses (...) to indicate that some text has been missed out.

I have used UK English spelling throughout, which I hope will not prove distracting to US readers. 

In programming, we use different types of brackets for different purposes, so it's important to have different names for them.
Throughout this tutorial, I will use the word parentheses to refer to `()`, square brackets to refer to `[]`, and curly brackets to refer to `{}`.

## Getting in touch

Learning to program is a difficult task, and my one goal in writing these pages is to make it as easy and accessible as possible to get started. So, if you find anything that is hard to understand, or you think may contain an error, please get in touch – just drop me an email at

martin@pythonforbiologists.com
 
and I promise to get back to you. 

## Setting up your environment

All that you need in order to follow the examples is a standard Python installation and a text editor. All the code in this tutorial

 will run on either Linux, Mac or Windows machines. The slight differences between operating systems are explained in the text.

## Python 2 vs. Python 3

As will quickly become clear if you spend any amount of time on the official Python website, there are two versions of Python currently available. The Python world is, at the time of writing, in the middle of a transition from version 2 to version 3. A discussion of the pros and cons of each version is well beyond the scope of this book1, but here's what you need to know: install Python 3 if possible, but if you end up with Python 2, don't worry – all the code examples in the book will work with both versions. 

If you're going to use Python 2, there is just one thing that you have to do in order to make some of the code examples work: include this line at the start of all your programs:

    from __future__ import division

We won't go into the explanation behind this line, except to say that it's necessary in order to correct a small quirk with the way that Python 2 handles division of numbers. 

Depending on what version you use, you might see slight differences between the output on these pages and the output you get when you run the code on your computer. I've tried to note these differences in the text where possible. 

## Installing Python

The process of installing Python depends on the type of computer you're running on. 
If you're using Windows, start by going to this page:

https://www.python.org/downloads/windows/

then follow the link at the top of the page to the latest release. From here you can download and run the Windows installer. 

If you're using Mac OS X, head to this page:

https://www.python.org/downloads/mac-osx/

then follow the link at the top of the page to the latest release. From here you can download and run the OS X installer. 

If you're running a mainstream Linux distribution like Ubuntu, Python is probably already installed. If your Linux installation doesn't already have Python installed, try installing it with your package manager – the command will probably be either 

sudo apt-get install python idle

or 

sudo yum install python idle

## Editing and running Python programs	
In order to learn Python, we need two things: the ability to edit Python programs, and the ability to run them and view the output. There are two different ways to do this – using a text editor from the command line, or using Python's graphical editor program. 

### Using the command line

If you're already comfortable using the command line, then this will probably be the easiest way to get started. Firstly, you'll need to be able to open a new terminal. If you're using Windows, you can do this by running the command prompt program. If you're using OS X, run the terminal program from inside the Utilities folder. If you're using Linux, you probably already know how to open a new terminal – the program is probably called something like Terminal Emulator. 

Since a Python program is just a text file, you can create and edit it with any text editor of your choice. Note that by a text editor I don't mean a word processor – do not try to edit Python programs with Microsoft Word, LibreOffice Writer, or similar tools, as they tend to insert special formatting marks that Python cannot read. 

When choosing a text editor, there is one feature that is essential2 to have, and one which is nice to have. The essential feature is something that's usually called tab emulation. The effect of this feature at first seems quite odd; when enabled, it replaces any tab characters that you type with an equivalent number of space characters (usually set to four). The reason why this is useful is discussed at length in chapter 4, but here's a brief explanation: Python is very fussy about your use of tabs and spaces, and unless you are very disciplined when typing, it's easy to end up with a mixture of tabs and spaces in your programs. This causes very infuriating problems, because they look the same to you, but not to Python! Tab emulation fixes the problem by making it effectively impossible for you to type a tab character. 

The feature that is nice to have is syntax highlighting. This will apply different colours to different parts of your Python code, and can help you spot errors more easily. 

Recommended text editors are Notepad++ for Windows3, TextWrangler for Mac OSX4, and gedit for Linux5, all of which are freely available. 

To run a Python program from the command line, just type the name of the Python executable (python.exe on Windows, python on OS X and Linux) followed by the name of the Python file you've created. 

If any of the above doesn't work or seems complicated, just use the graphical editor as described in the next section. 

### Using a graphical editor

Python comes with a program called IDLE which provides a friendly graphical interface for writing and running Python code. IDLE is an example of an Integrated Development Environment (sometimes shortened to IDE). 
IDLE works identically on Windows, OS X and Linux. To create a new Python file, just start the IDLE program and select New File from the File menu. This will open a new window in which you can type and edit Python code. When you want to run your Python program, use the File menu to save it (remember that the filename should end with .py) then select Run Module from the Run menu. The output will appear in the Python Shell window. 
You can also use IDLE as a text editor – for example, to view input and output files. Just select Open from the File menu and pick the file that you want to view. To open a non-Python file, you'll have to select All files from the Files of type drop-down menu. 

## Reading the documentation

Part of the teaching philosophy that I've used in writing these pages is that it's better to introduce a few useful features and functions rather than overwhelm you with a comprehensive list. The best place to go when you do want a complete list of the options available in Python is the official documentation

http://www.python.org/doc/

which, compared to many languages, is very readable.    

To get started with actually writing Python, carry on to the page on [manipulating text](https://pythonforbiologists.com/printing-and-manipulating-text). Or if you'd like a bit more help with getting started, you might want to [sign up for the online course](https://pythonforbiologists.com/python-for-biologists-online-course). 


