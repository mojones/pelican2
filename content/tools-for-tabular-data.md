Title: Tools for tabular data
Date: 2010-12-03 10:20
Modified: 2010-12-05 19:30
Category: article
Tags: linux, command line, bioinformatics
slug: tools-for-tabular-data
Authors: Martin

# Manipulating tabular output with command-line tools

## Introduction

Surprisingly, given that this is a website about programming, this article is going to be about how to avoid programming. Specifically: how to use simple existing command line tools to process tabular data. 

Even for folks who are comfortable writing programs to process tabular data, being able to use command-line tools to slice, dice and generally manipulate data files is a useful skill to have. For very simple tasks it's often quicker to write a command line to do the job rather than switching mental gears and starting to write a new program. Also, the command line tools we'll be discussing here are likely to be faster than Python for many jobs. 

If this kind of tutorial is useful to you, then [sign up for the newsletter](/newsletter) to get more of it.

## Tabular data

By tabular data I mean simple text files where each line stores a single record and consists of a number of different fields separated by a delimiter. The delimiter – i.e. the thing that separates the fields – can be anything, but the most common examples are tabs, spaces, or commas. In this last case, we sometimes refer to the file as being in Comma Separated Value or CSV format.

Let's look at a few concrete examples. A common type of tabular file that we encounter in biology is BLAST output. Normally when we run BLAST, we get the output in a human-readable format which shows the alignments (or if we're using a web BLAST server we get a nice webpage with graphics). This format is easy for humans to read but not so easy for computers to deal with; if we need to do some processing of out BLAST results then tabular output is often a better option. We can get tabular output from BLAST by giving it the `-m 9` option if we're using the old BLAST package, or giving it the `-outfmt 7` option if we're using the newer BLAST+ package. If we're using a web-based BLAST server, we can download our results in tabular format using the download link at the top


However we get hold of our tabular BLAST results they're going to look something like this:
    
    
    
    # blastp
    # Iteration: 0
    # Query: my_protein
    # RID: BFFAX0Z3015
    # Database: nr
    # Fields: query id, subject ids, % identity, % positives, alignment length, mismatches, gap opens, q. start, q. end, s. start, s. end, evalue, bit score
    # 100 hits found
    my_protein gi|60654447|gb|AAX29914.1| 100.00 100.00 599 0 0 1 599 1 599 0.0 1243
    my_protein gi|397526517|ref|XP_003833169.1| 99.50 100.00 599 3 0 1 599 1 599 0.0 1240
    my_protein gi|18104967|ref|NP_000953.2|;gi|317373262|sp|P23219.2|PGH1_HUMAN 99.83 99.83 599 1 0 1 599 1 599 0.0 1240
    my_protein gi|674214810|gb|AIK97765.1| 99.83 99.83 599 1 0 1 599 1 599 0.0 1239
    my_protein gi|694936914|ref|XP_009455498.1| 99.33 100.00 599 4 0 1 599 1 599 0.0 1239
    ...
    

This is only the first few lines; in reality there will be many more, but they'll all share the same format. The first few lines are comments which tell us various things – the name of the query, the number of hits, etc. By far the most important comment line is this one:
    
    
    
    # Fields: query id, subject ids, % identity, % positives, alignment length, mismatches, gap opens, q. start, q. end, s. start, s. end, evalue, bit score
    

because it tells us the order of the fields in each of the result lines – in other words, it tells us how to interpret the other lines. From looking at the list of fields we can see that, for example, the second field in each line is the name of the subject sequence, and the fifth field is the length of the match.

We'll be using BLAST tabular output for the examples in this tutorial, but it's worth remembering that exactly the same tools can be used for any type of tabular data. For example, here's the first few lines of a BED file which holds annotation for genomic coordinates:
    
    
    
    browser position chr7:127471196-127495720
    browser hide all
    track name="ItemRGBDemo" description="Item RGB demonstration" visibility=2
    itemRgb="On"
    chr7    127471196  127472363  Pos1  0  +  127471196  127472363  255,0,0
    chr7    127472363  127473530  Pos2  0  +  127472363  127473530  255,0,0
    chr7    127473530  127474697  Pos3  0  +  127473530  127474697  255,0,0
    chr7    127474697  127475864  Pos4  0  +  127474697  127475864  255,0,0
    chr7    127475864  127477031  Neg1  0  -  127475864  127477031  0,0,255
    chr7    127477031  127478198  Neg2  0  -  127477031  127478198  0,0,255
    chr7    127478198  127479365  Neg3  0  -  127478198  127479365  0,0,255
    chr7    127479365  127480532  Pos5  0  +  127479365  127480532  255,0,0
    chr7    127480532  127481699  Neg4  0  -  127480532  127481699  0,0,255
    

The data being stored are different, but the layout is just the same as our BLAST example: a handful of comment lines at the top followed by a bunch of lines with tab-separated fields. One more example: here's GFF format, also used for annotating sequence data:
    
    
    
    X	Ensembl	Repeat	2419108	2419128	42	.	.	hid=trf; hstart=1; hend=21
    X	Ensembl	Repeat	2419108	2419410	2502	-	.	hid=AluSx; hstart=1; hend=303
    X	Ensembl	Repeat	2419108	2419128	0	.	.	hid=dust; hstart=2419108; hend=2419128
    X	Ensembl	Pred.trans.	2416676	2418760	450.19	-	2	genscan=GENSCAN00000019335
    X	Ensembl	Variation	2413425	2413425	.	+	.
    X	Ensembl	Variation	2413805	2413805	.	+	.
    

Thinking about the data formats that you encounter in your own work will furnish further examples. 

Before we dive in and start looking at the tools we can use to manipulate tabular data, a few points about your computing environment. In this tutorial we'll be looking at command-line tools – `cut`, `sort`, `head`, `tail`, `uniq`, `wc`, `sed` and `awk`. If you're running Linux, or any UNIX-flavoured operating system, including OSX, these tools are part of the standard library and should already be installed. If you're using Windows, you'll need to either install them ([cygwin](https://www.cygwin.com/) is probably the best way to do this) or find somebody who can give you an account on a Linux server. 

All the example command lines we'll be looking at use the BLAST example from above – if you want to play along at home [you can download a copy here](https://raw.githubusercontent.com/mojones/random_scripts/master/blast_example.txt). For the command-line examples below, the lines starting with a dollar sign ($) are the ones that we type, and the first few lines of the output is shown below. This will look slightly different on your machine depending on your settings. 

## Getting rid of comments

The first thing we need to do in order to start processing the file is to remove the comment lines at the start. There are seven comment lines, so we need to start looking at the eighth line. The easiest way to do this is with `tail`. The `tail` command is normally used for extracting the last few lines of a file, but with the `-n` option we can tell it to extract all the lines starting from a given line. Here, we want to start at line number eight, so we can use the command
    
    
    
    $ tail -n +8 blast_example.txt
    my_protein	gi|60654447|gb|AAX29914.1|	100.00	100.00	599	0	0	1	599	1	599	0.0	 1243
    my_protein	gi|397526517|ref|XP_003833169.1|	99.50	100.00	599	3	0	1	599	1	599	0.0	 1240
    my_protein	gi|18104967|ref|NP_000953.2|;gi|317373262|sp|P23219.2|PGH1_HUMAN	99.83	99.83	599	1	0	1	599	1	599	0.0	 1240
    my_protein	gi|674214810|gb|AIK97765.1|	99.83	99.83	599	1	0	1	599	1	599	0.0	 1239
    my_protein	gi|694936914|ref|XP_009455498.1|	99.33	100.00	599	4	0	1	599	1	599	0.0	 1239
    my_protein	gi|387018|gb|AAA36439.1|	99.50	99.50	599	3	0	1	599	1	599	0.0	 1236
    

Notice that we have to put a plus sign (+) before the 8. The output of this command will be all the lines of the file from the eighth line onwards, which we can either save by redirecting it into a new file:
    
    
    
    $ tail -n +8 blast_example.txt >blast_example_nocomments.txt
    

or use a pipe to send the output directly to another program:
    
    
    
    $ tail -n +8 blast_example.txt | some_other_program
    

For the rest of the examples we'll use the file _blast_example_nocomments.txt_ so that we don't need to worry about the comment lines.

## Extracting fields

One of the simplest things we can do with tabular data is to just display the fields we're interested in. The command for doing this is called `cut`, and we use the `-f` option to say which field we want to extract. For example, we can extract just the percent identity for each hit, which is stored in the third field:
    
    
    
    $ cut -f 3 blast_example_nocomments.txt
    100.00
    99.50
    99.83
    99.83
    99.33
    99.50
    

To get multiple fields, we just list them separated by commas. Here's how to get the hit name, number of missmatches, and bitscore:
    
    
    
    $ cut -f 2,6,13 blast_example_nocomments.txt
    gi|60654447|gb|AAX29914.1|	0	 1243
    gi|397526517|ref|XP_003833169.1|	3	 1240
    gi|18104967|ref|NP_000953.2|;gi|317373262|sp|P23219.2|PGH1_HUMAN	1	 1240
    gi|674214810|gb|AIK97765.1|	1	 1239
    gi|694936914|ref|XP_009455498.1|	4	 1239
    gi|387018|gb|AAA36439.1|	3	 1236
    gi|76885916|gb|ABA60099.1|	1	 1234
    ...
    

Notice that in the output from this command, the fields don't line up nicely because some of the hit names are longer than others. By piping the results to the `column -t` command we can get the fields to line up:
    
    
    
    $ cut -f 2,6,13 blast_example_nocomments.txt | column -t
    gi|60654447|gb|AAX29914.1|                                        0  1243
    gi|60654447|gb|AAX29914.1|                                        0  1243
    gi|60654447|gb|AAX29914.1|                                        0  1243
    gi|397526517|ref|XP_003833169.1|                                  3  1240
    gi|18104967|ref|NP_000953.2|;gi|317373262|sp|P23219.2|PGH1_HUMAN  1  1240
    gi|674214810|gb|AIK97765.1|                                       1  1239
    gi|674214810|gb|AIK97765.1|                                       1  1239
    gi|694936914|ref|XP_009455498.1|                                  4  1239
    gi|387018|gb|AAA36439.1|                                          3  1236
    gi|76885916|gb|ABA60099.1|                                        1  1234
    

Be careful when using `column -t`, as the fields are effectively padded to the length of the longest value, so if you have a very long value somewhere in the file you'll end up with a lot of whitespace. Note that if you want to use `cut` on a file where the delimiter is something other than a tab, you'll need to use the `-d` option. For example, on a CSV file use
    
    $ cut -d , -f 1,2,3 myfile.txt

## Sorting tabular data

The `sort` command, by default, sorts alphabetically on entire lines, so if we use it without any options it will effectively sort on the query name (if our BLAST report contains multiple queries) and then on the hit name (since that is the second field):
    
    
    
    $ sort blast_example_nocomments.txt
    my_protein	gi|109110319|ref|XP_001088270.1|;gi|544492470|ref|XP_005580931.1|;gi|685591740|ref|XP_009186406.1|	98.33	99.33	599	10	0	1	599	1	599	0.0	 1227
    my_protein	gi|109110321|ref|XP_001088157.1|	98.32	99.33	596	10	0	4	599	37	632	0.0	 1222
    my_protein	gi|130485856|ref|NP_001076150.1|;gi|75039091|sp|O97554.1|PGH1_RABIT;gi|4103591|gb|AAD01796.1|	91.49	95.83	576	49	0	24	599	31	606	0.0	 1121
    my_protein	gi|14278642|pdb|1HT5|A;gi|14278643|pdb|1HT5|B;gi|14278644|pdb|1HT8|A;gi|14278645|pdb|1HT8|B	93.83	97.10	551	34	0	32	582	1	551	0.0	 1093
    my_protein	gi|157835592|pdb|2OYE|P;gi|157835593|pdb|2OYU|P	92.57	95.85	579	43	0	21	599	22	600	0.0	 1130
    ....
    my_protein	gi|76885914|gb|ABA60098.1|	99.83	99.83	596	1	0	4	599	35	630	0.0	 1233
    my_protein	gi|76885916|gb|ABA60099.1|	99.83	99.83	596	1	0	4	599	35	630	0.0	 1234
    my_protein	gi|999675|pdb|1PRH|A;gi|999676|pdb|1PRH|B	93.48	96.92	552	36	0	32	583	1	552	0.0	 1091
    

For our example file, where the hit names begin with GI numbers, this effectively sorts by GI number (though notice that the sorting is not numerical: 76885916 comes before 999675 because it starts with a lower digit, even though it's a higher number).

We can get much more use out of `sort` if we specify the field we want to sort on. To do this we use the `-k` option (short for **key**) and give two numbers (separated by a comma) which are the first and last fields to sort on. This looks a bit weird since most of the time we want to sort on just a single field, so the first and last fields are the same. Here's how we sort by percent identity:
    
    
    
    $ sort -k3,3  blast_example_nocomments.txt
    my_protein	gi|60654447|gb|AAX29914.1|	100.00	100.00	599	0	0	1	599	1	599	0.0	 1243
    my_protein	gi|507558178|ref|XP_004662827.1|	90.62	94.79	576	54	0	24	599	25	600	0.0	 1103
    my_protein	gi|507558180|ref|XP_004662828.1|	90.62	94.79	576	54	0	24	599	25	600	0.0	 1103
    my_protein	gi|507558182|ref|XP_004662829.1|	90.62	94.79	576	54	0	24	599	24	599	0.0	 1102
    my_protein	gi|586470237|ref|XP_006865713.1|	90.62	96.01	576	54	0	24	599	25	600	0.0	 1094
    ...
    my_protein	gi|674214810|gb|AIK97765.1|	99.83	99.83	599	1	0	1	599	1	599	0.0	 1239
    my_protein	gi|76885914|gb|ABA60098.1|	99.83	99.83	596	1	0	4	599	35	630	0.0	 1233
    my_protein	gi|76885916|gb|ABA60099.1|	99.83	99.83	596	1	0	4	599	35	630	0.0	 1234
    

Percent identity is the third field, so the first and last fields to sort on are both three. Looking closely at the output above reveals something odd: the first line, where the percent identity is 100, should be last, but instead it is first. The reason for this is that we're still sorting alphabetically, so 100 comes before 99 because it starts with a 1. To switch to numerical sorting, we use the  `-n` option:
    
    
    
    $ sort -k3,3  -n blast_example_nocomments.txt
    my_protein	gi|507558178|ref|XP_004662827.1|	90.62	94.79	576	54	0	24	599	25	600	0.0	 1103
    my_protein	gi|507558180|ref|XP_004662828.1|	90.62	94.79	576	54	0	24	599	25	600	0.0	 1103
    my_protein	gi|507558182|ref|XP_004662829.1|	90.62	94.79	576	54	0	24	599	24	599	0.0	 1102
    ...
    my_protein	gi|76885914|gb|ABA60098.1|	99.83	99.83	596	1	0	4	599	35	630	0.0	 1233
    my_protein	gi|76885916|gb|ABA60099.1|	99.83	99.83	596	1	0	4	599	35	630	0.0	 1234
    my_protein	gi|60654447|gb|AAX29914.1|	100.00	100.00	599	0	0	1	599	1	599	0.0	 1243
    

and the hit with 100% identity moves to its correct place at the end of the output. If we want to see the highest identity first – i.e. to reverse the sorting order – we can just use the `-r` option:
    
    
    
    $ sort -k3,3  -n -r blast_example_nocomments.txt
    my_protein	gi|60654447|gb|AAX29914.1|	100.00	100.00	599	0	0	1	599	1	599	0.0	 1243
    my_protein	gi|76885916|gb|ABA60099.1|	99.83	99.83	596	1	0	4	599	35	630	0.0	 1234
    my_protein	gi|76885914|gb|ABA60098.1|	99.83	99.83	596	1	0	4	599	35	630	0.0	 1233
    ...
    my_protein	gi|507558182|ref|XP_004662829.1|	90.62	94.79	576	54	0	24	599	24	599	0.0	 1102
    my_protein	gi|507558180|ref|XP_004662828.1|	90.62	94.79	576	54	0	24	599	25	600	0.0	 1103
    my_protein	gi|507558178|ref|XP_004662827.1|	90.62	94.79	576	54	0	24	599	25	600	0.0	 1103
    

If we want to sort on multiple fields we can use multiple **-k** options. For example, here's how we sort by percent identity (field number three) then by hit length (field number five):
    
    
    
    $ sort -k3,3  -k5,5 -n -r blast_example_nocomments.txt
    

## Head

Often when we sort BLAST hits by some criterion we're interested in the first or last hits. For example, we might only want to see the five longest hits. To do this, we can take the output of `sort` and pipe it directly into `head`. The `head` command displays just the few lines of its input – by default, the first ten lines, though we can specify a different number with the `-n` option. Here's how we extract the five longest hits from our BLAST report – we `sort` by hit length (the fifth field) in reverse order then pipe the results into `head` and extract the first 5 lines:
    
    
    
    $ sort -k5,5 -n -r blast_example_nocomments.txt | head -n 5
    my_protein	gi|724957135|ref|XP_010353695.1|	98.33	98.83	599	10	0	1	599	1	599	0.0	 1224
    my_protein	gi|694936914|ref|XP_009455498.1|	99.33	100.00	599	4	0	1	599	1	599	0.0	 1239
    my_protein	gi|674214810|gb|AIK97765.1|	        99.83	99.83	599	1	0	1	599	1	599	0.0	 1239
    my_protein	gi|635071455|ref|XP_007966350.1|	98.33	99.33	599	10	0	1	599	60	658	0.0	 1225
    my_protein	gi|60654447|gb|AAX29914.1|	       100.00	100.00	599	0	0	1	599	1	599	0.0	 1243
    

If we combine this with the `cut` command that we discussed earlier, we can do more sophisticated things. For example, we can look at just the hit names and bitscores for the five longest hits by piping the results of the above command into `cut` and extracting the second and thirteenth fields:
    
    
    
    $ sort -k5,5 -n -r blast_example_nocomments.txt | head -n 5 | cut -f 2,13
    gi|724957135|ref|XP_010353695.1|	 1224
    gi|694936914|ref|XP_009455498.1|	 1239
    gi|674214810|gb|AIK97765.1|	         1239
    gi|635071455|ref|XP_007966350.1|	 1225
    gi|60654447|gb|AAX29914.1|	         1243
    

This is an interesting example because the field that we're using for sorting – the hit length – doesn't form part of the final output. Because of this, we have to be careful with the order of commands – the `sort` has to come before the `cut`. If we try and do it the other way round:
    
    
    
    $ cut -f 2,13 | sort -k5,5 -n -r blast_example_nocomments.txt | head -n 5
    

it won't work, because by the time `sort` sees the data the hit length field is no longer there – it's been removed by `cut`.

## Filtering with awk

The last example we saw involved grabbing the five longest hits; what if, instead, we wanted to grab _all_ the hits that were longer than a given length? We can't use the `sort`/`head` approach outlined above, because we don't know in advance how many lines we want to display. Instead, we need a tool that's capable of filtering lines based on specific criteria, and the one we're going to use is called `awk`. The `awk` command is a very flexible tool for text manipulation and can do lots of things; here we're only going to use a tiny bit of its capability. To use `awk` for filtering, we run it with a command string that describes the criterion which lines must pass. Inside this string, we can use `$1` to refer to the first field, `$2` to refer to the second, and so on. We can also use mathematical comparisons like equals and less-than. A few examples will make it clearer – here's how we display just the hits that are longer than 590 bases (i.e. just the lines where the fifth field is greater than 590):
    
    
    
    $ awk '$5 > 590' blast_example_nocomments.txt
    my_protein	gi|60654447|gb|AAX29914.1|	100.00	100.00	599	0	0	1	599	1	599	0.0	 1243
    my_protein	gi|397526517|ref|XP_003833169.1|	99.50	100.00	599	3	0	1	599	1	599	0.0	 1240
    my_protein	gi|18104967|ref|NP_000953.2|;gi|317373262|sp|P23219.2|PGH1_HUMAN	99.83	99.83	599	1	0	1	599	1	599	0.0	 1240
    my_protein	gi|674214810|gb|AIK97765.1|	99.83	99.83	599	1	0	1	599	1	599	0.0	 1239
    my_protein	gi|694936914|ref|XP_009455498.1|	99.33	100.00	599	4	0	1	599	1	599	0.0	 1239
    my_protein	gi|387018|gb|AAA36439.1|	99.50	99.50	599	3	0	1	599	1	599	0.0	 1236
    my_protein	gi|76885916|gb|ABA60099.1|	99.83	99.83	596	1	0	4	599	35	630	0.0	 1234
    my_protein	gi|76885914|gb|ABA60098.1|	99.83	99.83	596	1	0	4	599	35	630	0.0	 1233
    my_protein	gi|109110319|ref|XP_001088270.1|;gi|544492470|ref|XP_005580931.1|;gi|685591740|ref|XP_009186406.1|	98.33	99.33	599	10	0	1	599	1	599	0.0	 1227
    my_protein	gi|635071455|ref|XP_007966350.1|	98.33	99.33	599	10	0	1	599	60	658	0.0	 1225
    my_protein	gi|724957135|ref|XP_010353695.1|	98.33	98.83	599	10	0	1	599	1	599	0.0	 1224
    my_protein	gi|109110321|ref|XP_001088157.1|	98.32	99.33	596	10	0	4	599	37	632	0.0	 1222
    my_protein	gi|544492468|ref|XP_005580930.1|;gi|355567462|gb|EHH23803.1|;gi|355753051|gb|EHH57097.1|	98.32	99.33	596	10	0	4	599	37	632	0.0	 1221
    my_protein	gi|403266039|ref|XP_003925205.1|	97.16	98.50	599	17	0	1	599	1	599	0.0	 1209
    my_protein	gi|667269156|ref|XP_008570258.1|	92.80	95.98	597	43	0	3	599	35	631	0.0	 1123
    my_protein	gi|470608129|ref|XP_004314816.1|	90.95	94.81	597	54	0	3	599	4	600	0.0	 1122
    my_protein	gi|403310639|ref|NP_001258093.1|	91.82	91.82	599	1	1	1	599	1	551	0.0	 1115
    my_protein	gi|194033503|ref|XP_001926164.1|	91.12	95.48	597	53	0	3	599	4	600	0.0	 1102
    

Here's how we display just the hits that include the very start of the query sequence (i.e. the lines where the eighth field is equal to one):
    
    
    
    $ awk '$8 == 1' blast_example_nocomments.txt
    my_protein	gi|60654447|gb|AAX29914.1|	100.00	100.00	599	0	0	1	599	1	599	0.0	 1243
    my_protein	gi|397526517|ref|XP_003833169.1|	99.50	100.00	599	3	0	1	599	1	599	0.0	 1240
    my_protein	gi|18104967|ref|NP_000953.2|;gi|317373262|sp|P23219.2|PGH1_HUMAN	99.83	99.83	599	1	0	1	599	1	599	0.0	 1240
    my_protein	gi|674214810|gb|AIK97765.1|	99.83	99.83	599	1	0	1	599	1	599	0.0	 1239
    my_protein	gi|694936914|ref|XP_009455498.1|	99.33	100.00	599	4	0	1	599	1	599	0.0	 1239
    my_protein	gi|387018|gb|AAA36439.1|	99.50	99.50	599	3	0	1	599	1	599	0.0	 1236
    my_protein	gi|109110319|ref|XP_001088270.1|;gi|544492470|ref|XP_005580931.1|;gi|685591740|ref|XP_009186406.1|	98.33	99.33	599	10	0	1	599	1	599	0.0	 1227
    my_protein	gi|635071455|ref|XP_007966350.1|	98.33	99.33	599	10	0	1	599	60	658	0.0	 1225
    my_protein	gi|724957135|ref|XP_010353695.1|	98.33	98.83	599	10	0	1	599	1	599	0.0	 1224
    my_protein	gi|403266039|ref|XP_003925205.1|	97.16	98.50	599	17	0	1	599	1	599	0.0	 1209
    my_protein	gi|403310639|ref|NP_001258093.1|	91.82	91.82	599	1	1	1	599	1	551	0.0	 1115
    

Here's how we display just the hits with fewer than four missmatches (i.e. the lines where the sixth field is less than four):
    
    
    
    $ awk '$6 < 4' blast_example_nocomments.txt
    my_protein	gi|60654447|gb|AAX29914.1|	100.00	100.00	599	0	0	1	599	1	599	0.0	 1243
    my_protein	gi|397526517|ref|XP_003833169.1|	99.50	100.00	599	3	0	1	599	1	599	0.0	 1240
    my_protein	gi|18104967|ref|NP_000953.2|;gi|317373262|sp|P23219.2|PGH1_HUMAN	99.83	99.83	599	1	0	1	599	1	599	0.0	 1240
    my_protein	gi|674214810|gb|AIK97765.1|	99.83	99.83	599	1	0	1	599	1	599	0.0	 1239
    my_protein	gi|387018|gb|AAA36439.1|	99.50	99.50	599	3	0	1	599	1	599	0.0	 1236
    my_protein	gi|76885916|gb|ABA60099.1|	99.83	99.83	596	1	0	4	599	35	630	0.0	 1234
    my_protein	gi|76885914|gb|ABA60098.1|	99.83	99.83	596	1	0	4	599	35	630	0.0	 1233
    my_protein	gi|530391358|ref|XP_005252162.1|	99.47	99.65	571	3	0	29	599	4	574	0.0	 1186
    my_protein	gi|403310639|ref|NP_001258093.1|	91.82	91.82	599	1	1	1	599	1	551	0.0	 1115
    

You get the idea. We can combine multiple criteria with `&&` – here's how we display just the hits with fewer than four miss-matches whose length is less than 599 bases:
    
    
    
    $ awk '$6 < 4 && $5 < 599' blast_example_nocomments.txt
    my_protein	gi|76885916|gb|ABA60099.1|	99.83	99.83	596	1	0	4	599	35	630	0.0	 1234
    my_protein	gi|76885914|gb|ABA60098.1|	99.83	99.83	596	1	0	4	599	35	630	0.0	 1233
    my_protein	gi|530391358|ref|XP_005252162.1|	99.47	99.65	571	3	0	29	599	4	574	0.0	 1186
    

Unsurprisingly, combining `awk` with the other tools allows us to ask even more specific questions. For example, what are the bitscores (field number 13) of the hits with at least 99% identical positions (field number 3)? To get the answer, we filter the lines using `awk` then pipe the output to `cut` to extract just the fields we want:
    
    
     $ awk '$3 >= 99' blast_example_nocomments.txt | cut -f 13
     1243
     1240
     1240
     1239
     1239
     1236
     1234
     1233
     1186
    

## Summarizing output

All of the examples we've seen so far result in multiple lines of output, but sometimes we want to summarize the output. Consider a variation on the example above: how many hits have at least 99% identical bases? We know how to use `awk` to filter out just the hits that satisfy the criterion, so all we need to do is count the number of lines of output we get. The `wc` tool (the name is short for word count) will do this if we use the `-l` (for lines) option:
    
    
    
    $ awk '$3 >= 99' blast_example_nocomments.txt | wc -l
    9
    

So that rather than getting back a list, we now get back a single number. Another way to summarize output is to take the average of a list of numbers – for example, what's the average bitscore of all hits with at least 99% identity? A combination of `awk` and `cut` will give us the bitscores:
    
    
    
    $ awk '$3 >= 99' blast_example_nocomments.txt | cut -f 13
     1243
     1240
     1240
     1239
     1239
     1236
     1234
     1233
     1186
    

and in fact, `awk` is also capable of calculating the mean average. The command that does so is `'{a+=$13} END{print a/NR}'`, which roughly translates as "for each line, add the thirteenth field to a variable called `a`, then at the end of the file divide `a` by the number of lines and print it". Any further explanation of this command string would require a massive diversion into the syntax of `awk`, so we'll just treat it as a magic string for now and look at how it's used:
    
    
    
    $ awk '$3 >= 99' blast_example_nocomments.txt | awk '{a+=$13} END{print a/NR}'
    1232.22
    

To calculate the average for a different field, we just replace `$13` with something else. Notice that in the above example we're using `awk` twice: once to filter out the lines and once to calculate the average. We could do the whole thing in a single `awk` command:
    
    
    
    $ awk '$3 >= 99  {a+=$13;b+=1} END{print a/b}' blast_example_nocomments.txt
    1232.22
    

but it's harder to read and would take even more space to explain. 

An interesting feature of BLAST reports is that we can (depending on the settings) potentially have multiple hits to a single subject sequence, which will show up as multiple lines with identical second columns. To turn a list with duplicates into a unique list, we use the `uniq` command. For example, to get a list of all unique subject sequence names:
    
    
    
    $ cut -f 2 blast_example_nocomments.txt | uniq 
    gi|60654447|gb|AAX29914.1|
    gi|397526517|ref|XP_003833169.1|
    gi|18104967|ref|NP_000953.2|;gi|317373262|sp|P23219.2|PGH1_HUMAN
    gi|674214810|gb|AIK97765.1|
    ...
    gi|14278642|pdb|1HT5|A;gi|14278643|pdb|1HT5|B;gi|14278644|pdb|1HT8|A;gi|14278645|pdb|1HT8|B
    gi|999675|pdb|1PRH|A;gi|999676|pdb|1PRH|B
    gi|507932370|ref|XP_004677426.1|
    gi|7245654|pdb|1EBV|A
    

If we compare the number of lines with and without `uniq`, we can get see how many subject names are duplicates:
    
    
    
    $ cut -f 2 blast_example_nocomments.txt | wc -l
    102
    $ cut -f 2 blast_example_nocomments.txt | uniq | wc -l
    98
    

In this case we get 102 lines but only 98 unique lines, so we know that 4 are duplicated. But we have no way of knowing how the duplicates are distributed; do we have a single subject sequence with five hits (resulting in four duplicates) or four subject sequences each with two hits (also resulting in four duplicates)? To find out, we can add the `-c` option to `uniq`, which will prefix each line with the number of times it occurs:
    
    
    
    $ cut -f 2 blast_example_nocomments.txt | uniq -c
          3 gi|60654447|gb|AAX29914.1|
          1 gi|397526517|ref|XP_003833169.1|
          1 gi|18104967|ref|NP_000953.2|;gi|317373262|sp|P23219.2|PGH1_HUMAN
          2 gi|674214810|gb|AIK97765.1|
          1 gi|694936914|ref|XP_009455498.1|
          ...
          1 gi|471370351|ref|XP_004375688.1|
          2 gi|545512378|ref|XP_005625066.1|
          ...
          1 gi|507932370|ref|XP_004677426.1|
          1 gi|7245654|pdb|1EBV|A
    

An important limitation of `uniq` is that it only identifies duplicated lines that are adjacent in the file. This is fine in the case of subject sequence names, because BLAST groups multiple hits to the same sequence together, but if we try the same trick with a field where duplicated values are spread throughout the file it won't work. For example, we might want to see how many hits there are of each length (remember, alignment length is the fifth field), but if we try doing this:
    
    
    
    $ cut -f 5 blast_example_nocomments.txt | uniq -c
          1 599
          2 123
          3 599
          1 123
          2 599
          ...
    

we can see that the lines containing 599 have not been correctly grouped together because they are not adjacent. To make this command work we need to `sort` the alignment lengths numerically before passing them to `uniq`:
    
    
    
    $ cut -f 5 blast_example_nocomments.txt | sort -n | uniq -c
          3 123
          1 309
          2 551
          1 552
          2 553
          1 567
         12 568
          1 569
          2 570
          3 571
          1 573
          5 574
          1 575
         35 576
          9 579
          1 580
          4 582
          4 596
          3 597
         11 599
    

Sorting the lengths in this way not only gives us the correct counts, but also makes it easier to interpret the results. 

## Editing fields

In the last section of this tutorial we'll look at a few different ways that we can make changes to the fields in a tabular data file. Let's start with our old friend `awk`; we already know that inside an `awk` command we can use `$3`, for example, to get the value of the third field. We can also use the same notation to set the value of the third field – say, to a specific number:
    
    
    
    $ awk '$3=123456789' blast_example_nocomments.txt 
    my_protein gi|60654447|gb|AAX29914.1| 123456789 100.00 599 0 0 1 599 1 599 0.0 1243
    my_protein gi|60654447|gb|AAX29914.1| 123456789 100.00 123 0 0 1 599 602 866 0.0 1243
    my_protein gi|60654447|gb|AAX29914.1| 123456789 100.00 123 0 0 1 599 702 904 0.0 1243
    my_protein gi|397526517|ref|XP_003833169.1| 123456789 100.00 599 3 0 1 599 1 599 0.0 1240
    ...
    

This by itself is not very useful – let's look at a more realistic example. Imagine that we want to express the proportion of identical bases for each hit as a decimal fraction rather than as a percentage. To accomplish this, we use the `awk` command `'$3=$3/100'` i.e. "set the new value of the third field to be the current value of the third field divided by one hundred":
    
    
    
    $ awk '$3=$3/100' blast_example_nocomments.txt 
    my_protein gi|60654447|gb|AAX29914.1| 1 100.00 599 0 0 1 599 1 599 0.0 1243
    my_protein gi|60654447|gb|AAX29914.1| 1 100.00 123 0 0 1 599 602 866 0.0 1243
    my_protein gi|60654447|gb|AAX29914.1| 1 100.00 123 0 0 1 599 702 904 0.0 1243
    my_protein gi|397526517|ref|XP_003833169.1| 0.995 100.00 599 3 0 1 599 1 599 0.0 1240
    my_protein gi|18104967|ref|NP_000953.2|;gi|317373262|sp|P23219.2|PGH1_HUMAN 0.9983 99.83 599 1 0 1 599 1 599 0.0 1240
    my_protein gi|674214810|gb|AIK97765.1| 0.9983 99.83 599 1 0 1 599 1 599 0.0 1239
    my_protein gi|674214810|gb|AIK97765.1| 0.9983 99.83 123 1 0 1 599 600 700 0.0 1239
    my_protein gi|694936914|ref|XP_009455498.1| 0.9933 100.00 599 4 0 1 599 1 599 0.0 1239
    my_protein gi|387018|gb|AAA36439.1| 0.995 99.50 599 3 0 1 599 1 599 0.0 1236
    ...
    

You'll notice that in the output shown above, `awk` has separated each field with a space rather than a tab as they were originally. Depending on what we want to do with the data at this point, this may not be a problem, but if we want to get the original tab separator back we just have to set the `-OFS` (short for **Output Field Separator**) option to the tab character, which we represent as `'t'`:
    
    
    
    $ awk  '$3=$3/100' OFS='t' blast_example_nocomments.txt 
    my_protein	gi|60654447|gb|AAX29914.1|	1	100.00	599	0	0	1	599	1	599	0.0	1243
    my_protein	gi|60654447|gb|AAX29914.1|	1	100.00	123	0	0	1	599	602	866	0.0	1243
    my_protein	gi|60654447|gb|AAX29914.1|	1	100.00	123	0	0	1	599	702	904	0.0	1243
    my_protein	gi|397526517|ref|XP_003833169.1|	0.995	100.00	599	3	0	1	599	1	599	0.0	1240
    my_protein	gi|18104967|ref|NP_000953.2|;gi|317373262|sp|P23219.2|PGH1_HUMAN	0.9983	99.83	599	1	0	1	599	1	599	0.0	1240
    my_protein	gi|674214810|gb|AIK97765.1|	0.9983	99.83	599	1	0	1	599	1	599	0.0	1239
    my_protein	gi|674214810|gb|AIK97765.1|	0.9983	99.83	123	1	0	1	599	600	700	0.0	1239
    ...
    

If we want to make multiple edits to each line, we simply separate them with a comma. Imagine that we have want to take our list of hits to a protein sequence and translate the start/stop positions to the corresponding DNA sequence by multiplying them both by three. Here's how we do it (remember that the query start and stop are fields number 8 and 9):
    
    
    
    $ awk  '$8=$8*3,$9=$9*3' OFS='t' blast_example_nocomments.txt 
    my_protein	gi|60654447|gb|AAX29914.1|	100.00	100.00	599	0	0	3	1797	1	599	0.0	1243
    my_protein	gi|60654447|gb|AAX29914.1|	100.00	100.00	123	0	0	3	1797	602	866	0.0	1243
    my_protein	gi|60654447|gb|AAX29914.1|	100.00	100.00	123	0	0	3	1797	702	904	0.0	1243
    my_protein	gi|397526517|ref|XP_003833169.1|	99.50	100.00	599	3	0	3	1797	1	599	0.0	1240
    ...
    

Notice that the command for multiplication is an asterisk (`*`), not an x. 

Finally, what if we want to alter just a part of a field, rather than the whole field? For example, all those bar characters (`|`) in the subject hit names make them difficult to read, so we might want to change them to spaces. We could do this job with `awk`, but the `sed` tool is a bit easier to use in this case. When using `sed` to replace one character with another, we give it a command string that looks like this: `'s/X/Y/g'`, where `X` is the character we want to change and **Y** is the replacement. In our example, we want to change all bar characters to spaces:
    
    
    
    $ sed 's/|/ /g' blast_example_nocomments.txt

    my_protein	gi 60654447 gb AAX29914.1 	100.00	100.00	599	0	0	1	599	1	599	0.0	 1243
    my_protein	gi 60654447 gb AAX29914.1 	100.00	100.00	123	0	0	1	599	602	866	0.0	 1243
    my_protein	gi 60654447 gb AAX29914.1 	100.00	100.00	123	0	0	1	599	702	904	0.0	 1243
    my_protein	gi 397526517 ref XP_003833169.1 	99.50	100.00	599	3	0	1	599	1	599	0.0	 1240
    

It's important to note that these last few examples of editing fields do not alter the original file – if we want to save the ouput then we need to redirect it to a new file. 

## Doing more complicated things

The examples in this tutorial just give a very brief overview of the capabilities of command-line tools to deal with tabular data. Each of the tools mentioned here – especially `sed` and `awk` – have many more options and are capable of doing much more complicated jobs. However, as command-lines and scripts get larger, they become harder to edit and maintain. 

For any job involving tabular data that's more complex than the examples outlined above, it's probably best to switch to a "real" programming language. Head over to the books page, or take a look at the tutorial.

  
