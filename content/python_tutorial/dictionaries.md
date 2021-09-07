Title: Python tutorial part eight
Date: 2010-12-03 10:20
Modified: 2010-12-05 19:30
Category: tutorial
Tags: python, tutorial
slug: tutorial/dictionaries
Authors: Martin
Summary: Python tutorial part 8: dictionaries

## Storing paired data

Suppose we want to count the number of As in a DNA sequence. Carrying out the calculation is quite straightforward:

    dna = "ATCGATCGATCGTACGCTGA"
    a_count = dna.count("A")

How will our code change if we want to generate a complete list of base counts for the sequence? We'll add a new variable for each base:

    dna = "ATCGATCGATCGTACGCTGA"
    a_count = dna.count("A")
    t_count = dna.count("T")
    g_count = dna.count("G")
    c_count = dna.count("C")

and now our code is starting to look rather repetitive. It's not too bad for the four individual bases, but what if we want to generate counts for the 16 dinucleotides:

    dna = "ATCGATCGATCGTACGCTGA"
    aa_count = dna.count("AA")
    at_count = dna.count("AT")
    ag_count = dna.count("AG")
    ...
    
or the 64 trinucleotides:

    dna = "ATCGATCGATCGTACGCTGA"
    aaa_count = dna.count("AAA")
    aat_count = dna.count("AAT")
    aag_count = dna.count("AAG")
    ...
    
For trinucleotides and longer, the situation is particularly bad. The DNA sequence is 20 bases long, so it only contains 18 overlapping trinucleotides in total:

    ATCGATCGATCGTACGCTGA
    ATC
     TCG
      CGA
       GAT
    ...

So there can be, at most, 18 unique trinucleotides in the sequence (and for a repetitive sequence, many fewer unique trinucleotides). This means that at least 46 out of our 64 variables will hold the value zero.

One possible way round this is to store the values in a list. Let's look at an example involving dinucleotides. If we create a list of the 16 possible dinucleotides we can iterate over it, calculate the count for each one, and store all the counts in a list. Take a look at the code – the list of dinucleotides is quite long so it's been split over  four lines to make it easier to read:

    dna = "ATGATCGATCGAGTGA"
    dinucleotides = ['AA','AT','AG','AC',
                     'TA','TT','TG','TC',
                     'GA','GT','GG','GC',
                     'CA','CT','CG','CC']
    all_counts = []
    for dinucleotide in dinucleotides:
    	count = dna.count(dinucleotide)
    	print("count is " + str(count) + " for " + dinucleotide)
    	all_counts.append(count)
    print(all_counts)

Although the code is above is quite compact, and doesn't require huge numbers of variables, the output shows two problems with this approach:

>count is 0 for AAA 
>count is 1 for AAT 
>count is 0 for AAG 
>count is 0 for AAC 
>count is 0 for ATA 
>count is 0 for ATT 
>count is 1 for ATG 
>count is 2 for ATC 
>...
>[0, 1, 0, 0, 0, 0, 1, 2, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 2, 0, 0, 0, 0, 0, 2, 0, 0, 2, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 1, 1, 0, 1, 0, 0, 0, 0]

Firstly, the data are still very sparse – the vast majority of the counts are zero. Secondly, the counts themselves are now disconnected from the dinucleotides. If we want to look up the count for a single dinucleotide – for example,  `TG` – we first have to figure out that `TG` was the 7th dinucleotide in the list. Only then can we get the element at the correct index:

    print("count for TG is " + str(all_counts[6]))

We can try various tricks to get round this problem. What if we used the `index()` method to figure out the position of the dinucleotide we are looking for in the list?

    i = dinucleotides.index('TG') 
    print(all_counts[i])

This works because we have two lists of the same length, with a one-to-one correspondence between the elements:

    print(dinucleotides)
    print(all_counts)
    
>['AA', 'AT', 'AG', 'AC', 'TA', 'TT', 'TG', 'TC', 'GA', 'GT', 'GG', 'GC', 'CA', 'CT', 'CG', 'CT'] 
>[2, 2, 0, 2, 0, 0, 2, 0, 3, 0, 0, 0, 0, 0, 1, 0] 

This is a little bit nicer, but still has major drawbacks. We're still storing all those zeros, and now we have two lists to keep track of. We need to be incredibly careful when manipulating either of the two lists to make sure that they stay perfectly synchronized – if we make any change to one list but not the other, then there will no longer be a one-to-one correspondence between elements and we'll get the wrong answer when we try to look up a count. 

This approach is also slow. To find the index of a given dinucleotide in the dinucleotides list, Python has to look at each element one at a time until it finds the one we're looking for. This means that as the size of the list grows, the time taken to look up the count for a given element will grow alongside it. 

If we take a step back and think about the problem in more general terms, what we need is a way of storing pairs of data (in this case, dinucleotides and their counts) in a way that allows us to efficiently look up the count for any given dinucleotide. This problem of storing paired data is incredibly common in programming. We might want to store:

- protein sequence names and their sequences
- DNA restriction enzyme names and their motifs
- codons and their associated amino acid residues
- colleagues' names and their email addresses
- sample names and their co-ordinates
- words and their definitions

All these are examples of what we call **key-value** pairs. In each case we have pairs of keys and values:

| Key  |  Value |
|---|---|
| dinucleotide  | count  |
| name  |  protein sequence |
| name  |  restriction enzyme motif |
| codon  | amino acid residue  |
| sample  | coordinates  |
| word | definition |


The last example in this table – words and their definitions – is an interesting one because we have a tool in the physical world for storing this type of data: a dictionary. Python's tool for solving this type of problem is also called a dictionary (usually abbreviated to **dict**) and in this section we'll see how to create and use them. 

## Creating a dictionary

The syntax for creating a dictionary is similar to that for creating a list, but we use curly brackets rather than square ones. Each pair of data, consisting of a key and a value, is called an item. When storing items in a dictionary, we separate them with commas. Within an individual item, we separate the key and the value with a colon. Here's a bit of code that creates a dictionary of restriction enzymes and their regular expressions with three items:

    enzymes = { 'EcoRI':r'GAATTC','AvaII':r'GG(A|T)CC', 'BisI':r'GC[ATGC]GC' }

In this case, the keys and values are both strings. Splitting the dictionary definition over several lines makes it easier to read:

    enzymes = { 
    	'EcoRI' : r'GAATTC', 
    	'AvaII' : r'GG(A|T)CC', 
    	'BisI'  : r'GC[ATGC]GC' 
    }
    
and doesn't affect how the code works. To retrieve a bit of data from the dictionary – i.e. to look up the motif for a particular enzyme – we write the name of the dictionary, followed by the key in square brackets:

    print(enzymes['BisI'])

The code looks very similar to using a list, but instead of giving the *index* of the element we want, we're giving the *key* for the value that we want to retrieve. 

Dictionaries are a very useful way to store data, but they come with some restrictions. The only types of data we are allowed to use as keys are strings and numbers, so we can't, for example, create a dictionary where the keys are file objects. Values can be whatever type of data we like. Also, keys must be unique – we can't store multiple values for the same key. 

### Building dicts

In real life programs, it's relatively rare that we'll want to create a dictionary all in one go like in the example above. More often, we'll want to create an empty dictionary, then add key/value pairs to it (just as we often create an empty list and then add elements to it). 

To create an empty dictionary we simply write a pair of curly brackets on their own, and to add elements, we use the square brackets notation on the left hand side of an assignment. Here's a bit of code that stores the restriction enzyme data one item at a time:

    enzymes = {}
    enzymes['EcoRI'] = r'GAATTC'
    enzymes['AvaII'] =  r'GG(A|T)CC'
    enzymes['BisI'] =  r'GC[ATGC]GC' 

We can delete a key from a dictionary using the `pop()` method. `pop()` actually returns the value and deletes the key at the same time:

    # remove the EcoRI enzyme from the dict
    enzymes.pop('EcoRI')

Let's take another look at the dinucleotide count example from the start of the module. Here's how we store the dinucleotides and their counts in a dict:

    dna = "AATGATGAACGAC" 
    dinucleotides = ['AA','AT','AG','AC', 
                     'TA','TT','TG','TC', 
                     'GA','GT','GG','GC', 
                     'CA','CT','CG','CT'] 
    all_counts = {} 
    for dinucleotide in dinucleotides: 
        count = dna.count(dinucleotide) 
        print("count is " + str(count) + " for " + dinucleotide) 
        all_counts[dinucleotide] = count 
    print(all_counts) 

We can see from the output that the dinucleotides and their counts are stored together in the `all_counts` variable:

>{'AA': 2, 'AC': 2, 'GT': 0, 'AG': 0, 'TT': 0, 'CG': 1, 'GG': 0, 'GC': 0, 'AT': 2, 'GA': 3, 'TG': 2, 'CT': 0, 'CA': 0, 'TC': 0, 'TA': 0} 

We still have a lot of repetitive counts of zero, but looking up the count for a particular dinucleotide is now very straightforward:

    print(all_counts['TA'])

We no longer have to worry about either "memorizing" the order of the counts or maintaining two separate lists. 
Let's now see if we can find a way of avoiding storing all those zero counts. We can add an `if` statement that ensures that we only store a count if it's greater than zero:

    dna = "AATGATGAACGAC" 
    dinucleotides = ['AA','AT','AG','AC', 
                     'TA','TT','TG','TC', 
                     'GA','GT','GG','GC', 
                     'CA','CT','CG','CT'] 
    all_counts = {} 
    for dinucleotide in dinucleotides: 
        count = dna.count(dinucleotide) 
        if count > 0: 
            all_counts[dinucleotide] = count 
    print(all_counts) 

When we look at the output from the above code, we can see that the amount of data we're storing is much smaller – just the counts for the dinucleotides that actually occur in the sequence:

>{'AA': 2, 'AC': 2, 'CG': 1, 'AT': 2, 'GA': 3, 'TG': 2}

Now we have a new problem to deal with. Looking up the count for a given dinucleotide works fine when the count is positive:

    print(all_counts['TA'])

But when the count is zero, the dinucleotide doesn't appear as a key in the dict:

    print(all_counts['TC'])

so we will get a `KeyError` when we try to look it up:

>KeyError: 'TC'

There are two possible ways to fix this. We can check for the existence of a key in a dict (just like we can check for the existence of an element in a list), and only try to retrieve it once we know it exists:

    if 'TC' in all_counts:
    	print(all_counts('TC'))
    else
        print(0)
    
Alternatively, we can use the dict's `get()` method. `get()` usually works just like using square brackets: the following two lines do exactly the same thing:

    print(all_counts['TC'])
    print(all_counts.get('TC'))

The thing that makes `get()` really useful, however, is that it can take an optional second argument, which is the default value to be returned if the key isn't present in the dict. In this case, we know that if a given dinucleotide doesn't appear in the dict then its count is zero, so we can give zero as the default value and use `get()` to print out the count for any dinucleotide:

    print("count for TG is " + str(all_counts.get('TG', 0))) 
    print("count for TT is " + str(all_counts.get('TT', 0)))
    print("count for GC is " + str(all_counts.get('GC', 0)))
    print("count for CG is " + str(all_counts.get('CG', 0)))

As we can see from the output, we now don't have to worry about whether or not any given dinucleotide appears in the dict – `get()` takes care of everything and returns zero when appropriate:

>count for TG is 2
>count for TT is 0 
>count for GC is 0 
>count for CG is 1 

More generally, assuming we have a dinucleotide string stored in the variable `dn`, we can run a line of code like this:

    print("count for " + dn + " is " + str(all_counts.get(dn, 0))) 

and be sure of getting the right answer.

## Iterating over a dictionary

What if, instead of looking up a single item from a dictionary, we want to do something for all items? For example, imagine that we wanted to take our `all_counts` dict variable from the code above and print out all dinucleotides where the count was 2. One way to do it would be to iterate over the list of dinucleotides, looking up the count for each one and deciding whether or not to print it:

    for dinucleotide in dinucleotides: 
        if all_counts.get(dinucleotide, 0) == 2: 
            print(dinucleotide) 
        
As we can see from the output, this works perfectly well:

>AA 
>AT 
>AC 
>TG 

For this example, this approach works because we have a list of the dinucleotides already written as part of the program. Most of the time when we create a dict, however, we'll do it using some other method which doesn't require an explicit list of the keys. For example, here's a different way to generate a dict of dinucleotide counts which uses two nested `for` loops to enumerate all the possible dinucleotides:

    dna = "AATGATGAACGAC" 
    bases = ['A','T','G','C'] 
    all_counts = {} 
    for base1 in bases: 
        for base2 in bases: 
            dinucleotide = base1 + base2 
            count = dna.count(dinucleotide) 
            if count > 0: 
                all_counts[dinucleotide] = count 

The resulting dict is just the same as in our previous examples, but because we haven't got a list of dinucleotides handy, we have to take a different approach to find all the dinucleotides where the count is two. Fortunately, the information we need – the list of dinucleotides that occur at least once – is stored in the dict as the keys.  

### Iterating over keys

When used on a dict, the `keys()` method returns a list of all the keys in the dict:

    print(all_counts.keys())

Looking at the output confirms that this is the list of dinucleotides we want to consider (remember that we're looking for dinucleotides with a count of two, so we don't need to consider ones that aren't in the dict as we already know that they have a count of zero):

>['AA', 'AC', 'CG', 'AT', 'GA', 'TG']

To find all the dinucleotides that occur exactly twice in the DNA sequence we can take the output of `keys()` and iterate over it, keeping the body of the loop the same as before:

    for dinucleotide in all_counts.keys():
    	if all_counts.get(dinucleotide) == 2:
		    print(dinucleotide)

This version prints exactly the same set of dinucleotides as the approach that used our list:

>AA 
>AC 
>AT 
>TG 

Before we move on, take a moment to compare the output immediately above this paragraph with the output from the version that used the list from earlier in this section. You'll notice that while the set of dinucleotides is the same, the order in which they appear is different. This illustrates an important point about dicts – they are inherently unordered. That means that when we use the `keys()` method to iterate over a dict, we can't rely on processing the items in the same order that we added them. This is in contrast to lists, which always maintain the same order when looping. If we want to control the order in which keys are printed we can use the `sorted()` function to sort the list before processing it:

    for dinucleotide in sorted(all_counts.keys()):
	    if all_counts.get(dinucleotide) == 2:
		    print(dinucleotide)
		
### Iterating over items

In the example code above, the first thing we need to do inside the loop is to look up the value for the current key. This is a very common pattern when iterating over dicts – so common, in fact, that Python has a special shorthand for it. Instead of doing this:

    for key in my_dict.keys():
    	value = my_dict.get(key)
    	# do something with key and value
	
We can use the `items()` method to iterate over pairs of data, rather than just keys:

    for key, value in my_dict.items():
    	# do something with key and value
	
The `items()` method does something slightly different from all the other methods we've seen so far in this book; rather than returning a single value, or a list of values, it returns a list of pairs of values. That's why we have to give two variable names at the start of the loop. Here's how we can use the `items()` method to process our dict of dinucleotide counts just like before:

    for dinucleotide, count in all_counts.items():
    	if count == 2:
		    print(dinucleotide)
		
This method is generally preferred for iterating over items in a dict, as it is very readable. 

### Lookup vs. iteration

Before we finish this section; a word of warning: don't make the mistake of iterating over all the items in a dict in order to look up a single value. Imagine we want to look up the number of times the dinculeotide AT occurs in our example above. It's tempting to use the `items()` method  to write a loop that looks at each item in the dict until we find the one we're looking for:

    for dinucleotide, count in all_counts.items():
        if dinucleotide == 'AT':
            print(count)
        
and this will work, but it's completely unnecessary (and slow). Instead, simply use the `get()` method to ask for the value associated with the key you want:

    print(all_counts.get('AT'))
    
## Recap
We started this section by examining the problem of storing paired data in Python. After looking at a couple of unsatisfactory ways to do it using tools that we've already learned about, we introduced a new type of data structure – the dict – which offers a much nicer solution to the problem of storing paired data. 

Later, we saw that the real benefit of using dicts is the efficient lookup they provide. We saw how to create dicts and manipulate the items in them, and several different ways to look up values for known keys. We also saw how to iterate over all the items in dictionary. 

In the process, we uncovered a few restrictions on what dicts are capable of – we're only allowed to use a couple of different data types for keys, they must be unique, and we can't rely on their order. Just as a physical dictionary allows us to rapidly look up the definition for a word but not the other way round, Python dictionaries allow us to rapidly look up the value associated with a key, but not the reverse.

## Exercises

### DNA translation

Here's a dict which represents the genetic code – the keys are codons and the values are amino acid residues:
    
    gencode = {
        'ATA':'I', 'ATC':'I', 'ATT':'I', 'ATG':'M',
        'ACA':'T', 'ACC':'T', 'ACG':'T', 'ACT':'T',
        'AAC':'N', 'AAT':'N', 'AAA':'K', 'AAG':'K',
        'AGC':'S', 'AGT':'S', 'AGA':'R', 'AGG':'R',
        'CTA':'L', 'CTC':'L', 'CTG':'L', 'CTT':'L',
        'CCA':'P', 'CCC':'P', 'CCG':'P', 'CCT':'P',
        'CAC':'H', 'CAT':'H', 'CAA':'Q', 'CAG':'Q',
        'CGA':'R', 'CGC':'R', 'CGG':'R', 'CGT':'R',
        'GTA':'V', 'GTC':'V', 'GTG':'V', 'GTT':'V',
        'GCA':'A', 'GCC':'A', 'GCG':'A', 'GCT':'A',
        'GAC':'D', 'GAT':'D', 'GAA':'E', 'GAG':'E',
        'GGA':'G', 'GGC':'G', 'GGG':'G', 'GGT':'G',
        'TCA':'S', 'TCC':'S', 'TCG':'S', 'TCT':'S',
        'TTC':'F', 'TTT':'F', 'TTA':'L', 'TTG':'L',
        'TAC':'Y', 'TAT':'Y', 'TAA':'_', 'TAG':'_',
        'TGC':'C', 'TGT':'C', 'TGA':'_', 'TGG':'W'}
    

Copy and paste this chunk of code into a new program, then use this dict to write a program which will translate a DNA sequence into protein.  You'll have to figure out how to:

- split the DNA sequence into codons
- look up the amino acid residue for each codon
- join all the amino acids to give a protein

Test your program on a couple of different inputs to see what happens. How does your program cope with a sequence whose length is not a multiple of 3? How does it cope with a sequence that contains unknown bases?

## Solutions
You can find solutions to all the exercises, along with explanations of how they work, by signing up for the [online course](/python-for-biologists-online-course).
