# Geog 418 Fall 2024 - Assignment 1
All assignments for this course will be completed in the coding language R. There are two software that you will need to install: (1) [R version 4.4.1](https://www.r-project.org/), and (2) [R Studio version 2024.04.2](https://posit.co/download/rstudio-desktop/). R is an open-source programming language fpr statistical analysis, while R Studio is the graphical user interface that provides you with editing and visualization capabilities. You can visit the video in the Read Me First Page on the course Brightspace site to learn how to download these software. 

R operates by running through lines of code that a user (e.g. you) creates. Like all languages, R is sensitive to spelling, upper/lower cases, and punctuation, so it is best to approach your coding in a slow and deliberate manner to ensure that you are not making any mistakes. In addition to code, you can enter comments in R that serve as plain text for explaining what the code is suppose to do.

There are really only three basic elements to coding. 
1. objects: this is the thing that you are doing something to with your code. It could be a number, a name, or a dataset (amongst other things)
2. variables: each object can have multiple variables that describe something about it. For example, if the object is a list of trees, the variable could be height
3. functions: a function is applied to do something to an object or a variable. For example, the function "mean" would return the average height of all the trees in your list. Functions can be short and simple or they can be very long and confusing. Sometimes we will ignore some of the elements of a function and just focus on the essentials.

Whatever your experience is with coding, please have the confidence that will learn some new skills and will walk away from this course with a new or polished tool in your analytical toolbelt. You can do it, we can help. Let's get started with Assignment 1.


## Instructions
Open R Studio and create a new project and give it an appropriate name (e.g. Geog418_Assignment_1). You should see multiple panels in the interface. The top-left panel is where you create and save your code. When you run your code you will see it being executed in the bottom-left panel. Any outputs are shown in the Plots panel on the right.

R has many generic functions that are built into its main download "package". However, most often you will want to use a specific function that is part of some specific package that does not come with the main download, and so you will need to install these packages onto your machine. Here is a list of packages that you will need to install for this assignment. Copy and paste this code into your R Studio editor panel. Highlight the code and select "Run"

```
install.packages("terra")
install.packages("lubridate")
install.packages("e1071")
install.packages("gridExtra")
install.packages("gtable")
install.packages("grid")
install.packages("ggplot2")
install.packages("dplyr")
install.packages("bcmaps")
install.packages("tmap")
install.packages("sf")
```
You will see all the packages being installed in the execution panel. Note that you will only need to install packages once on your machine.

Next, you will need to activate these packages by calling them into a "library". This step will need to be done each time you open R for your assignment. 
```
library("lubridate")
library("e1071")
library("gtable")
library("grid")
library("gridExtra")
library("ggplot2")
library("dplyr")
library("bcmaps")
library("raster")
library("maps")
library("tmap")
```






