Title: Python tutorial part three
Date: 2010-12-03 10:20
Modified: 2010-12-05 19:30
Category: tutorial
Tags: python, tutorial
slug: tutorial/files
Authors: Martin
Summary: Python tutorial part 3: working with files


## Why are we so interested in working with files?

As we start this section, we find ourselves once again doing things in a slightly different order to most programming books. The majority of introductory programming books won't consider working with external files until much further along, so why are we introducing it now?

The answer, as was the case in the last section, lies in the particular jobs that we want to use Python for. The data that we as biologists work with is stored in files, so if we're going to write useful programs we need a way to get the data out of files and into our programs (and vice versa). Writing a DNA sequence directly into a program each time we want to use it is not a very good approach to take, and you'd be right. Also, it seems inelegant to have the data we want to work on mixed up with the code that manipulates it. In this section we'll see a better way to do it.

We're lucky in biology in that many of the types of data that we work with are stored in text files which are relatively simple to process using Python. Chief among these, of course, are DNA and protein sequence data, which can be stored in a variety of formats. But there are many other types of data – sequencing reads, quality scores, SNPs, phylogenetic trees, read maps, geographical sample data, genetic distance matrices – which we can access from within our Python programs. 

Another reason for our interest in file input/output is the need for our Python programs to work as part of a pipeline or work flow involving other, existing tools. When it comes to using Python in the real world, we often want Python to either accept data from, or provide data to, another program. Often the easiest way to do this is to have Python read, or write, files in a format that the other program already understands. 

## Reading text from a file

Firstly, a quick note about what we mean by text. In programming, when we talk about text files, we are not necessarily talking about something that is human readable. Rather, we are talking about a file that contains characters and lines – something that you could open and view in a text editor, regardless of whether you could actually make sense of the file or not. Examples of text files which you might have encountered include:

- FASTA files of DNA or protein sequences
- files containing output from command-line programs (e.g. BLAST)
- FASTQ files containing DNA sequencing reads
- HTML files
- and of course, Python code itself

In contrast, most files that you encounter day-to-day will be binary files – ones which are not made up of characters and lines, but of bytes. Examples include:

- image files (JPEGs and PNGs)
- audio files
- video files
- compressed files (e.g. ZIP files) 

If you're not sure whether a particular file is text or binary, there's a very simple way to tell – just open it up in a text editor. If the file displays without any problem, then it's text (regardless of whether you can make sense of it or not). If you get an error or a warning from your text editor, or the file displays as a collection of indecipherable characters, then it's binary. 

The examples on this page are a little different from those in the previous one, because they rely on the existence of the files that we are going to manipulate. If you want to try running the examples in this chapter, you'll need to make sure that there is a file in your working directory called `dna.txt` which has a single line containing a DNA sequence. 

## Using open to read a file

In Python, as in the physical world, we have to open a file before we can read what's inside it. The Python function that carries out the job of opening a file is very sensibly called `open()`. It takes one argument – a string which contains the name of the file – and returns a file object:

    my_file = open("dna.txt")

A file object is a new type which we haven't encountered before, and it's a little more complicated than the string and number types that we saw in the previous section. With strings and numbers it was easy to understand what they represented – a single bit of text, or a single number. A file object, in contrast, represents something a bit less tangible – it represents a file on your computer's hard drive. 

The way that we use file objects is a bit different to strings and numbers as well. If you glance back at the examples from the previous section you'll see that most of the time when we want to use a variable containing a string or number we just use the variable name:

    my_string = 'abcdefg'
    print(my_string)
    my_number = 42
    print(my_number + 1)
    
In contrast, when we're working with file objects most of our interaction will be through methods. This style of programming will seem unusual at first, but as we'll see in this chapter, the file type has a well thought-out set of methods which let us do lots of useful things.  

The first thing we need to be able to do is to read the contents of the file. The file type has a `read()` method which does this. It doesn't take any arguments, and the return value is a string, which we can store in a variable. Once we've read the file contents into a variable, we can treat them just like any other string – for example, we can print them:

    my_file = open("dna.txt")
    file_contents = my_file.read()
    print(file_contents)

### Files, contents and filenames

When learning to work with files it's very easy to get confused between a file object, a filename, and the contents of a file.  Take a look at the following bit of code:

    my_file_name = "dna.txt"❶
    my_file = open(my_file_name)❷
    my_file_contents = my_file.read()❸
    
What's going on here? First, we store the string `dna.txt` in the variable `my_file_name`❶. Next, we use the variable `my_file_name` as the argument to the `open()` function, and store the resulting file object in the variable `my_file`❷. Finally, we call the `read()` method on the variable `my_file`, and store the resulting string in the variable `my_file_contents`❸. 

The important thing to understand about this code is that there are three separate variables which have different types and which are storing three very different things:

- `my_file_name` is a string, and it stores the name of a file on disk.
- `my_file` is a file object, and it represents the file itself.
- `my_file_contents` is a string, and it stores the text that is in the file. 

Remember that variable names are arbitrary – the computer doesn't care what you call your variables. So this piece of code is exactly the same as the previous example:

    apple = "dna.txt"
    banana = open(apple)
    grape = banana.read()

except it is harder to understand! In contrast, the filename (`dna.txt`) is not arbitrary – it must correspond to the name of a file on the hard drive of your computer. 

A common error is to try to use the `read()` method on the wrong thing. Recall that `read()` is a method that only works on file objects. If we try to use the `read()` method on the filename:

    my_file_name = "dna.txt"
    my_contents = my_file_name.read()

we'll get an `AttributeError` – Python will complain that strings don't have a `read()` method:

>AttributeError: 'str' object has no attribute 'read'

Another common error is to use the file object when we meant to use the file contents. If we try to print the file object:

    my_file_name = "dna.txt"
    my_file = open(my_file_name)
    print(my_file)

we won't get an error, but we'll get an odd looking line of output:

><open file 'dna.txt', mode 'r' at 0x7fc5ff7784b0>

We won't discuss the meaning of this line now: just remember that if you try to print the contents of a file but instead you get some output that looks like the above, you have almost definitely printed the file object rather than the file contents. 

### Dealing with newlines

Let's take a look at the output we get when we try to print some information from a file. Imagine we have a file in the current folder which contains a single line with a short DNA sequence. 

We're going to write a simple program to read the DNA sequence from the file and print it out along with its length. Putting together the file functions and methods from this section, and the material we saw in the previous section, we get the following code:

    # open the file
    my_file = open("dna.txt")
    
    # read the contents
    my_dna = my_file.read()
    
    # calculate the length
    dna_length = len(my_dna)
    
    # print the output
    print("sequence is " + my_dna +  " and length is " + str(dna_length))

When we look at the output, we can see that there are two things wrong.

>sequence is ACTGTACGTGCACTGATC 
> and length is 19 
 
Firstly, the output has been split over two lines, even though we didn't ask for it. And secondly, the length is wrong – there are only 18 characters in the DNA string.

Both of these problems have the same explanation: Python has included the newline character at the end of the dna.txt file as part of the contents. In other words, the variable `my_dna` has a newline character at the end of it. If we could view the `my_dna` variable directly, we would see that it looks like this:

>'ACTGTACGTGCACTGATC\n'

This explains why the output from our program is split over two lines – the newline character is part of the string we are trying to print. It also explains why the length is wrong – Python is including the newline character when it counts the number of characters in the string. 

The solution is also simple. Because this is such a common problem, strings have a method for removing newlines from the end of them. The method is called `rstrip()`, and it takes one string argument which is the character that you want to remove. In this case, we want to remove the newline character (`\n`).  Here's a modified version of the code – note that the argument to `rstrip()` is itself a string so needs to be enclosed in quotes:❶

    my_file = open("dna.txt")
    my_file_contents = my_file.read()
    
    # remove the newline from the end of the file contents
    my_dna = my_file_contents.rstrip("\n")❶
    
    dna_length = len(my_dna)
    print("sequence is " + my_dna +  " and length is " + str(dna_length))

and now the output looks just as we expected:

>sequence is ACTGTACGTGCACTGATC and length is 18

In the code above, we first read the file contents and then removed the newline, in two separate steps:

    my_file_contents = my_file.read()
    my_dna = my_file_contents.rstrip("\n")

but it's more common to read the contents and remove the newline all in one go, like this:

    my_dna = my_file.read().rstrip("\n")
    
This is a bit tricky to read at first as we are using two different methods (`read()` and `rstrip()`) in the same statement. The key is to read it from left to right – we take the `my_file` variable and use the `read()` method on it, then we take the output of that method (which we know is a string) and use the `rstrip()` method on it.  The result of the `rstrip()` method is then stored in the `my_dna` variable. 

If you find it difficult to write the whole thing as one statement like this, just break it up and do the two things separately – your programs will run just as well. 

## Missing files

What happens if we try to read a file that doesn't exist?

    my_file = open("nonexistent.txt")

We get a new type of error that we've not seen before:

>IOError: [Errno 2] No such file or directory: 'nonexistent.txt'

If you encounter this error, you've probably got the filename wrong, or are working in the wrong folder. Ideally, we'd like to be able to check if a file exists before we try to open it – we'll see how to do that in a future lesson.

## Writing text to files

All the example programs that we've seen so far have produced output straight to the screen. That's great for exploring new features and when working on programs, because it allows you to see the effect of changes to the code right away. It has a few drawbacks, however, when writing code that we might want to use in real life.

Printing output to the screen only works well when there isn't very much of it. It's great for short programs and status messages, but quickly becomes cumbersome for large amounts of output. Some terminals struggle with large amounts of text, or worse, have a limited scrollback capability which can cause the first bit of your output to disappear. It's not easy to search in output that's being displayed at the terminal, and long lines tend to get wrapped. Also, for many programs we want to send different bits of output to different files, rather than having it all dumped in the same place. 

Most importantly, terminal output vanishes when you close your terminal program. For small programs like the examples on these pages, that's not a problem – if you want to see the output again you can just rerun the program. If you have a program that requires a few hours to run, that's not such a great option. 

### Opening files for writing

In the previous section, we saw how to open a file and read its contents. We can also open a file and write some data to it, but we have to use the `open()` function in a slightly different way. To open a file for writing, we use a two-argument version of the `open()` function, where the second argument is a short string describing what we want to do to the file. This second argument can be `"r"`  for reading, `"w"` for writing, or `"a"` for appending. If we leave out the second argument (like we did for all the examples above), Python uses the default, which is `"r"` for reading. 

The difference between `"w"` and `"a"` is subtle, but important. If we open a file that already exists using the mode `"w"`, then we will overwrite the current contents with whatever data we write to it. If we open an existing file with the mode `"a"`, it will add new data onto the end of the file, but will not remove any existing content.  If there doesn't already exist a file with the specified name, then `"w"` and `"a"` behave identically – they will both create a new file to hold the output. 

Quite a lot of Python functions and methods have these optional arguments. For the purposes of these pages, we will only mention them when they are directly relevant to what we're doing. If you want to see all the optional arguments for a particular method or function, the best place to look is the official Python documentation – see the previous page for details. 

Once we've opened a file for writing, we can use the file `write()` method to write some text to it. `write()` works a lot like `print()` – it takes a single string argument -  but instead of printing the string to the screen it writes it to the file. 

Here's how we use `open()` with a second argument to open a file and write a single line of text to it:

    my_file = open("out.txt", "w")
    my_file.write("Hello world")

Because the output is being written to the file in this example, you won't see any output on the screen if you run it. To check that the code has worked, you'll have to run it, then open the file `out.txt` in your text editor and check that its contents are what you expect.

Remember that with `write()`, just like with `print()`, we can use any string as the argument. This also means that we can use any method or function that returns a string. The following are all perfectly OK:

    # write "abcdef"
    my_file.write("abc" + "def")
    
    # write "8"
    my_file.write(str(len('AGTGCTAG')))
    
    # write "TTGC"
    my_file.write("ATGC".replace('A', 'T'))
    
    # write "atgc"
    my_file.write("ATGC".lower())
    
    # write contents of my_variable
    my_file.write(my_variable)
    
## Closing files
There's one more important file method to look at before we finish this section – `close()`. Unsurprisingly, this is the opposite of `open()` (but note that it's a method, whereas `open()` is a function).  We should call `close()` after we're done reading or writing to a file – we won't go into the details here, but it's a good habit to get into as it avoids some types of bugs that can be tricky to track down. `close()` is an unusual method as it takes no arguments (so it's called with an empty pair of parentheses) and doesn't return any useful value:

    my_file = open("out.txt", "w")
    my_file.write("Hello world")
    # remember to close the file
    my_file.close()

## Paths and folders

So far, we have only dealt with opening files in the same folder that we are running our program. What if we want to open a file from a different part of the file system?

The `open()` function is quite happy to deal with files from anywhere on your computer, as long as you give the full path (i.e. the sequence of folder names that tells you the location of the file). Just give the path to the file as the argument rather than the name of the file. The format of the file path looks different depending on your operating system.  If you're using Windows, the path will look like this:

    my_file = open("c:/windows/Desktop/myfolder/myfile.txt")

Notice that the folder names are separated by forward slashes rather than the back slashes that Windows normally uses. This is to avoid problems with special characters like the ones we saw on the previous page.

If you're using a Mac or Linux machine, then the path will look slightly different:

    my_file = open("/home/martin/myfolder/myfile.txt")

## Recap

We've taken a whole chapter to introduce the various ways of reading and writing to files, because it's such an important part of building programs that are useful in real life. We've seen how working with file contents is always a two step process – we must open a file before reading or writing – and looked at several common pitfalls. 

We've also introduced a couple of new concepts that are more widely applicable. We've encountered our first example of an optional argument in the `open()` function (we'll see more of these in future sections).  We've also encountered the first example of a complex data type – the file object – and seen how we can do useful things with it by calling its various methods, in contrast to the simple strings and numbers that we've been working with in the previous chapter. In future sections, we'll learn about more of these complex data types and how to use them. 

## Exercises
To practice using files in these exercise, you'll need a file called *genomic_dna.txt* to use as a test - [click here to download it][1]. Remember, you can always find solutions to all the exercises in the [Python for Biologists books][2]. 

[2]: /python-books
[1]: /s/genomic_dna.txt

### Splitting genomic DNA

Look at the file called *genomic_dna.txt* – it contains the same piece of genomic DNA that we were using in the final exercise from the previous page. Write a program that will split the genomic DNA into coding and non-coding parts, and write these sequences to two separate files. 

### Writing a FASTA file
FASTA file format is a commonly used DNA and protein sequence file format. A single sequence in FASTA format looks like this:

    >sequence_name
    ATCGACTGATCGATCGTACGAT

Where `sequence_name` is a header that describes the sequence (the greater-than symbol indicates the start of the header line). Often, the header contains an accession number that relates to the record for the sequence in a public sequence database. A single FASTA file can contain multiple sequences, like this:

    >sequence_one
    ATCGATCGATCGATCGAT
    >sequence_two
    ACTAGCTAGCTAGCATCG
    >sequence_three
    ACTGCATCGATCGTACCT

Write a program that will create a FASTA file for the following three sequences – make sure that all sequences are in uppercase and only contain the bases A, T, G and C.

| Sequence header | DNA sequence |
|-----------------|-------------|
|ABC123 | ATCGTACGATCGATCGATCGCTAGACGTATCG|
|DEF456 | actgatcgacgatcgatcgatcacgact|
|HIJ789 | ACTGAC-ACTGT--ACTGTA----CATGTG|

### Writing multiple FASTA files

Use the data from the previous exercise, but instead of creating a single FASTA file, create three new FASTA files – one per sequence. The names of the FASTA files should be the same as the sequence header names, with the extension .fasta.

## Solutions
You can find solutions to all the exercises, along with explanations of how they work, by signing up for the [online course](/python-for-biologists-online-course).


