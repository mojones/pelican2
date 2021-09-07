Title: Introduction to Python for biologists
Date: 2010-12-03 10:20
Modified: 2010-12-05 19:30
Category: books
Tags: python, tutorial
slug: introduction-to-python-for-biologists
Authors: Martin
Template: plain


# Introduction to Python for Biologists

This is my most popular course, which is intended for people with a biological background but with no previous programming experience. The course introduces basic programming concepts with plenty of time to practice writing code and working on your own data. Comments from people who have attended:

*"I loved this course. It started from very basic concepts and gradually came to more advanced tasks. I feel like I can apply knowledge gained during the course to my own data. "*

*"Martin succeeds a 100% at explaining the logic behind and demonstrating during "live" coding session how to break down problems into smaller bits and combine it into a working solution. "*

*"I wasn't sure I would enjoy this as I thought I might run into a lot of issues with misunderstanding what was being taught, but Martin delivers the material in a clear and understandable way. I'm really pleased with myself for making it through these two weeks without feeling like this is beyond my capabilities. "*

## Description
Python is a dynamic, readable language that is a popular platform for all types of bioinformatics work, from simple one-off scripts to large, complex software projects. This workshop is aimed at complete beginners and assumes no prior programming experience. It gives an overview of the language with an emphasis on practical problem-solving, using examples and exercises drawn from various aspects of bioinformatics work. The workshop is structured so that the parts of the language most useful for bioinformatics are introduced as early as possible, and that students can start writing plausibly-useful programs after the first few sessions. After completing the workshop, students should be in a position to (1) apply the skills they have learned to tackling problems in their own research and (2) continue their Python education in a self-directed way.

## Now available online
Can't make it to any of the classroom courses? You can now [take this course online](/python-for-biologists-online-course).



### Detailed syllabus

#### 1\. Introduction

In this session I introduce the students to Python and explain what we expect them to get out of it and how learning to program can benefit their research. I explain the format of the course and take care of any housekeeping details (like coffee breaks and catering arrangements). I outline the edit-run-fix cycle of software development and talk about how to avoid common text editing errors. In this session, we also check that the computing infrastructure for the rest of the course is in place (e.g. making sure that everybody has an appropriate version of Python installed). Core concepts introduced: source code, text editors, whitespace, syntax and syntax errors, Python versions

#### 2\. Manipulating text

In this session students learn to write very simple programs that produce output to the terminal, and in doing so become comfortable with editing and running Python code. This session also introduces many of the technical terms that we'll rely on in future sessions. I run through some examples of tools for working with text and show how they work in the context of biological sequence manipulation. We also cover different types of errors and error messages, and learn how to go about fixing them methodically. Core concepts introduced: terminals, standard output, variables and naming, strings and characters, special characters, output formatting, statements, functions, methods, arguments, comments.

#### 3\. Working with files

I introduce this session by talking about the importance of files in bioinformatics pipelines and workflows, and we then explore the Python interfaces for reading from and writing to files. This involves introducing the idea of types and objects, and a bit of discussion about how Python interacts with the operating system. The practical session is spent combining the techniques from session 2 with the file IO tools to create basic file- processing scripts. Core concepts introduced: objects and classes, paths and folders, relationships between variables and values, text and binary files, newlines.

#### 4\. Lists and loops

A discussion of the limitations of the techniques learned in session 3 quickly reveals that flow control is required to write more sophisticated file-processing programs, and I introduce the concept of loops. We look at the way in which Python loops work, and how they can be used in a variety of contexts. We explore the use of loops and lists together to tackle some more difficult problems. Core concepts introduced: lists and arrays, blocks and indentation, variable scoping, iteration and the iteration interface, ranges.

#### 5\. Conditions

I use the idea of decision-making as a way to introduce conditional tests, and outline the different building-blocks of conditions before showing how conditions can be combined in an expressive way. We look at the different ways that we can use conditions to control program flow, and how we can structure conditions to keep programs readable. Core concepts introduced: Truth and falsehood, Boolean logic, identity and equality, evaluation of statements, branching.

#### 6\. Writing functions

We discuss functions that we'd like to see in Python before considering how we can add to our computational toolbox by creating our own. We examine the nuts and bolts of writing functions before looking at best-practice ways of making them usable. We also look at a couple of advanced features of Python - named arguments and defaults. Core concepts introduced: argument passing, encapsulation, data flow through a program.

#### 7\. Regular expressions

I show how a range of common problems in bioinformatics can be described in terms of pattern matching, and give an overview of Pythons regex tools. We look at the building blocks of regular expressions themselves, and learn how they are a general solution to the problem of describing patterns in strings, before practising writing some specific examples of regular expressions. Core concepts introduced: domain-specific languages, modules and namespaces.

#### 8\. Dictionaries

We discuss a few examples of key-value data and see how the problem of storing them is a common one across bioinformatics and programming in general. We learn about the syntax for dictionary creation and manipulation before talking about the situations in which dictionaries are a better fit that the data structures we have learned about thus far. Core concepts introduced: paired data types, hashing, key uniqueness, argument unpacking and tuples.

#### 9\. Working with the filesystem

We discuss the role of Python in the context of a bioinformatics workflow, and how it is often used as a language to "glue" various other components together. We then look at the Python tools for carrying out file and directory manipulation, and for running external programs - two tasks that are often necessary in order to integrate our own programs with existing ones. Core concepts introduced: processes and subprocesses, the shell and shell utilities, program return values.

#### 10\. Final day

The schedule for the final day will be set based on the progress of the course and the interests of the students. We will have time set aside for attendees to finish exercises, work on their own data, or get one-on-one help with real-world problems arising from their research. We may also use some of the time to cover more advanced topics of interest to the attendees, including BioPython, data visualisation, packaging and distributing code, and using alternative interfaces such as iPython.




