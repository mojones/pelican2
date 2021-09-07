Title: Linux and workflows for biologists
Date: 2010-12-03 10:20
Modified: 2010-12-05 19:30
Category: books
Tags: python, tutorial
slug: linux-workflows
Authors: Martin
Template: plain

# Linux and workflows for biologists

This course introduces the Linux operating system, and takes students from the very basics of using the command line to advanced techniques like shell scripting and analysing tabular data.

*"I thought the workshop was fantastic. I really enjoyed it and I learned a lot. It was extremely well run."*


## Upcoming courses

None at the moment; watch this space or drop me an email if you'd like to run this course at your institution!


## Overview

Most high-throughput bioinformatics work these days takes place on the Linux command line. The programs which do the majority of the computational heavy lifting — genome assemblers, read mappers, and annotation tools — are designed to work best when used with a command-line interface. Because the command line can be an intimidating environment, many biologists learn the bare minimum needed to get their analysis tools working. This means that they miss out on the power of Linux to customise their environment and automate many parts of the bioinformatics workflow. This course will introduce the Linux command line environment from scratch and teach students how to make the most of its tools to achieve a high level of productivity when working with biological data. 

### Detailed syllabus

#### 1\. The design of Linux

In the first session we briefly cover the design of Linux: how is it different from Windows/OSX and how is it best used? We'll then jump straight onto the command line and learn about the layout of the Linux filesystem and how to navigate it. We'll describe Linux's file permission system (which often trips up beginners), how paths work, and how we actually run programs on the command line. We'll learn a few tricks for using the command line more efficiently, and how to deal with programs that are misbehaving. We'll finish this session by looking at the built in help system and how to read and interpret manual pages.

#### 2\. System management

We'll first look at a few command line tools for monitoring the status of the system and keeping track of what's happening to processor power, memory, and disk space. We'll go over the process of installing new software from the built in repositories (which is easy) and from source code downloads (which is trickier). We'll also introduce some tools for benchmarking software (measuring the time/memory requirements of processing large datasets).

#### 3\. Manipulating tabular data

Many data types we want to work with in bioinformatics are stored as tabular plain text files, and here we learn all about manipulating tabular data on the command line. We'll start with simple things like extracting columns, filtering and sorting, searching for text before moving on to more complex tasks like searching for duplicated values, summarizing large files, and combining simple tools into long commands.

#### 4\. Constructing pipelines

In this session we will look at the various tools Linux has for constructing pipelines out of individual commands. Aliases, shell redirection, pipes, and shell scripting will all be introduced here. We'll also look at a couple of specific tools to help with running tools on multiple processors, and for monitoring the progress of long running tasks.

#### 5\. EMBOSS

EMBOSS is a suite of bioinformatics command-line tools explicitly designed to work in the Linux paradigm. We'll get an overview of the different sequence data formats that we might expect to work with, and put what we learned about shell scripting to biological use by building a pipeline to compare codon usage across two collections of DNA sequences.

#### 6\. Using a Linux server

Often in bioinformatics we'll be working on a Linux server rather than our own computer— typically because we need access to more computing power, or to specialized tools and datasets. In this session we'll learn how to connect to a Linux server and how to manage sessions. We'll also consider the various ways of moving data to and from a server from your own computer, and finish with a discussion of the considerations we have to make when working on a shared computer.

#### 7\. Combining methods

In the next two sessions — i.e. one full day — we'll put everything we have learned together and implement a workflow for next-gen sequence analysis. In this first session we'll carry out quality control on some paired-end Illumina data and map these reads to a reference genome. We'll then look at various approaches to automating this pipeline, allowing us to quickly do the same for a second dataset.

#### 8\. Combining methods

The second part of the next-gen workflow is to call variants to identify SNPs between our two samples and the reference genome. We'll look at the VCF file format and figure out how to filter SNPs for read coverage and quality. By counting the number of SNPs between each sample and the reference we will try to figure out something about the biology of the two samples. We'll attempt to automate this analysis in various ways so that we could easily repeat the pipeline for additional samples.

#### 9\. Customisation

Part of the Linux design is that everything can be customised. This can be intimidating at first but, given that bioinformatics work is often fairly repetitive, can be used to good effect. Here we'll learn about environment variables, custom prompts, soft links, and ssh configuration — a collection of tools with modest capabilities, but which together can make life on the command line much more pleasant. In this last session there will also be time to continue working on the next-gen sequencing pipeline.  

The last afternoon is reserved for finishing off the next-gen workflow exercise, working on your own datasets, or leaving early for travel.
