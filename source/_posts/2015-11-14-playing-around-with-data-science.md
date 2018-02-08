---
layout: post
title: "Playing around with Data Science"
date: 2015-10-26 13:21:45 +0800
categories: data-science
keywords: "R, data-science"
---
After a few influences here and there from colleagues, and how I kept hearing this buzzword time and time again. I am finally curious to understand what is data science all about.

I started with an online course from [Coursera](https://www.coursera.org/user/i/394c3bbb467e444d4cb349ad6217d8e6) in my spare time as an introduction, then I’m currently in the process of finishing the R programming course at the end of October.

Overall, as a long time programmer, I consider the level of difficulty of the R programming assignments as easy. You don’t need to spend days to solve one of them. Mostly, it can be done in an hour or more within a day. Moreover, the course team are very generous in providing examples, and all of the lecture materials are very organized.

Though, it’s likely to get harder if I proceed to the next course in the data science specialization track. Which, I probably will. But before that, I’ve accidentally discovered [data science on the command line](http://datascienceatthecommandline.com/) by [Jeroen Janssens](https://github.com/jeroenjanssens) which introduced a set of [toolbox](http://datasciencetoolbox.org/) that I perceived as a more complete tooling than just utilizing R.

I generally believe that programming toolsets should not be confined to one environment, one editor, with their own custom interfaces or protocols written by a vendor that is not extensible or interoperable with another toolset. Although this is not the case with R, but I have seen other programming tools — which I can’t recall right now — that are built this way.

What about Python for data science? Yes, Python is a general purpose programming language, but I assume that the data science community are more productive within the R programming environment. Proven by how Johns Hopkins University are using R to teach data science. It would take more learning and time for those with little programming experience to solve these problems using Python.

The data scientists have done a lot for the R development tools. I have searched and peeked a bit into a few of the stuff that they had built. Though, honestly I haven’t explored what kind of tools or libraries in Python that is made for data science.

So, I’m thinking along the lines of using what is already been done on R, and we should be able to interface with it from a different programming environment.

For example:
An interactive application (desktop, web, mobile, whatever) that continuosly records data from user input, or other sources which is then formatted into CSV or data frames which is the data type that R usually interacts with.
These data should be fed into R programs that are being specifically written to do a unit of calculation. The raw data of the result should be sent back to the application which are optimized for user interface and visualization purpose.

We need this interfacing because I’m not sure that R tools (even though it has Shiny and other plotting libraries) are complete or well developed enough to be able to do advanced UI or visualization, it’s not their focus.
Regardless, it’s still very early for me to tell whether it’s even better to remove R from this sort of system. Because it does seem like it’s just a tool for data scientist, it’s not something that you’ll use for an automated stand alone performance critical system that will process and compute huge amounts of data.
