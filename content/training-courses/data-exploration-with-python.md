Title: Data exploration with Python
Date: 2010-12-03 10:20
Modified: 2010-12-05 19:30
Category: books
Tags: python, tutorial
slug: data-exploration-with-python
Authors: Martin
Template: plain


# Data exploration with Python

This course focusses on using Python's scientific software libraries to manipulate and visualise large datasets. It's intended for researchers with a basic knowledge of Python who need to explore large datasets and quickly visualise patterns and relationships. 

*"Martin was very good at both teaching and helping with customising/practicing on our own dataset." (September 2016)*

## Upcoming courses
None currently, watch this space or drop me an email if you'd like to host one!

## Overview

One of the strengths of the Python language is the availability of mature, high-quality libraries for working with scientific data. Integration between the most popular libraries has lead to the concept of a "scientific Python stack": a collection of packages which are designed to work well together. In this workshop we will see how to leverage these libraries to efficiently work with and visualise large volumes of data.

### Detailed syllabus

#### 1\. Introduction and datasets

Jupyter (formerly iPython) is a programming environment that is rapidly becoming the de facto standard for scientific data analysis. In this session we'll learn why Jupyter is so useful, covering its ability to mix notes and code, to render inline plots, charts and tables, to use custom styles and to create polished web pages. We'll also take a look at the datasets that we'll be investigating during the course and discuss the different types of data we encounter in bioinformatics work.

#### 2\. Introduction to pandas

In this session we introduce the first part of the scientific Python stack: the pandas data manipulation package. We'll learn about Dataframes — the core data structure that much of the rest of the course will rely on — and how they allow us to quickly select, sort, filter and summarize large datasets. We'll also see how to extend existing Dataframes by writing functions to create new columns, as well as how to deal with common problems like missing or inconsistent values in datasets. We'll get our first look at data visualisation by using pandas' built in plotting ability to investigate basic properties of our datasets. 

#### 3\. Grouping and pivoting with pandas

This session continues our look at pandas with advanced uses of Dataframes that allow us to answer more complicated questions. We'll look two very powerful tools: grouping, which allows us to aggregate information in datasets, and pivoting/stacking, which allows us to flexibly rearrange data (a key step in preparing datasets for visualisation). In this session we'll also go into more detail about pandas indexing system.

#### 4\. Advanced manipulation with pandas

In this final session on the pandas library we'll look at a few common types of data manipulation — binning data (very useful for working with time series), carrying out principal component analysis, and creating networks. We'll also cover some features of pandas designed for working with specific types of data like timestamps and ordered categories.

#### 5\. Introduction to seaborn

This session introduces the seaborn charting library by showing how we can use it to investigate relationships between different variables in our datasets. Initially we concentrate on showing distributions with histograms, scatter plots and regressions, as well as a few more exotic chart types like hexbins and KDE plots. We also cover heatmaps, in particular looking at how they lend themselves to displaying the type of aggregate data that we can generate with pandas.

#### 6\. Categories in seaborn

This session is devoted to seaborn's primary use case: visualising relationships across multiple categories in complex datasets. We see how we can use colour and shape to distinguish categories in single plots, and how these features work together with the pandas tools we have already seen to allow us to very quickly explore a dataset. We continue by using seaborn to build small multiple or facet plots, separating categories by rows and columns. Finally, we look at chart types that are designed to show distributions across categories: box and violin plots, and the more exotic swarm and strip plots.

#### 7\. Customisation with seaborn

For the final session on seaborn, we go over some common types of customisation that can be tricky. To achieve very fine control over the style and layout of our plots, we'll learn how to work directly with axes and chart objects to implement things like custom heatmap labels, log axis scales, and sorted categories.

#### 8\. Matplotlib

In the final teaching session, we look at the library that both pandas and seaborn rely on for their charting tools: matplotlib. We'll see how by using matplotlib directly we can do things that would be impossible in pandas or seaborn, such as adding custom annotations to our charts. We'll also look at using matplotlib to build completely new, custom visualisation by combining primitive shapes.

#### 9\. / 10. Data workshop

The two sessions on the final day are set aside for a data workshop. Students can practice applying the tools they've learned to their own datasets with the help of an instructor, or continue to work on exercises from the previous day. There may also be time for some demonstrations of topics of particular interest, such as interactive visualisation tools and animations.
