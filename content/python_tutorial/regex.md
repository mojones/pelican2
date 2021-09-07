Title: Python tutorial part seven
Date: 2010-12-03 10:20
Modified: 2010-12-05 19:30
Category: tutorial
Tags: python, tutorial
slug: tutorial/regex
Authors: Martin
Summary: Python tutorial part 7: regular expressions

## The importance of patterns in biology

A lot of what we do when writing programs for biology can be described as searching for patterns in strings. The obvious examples come from the analysis of biological sequence data – remember that DNA, RNA and protein sequences are just strings. Many of the things we want to look for in biological sequences can be described in terms of patterns:

- protein domains
- DNA transcription factor binding motifs
- restriction enzyme cut sites
- degenerate PCR primer sites
- runs of mononucleotides

However, it's not just sequence data that can have interesting patterns. As we discussed in when talking about files, most of the other types of data we have to deal with in biology comes in the form of strings inside text files – things like:

- read mapping locations
- geographical sample coordinates
- taxonomic names
- gene names
- gene accession numbers
- BLAST search results

In previous pages, we've looked at some programming tasks that involve pattern recognition in strings. We've seen how to count individual amino acid residues (and even groups of amino acid residues) in protein sequences, and how to identify restriction enzyme cut sites in DNA sequences. We've also seen how to examine parts of gene names and match them against individual characters. 

The common theme among all these problems is that they involve searching for a fixed pattern. But there are many problems that we want to solve that require more flexible patterns. For example:

- given a DNA sequence, what's the length of the poly-A tail?
- given a gene accession name, extract the part between the third character and the underscore
- given a protein sequence, determine if it contains this highly redundant protein domain motif

Because these types of problems crop up in so many different fields, there's a standard set of tools in Python for dealing with them: **regular expressions**. Regular expressions are a topic that might not be covered in a general purpose programming book, but because they're so useful in biology, we're going to devote the whole of this section to looking at them.

Although the tools for dealing with regular expressions are built in to Python, they are not made automatically available when you write a program. In order to use them we must first talk about modules. 

### Modules in Python

The functions and data types that we've discussed so far have been the basic ones that are likely to be needed in pretty much every program – tools for dealing with strings and numbers, for reading and writing files, and for manipulating lists of data. As such, they are automatically made available when we start to create a Python program. If we want to open a file, we simply write a statement that uses the `open()` function. 

However, there's another category of tools in Python which are more specialized. Regular expressions are one example, but there is a large list of specialized tools which are very useful when you need them, but are not likely to be needed for the majority of programs. Examples include tools for doing advanced mathematical calculations, for downloading data from the web, for running external programs, and for manipulating dates. Each collection of specialized tools – really just a collection of specialized functions and data types – is called a **module**. 

For reasons of efficiency, Python doesn't automatically make these modules available in each new program, as it does with the more basic tools. Instead, we have to explicitly load each module of specialized tools that we want to use inside our program. To load a module we use the `import` statement. For example, the module that deals with regular expressions is called `re`, so if we want to write a program that uses the regular expression tools we must include the line:

    import re

at the top of our program. When we then want to use one of the tools from a module, we have to prefix it with the module name. For example, to use the regular expression `search()` function (which we'll discuss) we have to write:

    re.search(pattern, string)

rather than simply:

    search(pattern, string)
    
If we forget to import the module which we want to use, or forget to include the module name as part of the function call, we will get a `NameError`.

We'll encounter various other modules in future pages. For the rest of this page specifically, all code examples will require the `import re` statement in order to work. For clarity, we won't include it, so if you want try running any of the examples, you'll need to add it at the start of your code.

### Raw strings

Writing regular expression patterns, as we'll see in the next section of this section, requires us to type a lot of special characters. Recall from the section on manipulating text that certain combinations of characters are interpreted by Python to have a special meaning. For example, `\n` means start a new line, and `\t` means insert a tab character. 

Unfortunately, there are a limited number of special characters to go round, so some of the characters that have a special meaning in regular expressions clash with the characters that already have a special meaning. Python's way round this problem is to have a special rule for strings: if we put the letter `r` immediately before the opening quotation mark, then any special characters inside the string are ignored:

    print(r"\t\n")

The `r` stands for raw, which is Python's description for a string where special characters are ignored. Notice that the `r` goes outside the quotation marks – it is not part of the string itself. We can see from the output that the above code prints out the string just as we've written it:

>\t\n

without any tabs or newlines. You'll see this special raw notation used in all the regular expression code examples on this page – even when it's not strictly necessary – because it's a good habit to get in to. 

## Searching for a pattern in a string

We'll start off with the simplest regular expression tool. `re.search()` is a true/false function that determines whether or not a pattern appears somewhere in a string. It takes two arguments, both strings. The first argument is the pattern that you want to search for, and the second argument is the string that you want to search in. For example, here's how we test if a DNA sequence contains an EcoRI restriction site:

    dna = "ATCGCGAATTCAC"
    if re.search(r"GAATTC", dna):
    	print("restriction site found!")

Notice that we've used the raw notation for the pattern string, even though it's not strictly necessary since it doesn't contain any special characters.

### Alternation

Now that we've seen a simple example of how to use `re.search()`, let's look at something a bit more interesting. This time, we'll check for the presence of an AvaII recognition site, which can have two different sequences: GGACC and GGTCC. One way to do this would be to use the techniques we learned in the previous chapter to make a complex condition using `or`:

    dna = "ATCGCGAATTCAC"
    if re.search(r"GGACC", dna) or re.search(r"GGTCC", dna):
    	print("restriction site found!")
	
But a better way is to capture the variation in the AvaII site using a regular expression. One useful feature of regular expressions is called alternation. To represent a number of different alternatives, we write the alternatives inside parentheses separated by a pipe character. In the case of AvaII, there are two alternatives for the third base – it can be either A or T – so the pattern looks like this:

>GG(A|T)CC

Writing the pattern as a raw string and putting it inside a call to `re.search()` gives us the code:

    dna = "ATCGCGAATTCAC"
    if re.search(r"GG(A|T)CC", dna):
    	print("restriction site found!")

Notice the power of what we've done here; we've written a single pattern which captures all the variation in the sequence in one string.  

### Character groups

The BisI restriction enzyme cuts at an even wider range of motifs – the pattern is GCNGC, where N represents any base. We can use the same alternation technique to represent this pattern:

>GC(A|T|G|C)GC

However, there's another regular expression feature that lets us write the pattern more concisely. A pair of square brackets with a list of characters inside them can represent any one of these characters. So the pattern `GC[ATGC]GC` will match GCAGC, GCTGC, GCGGC and GCCGC. Here's a program that checks for the presence of a BisI restriction site using character groups:

    dna = "ATCGCGAATTCAC"
    if re.search(r"GC[ATGC]GC", dna):
    	print("restriction site found!")

Taken together, alternation and character groups do a pretty good job of capturing the kind of variation that we're interested in for biological programming. Before we move on, here are two short cuts that deal with specific, common scenarios. 

If we want a character in a pattern to match any character in the input, we can use a period or dot. For example, the pattern `GC.GC` would match all four possibilities in the BisI example above. However, the period would also match any character which is not a DNA base, or even a letter. Therefore, the whole pattern would also match GCFGC, GC&GC and GC9GC, which may not be what we want, so be careful when using this feature.

Sometimes it's easier, rather than listing all the acceptable characters, to specify the characters that we don't want to match. Putting a caret `^` at the start of a character group like this 

>[^XYZ] 

will negate it, and match any character that isn't in the group. The example above will match any character other than X, Y or Z. 

### Quantifiers

The regular expression features discussed above let us describe variation in the individual characters of patterns. Another group of features, quantifiers, let us describe variation in the number of times a section of a pattern is repeated. 

A question mark immediately following a character means that that character is optional – it can match either zero or one times. So in the pattern `GAT?C` the T is optional, and the pattern will match either GATC or GAC. If we want to apply a question mark to more than one character, we can group the characters in parentheses. For example, in the pattern `GGG(AAA)?TTT` the group of three As is optional, so the pattern will match either GGGAAATTT or GGGTTT. 

A plus sign immediately following a character or group means that the character or group must be present but can be repeated any number of times – in other words, it will match one or more times. For example, the pattern `GGGA+TTT` will match three Gs, followed by one or more As, followed by three Ts. So it will match GGGATTT, GGGAATT, GGGAAATT, etc. but not GGGTTT.

An asterisk immediately following a character or group means that the character or group is optional, but can also be repeated. In other words, it will match zero or more times. For example, the pattern `GGGA*TTT` will match three Gs, followed by zero or more As, followed by three Ts. So it will match GGGTTT, GGGATTT, GGGAATTT, etc. It's the most flexible quantifier.

If we want to match a specific number of repeats, we can use curly brackets. Following a character or group with a single number inside curly brackets will match exactly that number of repeats. For example, the pattern `GA{5}T` will match GAAAAAT but not GAAAAT or GAAAAAAT. Following a character or group with a pair of numbers inside curly brackets separated with a comma allows us to specify an acceptable range of repeats. For example, the pattern `GA{2,4}T` means G, followed by between 2 and 4 As, followed by T. So it will match GAAT, GAAAT and GAAAAT but not GAT or GAAAAAT. 

Just like with substrings, we can leave out the lower or upper limits. `A{3,}` will match three or more As, and `G{,7}` will match up to seven Gs. 

### Positions

The final set of regular expression tools we're going to look at don't represent characters at all, but rather positions in the input string. The caret symbol `^` matches the start of a string8, and the dollar symbol `$` matches the end of a string. The pattern `^AAA` will match AAATTT but not GGGAAATTT. The pattern `GGG$` will match AAAGGG but not AAAGGGCCC. 

### Combining

The real power of regular expressions comes from combining these tools. We can use quantifiers together with alternations and character groups to specify very flexible patterns. For example, here's a complex pattern to identify full-length eukaryotic messenger RNA sequences: 

>^AUG[AUGC]{30,1000}A{5,10}$

Reading the pattern from left to right, it will match:

- an AUG start codon at the beginning of the sequence
- followed by between 30 and 1000 bases which can be A, U, G or C
- followed by a poly-A tail of between 5 and 10 bases at the end of the sequence

As you can see, regular expressions can be quite tricky to read until you're familiar with them! However, it's well worth investing a bit of time learning to use them, as the same notation is used across multiple different tools. The regular expression skills that you learn in Python are transferable to other programming languages, command line tools, and text editors. 

The features we've discussed above are the ones most useful in biology. However, there are many more regular expression features available in Python. If you want to become a regular expression master, it's worth reading up on *greedy vs. minimal quantifiers*, *back-references*, *lookahead* and *lookbehind* assertions, and built in *character classes*.

Before we move on to look at some more sophisticated uses of regular expressions, it's worth noting that there's a method similar to `re.search()` called `re.match()`. The difference is that `re.search()` will identify a pattern occurring anywhere in the string, whereas `re.match()` will only identify a pattern if it matches the entire string. Most of the time we want the former behaviour.

## More ways to use patterns

In all the examples we've seen so far, we used `re.search()` as the condition in an `if` statement to decide whether or not a string contained a pattern. However, there are lots more interesting things we can do with regular expression patterns.

### Extracting the part that matched

Often in our programs we want to find out not only if a pattern matched, but what part of the string was matched. To do this, we need to store the result of using `re.search()`, then use the `group()` method on the resulting object. 

When introducing the `re.search()` function above I said that it was a true/false function. That's not exactly correct though – if it finds a match, it doesn't return `True`, but rather an object that is evaluated as true in a conditional context (if the distinction doesn't seem important to you, then you can safely ignore it). 

The value that's actually returned is a **match object** – a new data type that we've not encountered before. Like a file object, a match object doesn't represent a simple thing, like a number or string. Instead, it represents the results of a regular expression search. And just like a file object, a match object has a number of useful methods for getting data out of it. 

One such method is the `group()` method. If we call this method on the result of a regular expression search, we get the portion of the input string that matched the pattern. Here's an example: imagine we want to take a DNA sequence and determine whether or not it contains any ambiguous bases – i.e. any bases that are not A, T, G or C. We can use a negated character group to write a regular expression that will match any non-ATGC base:

>[^ATGC]

and test the sequence like this:

    dna = "ATCGCGYAATTCAC"
    
    if re.search(r"[^ATGC]", dna):
        print("ambiguous base found!")
    
The code above tells us that the DNA sequence contained a non-ATGC base, but it doesn't tell us exactly what the base was. To do that, we need to call the `group()` method on the match object like this:

    dna = "CGATNCGGAACGATC"
    m = re.search(r"[^ATGC]", dna)
    
    # m is now a match object
    if m:
        print("ambiguous base found!")
        ambig = m.group()
        print("the base is " + ambig)

The output from this program:

>ambiguous base found! 
>the base is N

tells us not only that the sequence contained an ambiguous base, but that the ambiguous base was N.

### Extracting multiple groups

What if we want to extract more than one bit of the pattern? Say we want to match a scientific name like *Homo sapiens* or *Drosophila melanogaster*. The pattern is relatively simple: multiple characters, followed by a space, followed by multiple characters:

>.+ .+

To match multiple characters we're using a period (meaning any character) followed by a plus (meaning repeated at least once but possibly multiple times).

Now let's say that we want to extract the genus name and species name into separate variables. We add parentheses around the parts of the pattern that we want to store:

>(.+) (.+)
 
This is called **capturing** part of the pattern.  We can now refer to the captured bits of the pattern by supplying an argument to the `group()` method. `group(1)` will return the bit of the string matched by the section of the pattern in the first set of parentheses, `group(2)` will return the bit matched by the second, etc.:

    scientific_name = "Homo sapiens" 
     
    m = re.search("(.+) (.+)", scientific_name) 
     
    if m: 
        genus = m.group(1) 
        species = m.group(2) 
        print("genus is " + genus + ", species is " + species)

The output shows how the two bits of the same pattern were stored in different variables. Note that the space, which was part of the pattern but not part of the captured groups, isn't in either of the two variables:

>genus is Homo, species is sapiens

If you're keeping count, you'll realize that we now have three different roles for parentheses in regular expressions:

- surrounding the alternatives in an alternation
- grouping parts of a pattern for use with a quantifier
- defining parts of a pattern to be extracted after the match

### Getting match positions

As well as containing information about the contents of a match, the match object also holds information about the position of the match. The `start()` and `end()` methods get the positions of the start and end of the pattern on the string. Let's go back to our ambiguous base example and find the position of the ambiguous base:

    dna = "CGATNCGGAACGATC" 
    m = re.search(r"[^ATGC]", dna) 
     
    if m: 
        print("ambiguous base found!") 
        print("at position " + str(m.start()))

Remember that we start counting from zero, so in this case, the match starting at the fifth base has a start position of four:

>ambiguous base found! 
>at position 4

### Multiple matches

An obvious limitation of the above example is that it can only find a single ambiguous base, because `re.search()` can only find a single match. To process multiple matches, we need to switch to `re.finditer()`, which returns a list of match objects which we can process in a loop:

    dna = "CGCTCNTAGATGCGCRATGACTGCAYTGC" 
     
    matches = re.finditer(r"[^ATGC]", dna) 
    for m in matches: 
    	base = m.group() 
    	pos  = m.start() 
    	print(base + " found at position " + str(pos))

We can see from the output that we now find all three parts of the string that match the pattern:

>N found at position 5 
>R found at position 15 
>Y found at position 25

### Getting multiple matches as strings

A common scenario is where we want to get a list of all the parts of a string that match a given pattern. Here's a regular expression pattern that matches runs of A and T longer than five bases:

>[AT]{6,}

Here's a DNA sequence with the bits that we want to extract in bold:

ACTGC**ATTATAT**CGTACG**AAATTATA**CGCGCG

We could extract the bits of the string that match the pattern using `re.finditer()` and `group()`:

    dna = "CTGCATTATATCGTACGAAATTATACGCGCG" 
     
    matches = re.finditer(r"[AT]{6,}", dna) 
     
    result = [] 
    for m in matches: 
        result.append(m.group()) 
     
    print(result)
    
but because this is a common problem, there's a special method for dealing with it called `re.findall()`.  Just like the other methods we've seen, `re.findall()` takes the pattern and the string as arguments, but rather than returning a list of match objects it returns a list of strings. We can rewrite our code like this:

    dna = "CTGCATTATATCGTACGAAATTATACGCGCG" 
    result = re.findall(r"[AT]{6,}", dna) 
    print(result)

### Splitting a string using a regular expression

Occasionally it can be useful to split a string using a regular expression pattern as the delimiter. The normal string `split()` method doesn't allow this, but the `re` module has a `split()` function of its own that takes a regular expression pattern as an argument. The first argument is the pattern, the second argument is the string to be split. 

Imagine we have a consensus DNA sequence that contains ambiguity codes, and we want to extract all runs of contiguous unambiguous bases. We need to split the DNA string wherever we see a base that isn't A, T, G or C:

    dna = "ACTNGCATRGCTACGTYACGATSCGAWTCG"
    runs = re.split(r"[^ATGC]", dna)
    print(runs)

The output shows how the function works – the return value is a list of strings:

>['ACT', 'GCAT', 'GCTACGT', 'ACGAT', 'CGA', 'TCG']

Notice that the bits of the string that matched the pattern are excluded from the output (just like the delimiters are excluded from the output when we use the normal `split()` method). 

## Recap
In this section we learned about regular expressions, and the functions and methods that use them. 
We started with a brief introduction to two concepts that, while not part of the regular expression tools, are necessary in order to use them – modules and raw strings. We got a brief overview of features that can be used in regular expression patterns, and a quick look at the range of different things we can do with them. Just as regular expressions themselves can range from simple to complex, so can their uses. We can use regular expressions for simple tasks – like determining whether or not a sequence contains a particular motif – or for complicated ones, like identifying messenger RNA sequences by using complex patterns. 

Before we move on to the exercises, it's important to recognize that for any given pattern, there are probably multiple ways to describe it using a regular expression. Near the start of the page, we came up with the pattern 

    GG(A|T)CC 

to describe the AvaII restriction enzyme recognition site, but the same pattern could also be written as:

    GG[AT]CC
    (GGACC|GGTCC) 
    (GGA|GGT)CC
    G{2}[AT]C{2}

As with other situations where there are multiple different ways to write the same thing, it's best to be guided by what is clearest to read. 

## Exercises

Have a go at the following two exercises. The first is a simple multi-part problem that will give you experience writing a bunch of different regular expressions while keeping the bulk of the program the same. The second one is a classic bioinformatics problem: predicting the fragments that will be produced by digesting a given DNA sequence with a restriction enzyme. Remember, you can always find solutions and explanations for all exericses in the [Python for Biologists books][1]. 

[1]: /python-books

### Accession names

Here's a list of made up gene accession names:

    accessions = ['xkn59438', 'yhdck2', 'eihd39d9', 'chdsye847', 'hedle3455', 'xjhd53e', '45da', 'de37dp']

Copy and paste this line into your text editor (or whatever you're using to write Python code).

Write a program that will print only the accession names that satisfy the following criteria – treat each criterion separately:

- contain the number 5
- contain the letter d or e
- contain the letters d and e in that order
- contain the letters d and e in that order with a single letter between them
- contain both the letters d and e in any order
- start with x or y
- start with x or y and end with e
- contain three or more digits in a row
- end with d followed by either a, r or p

### Double digest
[Click here][2] to download a file *dna.txt* which contains a made up DNA sequence. Predict the fragment lengths that we will get if we digest the sequence with two made up restriction enzymes – AbcI, whose recognition site is ANT/AAT, and AbcII, whose recognition site is GCRW/TG. **The forward slashes (/) in the recognition sites represent the place where the enzyme cuts the DNA**. 

[2]: /s/dna.txt

## SOLUTIONS

You can find solutions to all the exercises, along with explanations of how they work, by signing up for the [online course](/python-for-biologists-online-course).
