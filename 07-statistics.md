# Statistics

# Table of Contents

[1. Introduction](#section-a)  
[2. Why We Are Using Think Stats](#section-b)  
[3. Instructions for Cloning the Repo](#section-c)  
[4. Required Exercises](#section-d)  
[5. Optional Exercises](#section-e)  
[6. Recommended Reading](#section-f)  
[7. Resources](#section-g)

## <a name="section-a"></a>1.  Introduction

[<img src="img/think_stats.jpg" title="Think Stats"/>](http://greenteapress.com/thinkstats2/)

Use Allen Downey's [Think Stats (second edition)](http://greenteapress.com/thinkstats2/) book for getting up to speed with core ideas in statistics and how to approach them programmatically. This book is available online, or you can buy a paper copy if you would like.

Use this book as a reference when answering the 6 required statistics questions below.  The Think Stats book is approximately 200 pages in length.  **It is recommended that you read the entire book, particularly if you are less familiar with introductory statistical concepts.**

Complete the following exercises along with the questions in this file. Some can be solved using code provided with the book. The preface of Think Stats [explains](http://greenteapress.com/thinkstats2/html/thinkstats2001.html#toc2) how to use the code.  

Communicate the problem, how you solved it, and the solution, within each of the following [markdown](https://guides.github.com/features/mastering-markdown/) files. (You can include code blocks and images within markdown.)

## <a name="section-b"></a>2.  Why We Are Using Think Stats 

The stats exercises have been chosen to introduce/solidify some relevant statistical concepts related to data science.  The solutions for these exercises are available in the [ThinkStats repository on GitHub](https://github.com/AllenDowney/ThinkStats2).  You should focus on understanding the statistical concepts, python programming and interpreting the results.  If you are stuck, review the solutions and recode the python in a way that is more understandable to you. 

For example, in the first exercise, the author has already written a function to compute Cohen's D.  **You could import it, or you could write your own code to practice python and develop a deeper understanding of the concept.** 

Think Stats uses a higher degree of python complexity from the python tutorials and introductions to python concepts, and that is intentional to prepare you for the bootcamp.  

**One of the skills to learn here is to understand other people’s code.  And this author is quite experienced, so it’s good to learn how functions and imports work.**

---

## <a name="section-c"></a>3.  Instructions for Cloning the Repo 
Using the [code referenced in the book](https://github.com/AllenDowney/ThinkStats2), follow the step-by-step instructions below.  

**Step 1. Create a directory on your computer where you will do the prework.  Below is an example:**

```
(Mac):      /Users/yourname/ds/metis/metisgh/prework  
(Windows):  C:/ds/metis/metisgh/prework
```

**Step 2. cd into the prework directory.  Use GitHub to pull this repo to your computer.**

```
$ git clone https://github.com/AllenDowney/ThinkStats2.git
```

**Step 3.  Put your ipython notebook or python code files in this directory (that way, it can pull the needed dependencies):**

```
(Mac):     /Users/yourname/ds/metis/metisgh/prework/ThinkStats2/code  
(Windows):  C:/ds/metis/metisgh/prework/ThinkStats2/code
```

---


## <a name="section-d"></a>4.  Required Exercises

*Include your Python code, results and explanation (where applicable).*

### Q1. [Think Stats Chapter 2 Exercise 4](statistics/2-4-cohens_d.md) (effect size of Cohen's d)  
Cohen's D is an example of effect size.  Other examples of effect size are:  correlation between two variables, mean difference, regression coefficients and standardized test statistics such as: t, Z, F, etc. In this example, you will compute Cohen's D to quantify (or measure) the difference between two groups of data.   

You will see effect size again and again in results of algorithms that are run in data science.  For instance, in the bootcamp, when you run a regression analysis, you will recognize the t-statistic as an example of effect size.

exercise 2.4
using 'totalwgt_lb' are first babies heavier or lighter than others?
compute cohen's d, how does it compare to diff in preg length?
```python
#import modules for analysis and data from book
import thinkstats2
import nsfg
import first
import math

#variables for preg data, using only live births
preg = nsfg.ReadFemPreg()
live = preg[preg.outcome == 1]
#variables to distinguish first-born from others
firsts = live[live.birthord == 1]
others = live[live.birthord != 1]

#function to compute Cohen's D
def CohenEffectSize(group1, group2)
    diff = group1.mean() - group2.mean()
    var1 = group1.var()
    var2 = group2.var()
    n1, n2 = len(group1), len(group2)
    pooled_std = math.sqrt((n1*var1 + n2*var2) / (n1 + n2))
    d = diff/pooled_std
    return d

#observe the mean weight for firsts vs others
print(firsts.totalwgt_lb.mean(), others.totalwgt_lb.mean())
#7.201094430437772 7.325855614973262
#~115.2oz and ~117.2oz

#compute the D for weights
print(CohenEffectSize(firsts.totalwgt_lb, others.totalwgt_lb))
#-0.088672927072602
#because |d| < 0.2, the difference in weights is trivial
#given that the difference in mean weights was about 2oz,
#our result makes sense
```

### Q2. [Think Stats Chapter 3 Exercise 1](statistics/3-1-actual_biased.md) (actual vs. biased)
This problem presents a robust example of actual vs biased data.  As a data scientist, it will be important to examine not only the data that is available, but also the data that may be missing but highly relevant.  You will see how the absence of this relevant data will bias a dataset, its distribution, and ultimately, its statistical interpretation.

```python
#use numkdh to construct dist for number of children under 18
#compute the biased dist
#plot the actual and biased, compare means

import thinkstats2
import nsfg
import first
import thinkplot

#function to bias the number of children in a family
#by multiplying the probability with the number of children
def BiasPmf(pmf, label):
    new_pmf = pmf.Copy(label=label)

    for x, p in pmf.Items():
        new_pmf.Mult(x, x)

    new_pmf.Normalize()
    return new_pmf

#variable for respondents
resp = nsfg.ReadFemResp()

#pmf and plot for actual number of children
pmf = thinkstats2.Pmf(resp.numkdhh, label = 'numkdhh')
thinkplot.Pmf(pmf)
thinkplot.Config(xlabel = 'children', ylabel = 'probability')
#the highest probability is 0 children, so there will be a massive difference
#when we bias the pmf, since this whole chunk will be removed

#pmf and plot for 'observed' number
#biased pmf
bias = BiasPmf(pmf, 'biased')
#setup overlay of pmf and bias
thinkplot.PrePlot(2)
#overlay
thinkplot.Pmfs([pmf, bias])
thinkplot.Config(xlabel='children', ylabel='probability')

#means of actual vs biased
print(pmf.Mean(), bias.Mean())
#1.02420515504 2.40367910066
print(bias.Mean()/pmf.Mean())
#2.34687268349
#the biased mean is almost 235% higher, which is both significant and expected

#therefore, we can draw the conclusion that surveying children about the number of children
#in their family would result in a very biased estimate
```

### Q3. [Think Stats Chapter 4 Exercise 2](statistics/4-2-random_dist.md) (random distribution)  
This questions asks you to examine the function that produces random numbers.  Is it really random?  A good way to test that is to examine the pmf and cdf of the list of random numbers and visualize the distribution.  If you're not sure what pmf is, read more about it in Chapter 3.  

```python
#the numbers generated by random.random in numpy should be U(0,1)
#generate dist of 1000 nums and determine if it's uniform

import numpy as np
import thinkstats2
import thinkplot
from scipy import stats

#check a random random number
#v = np.random.random()
#print(v)

#generate 1000
dist = np.random.random(1000)

#generate the pmf
pmf = thinkstats2.Pmf(dist)
#create plot of the pmf
thinkplot.Pmf(pmf, linewidth=0.1)
thinkplot.Config(xlabel='random numbers', ylabel='probability')
#result has no overlap, every number generated has prob of 1/1000
#this implies randomness

#generate the cdf
cdf = thinkstats2.Cdf(dist)
#create plot of the cdf
thinkplot.Cdf(cdf)
thinkplot.Config(xlabel='random numbers', ylabel='probability')
#compute slope of the cdf to determine if it's uniform
points = cdf.Render()
regress = stats.linregress(points[0], points[1])
slope = regress[0]
print(slope)
#1.0168747140846803
#since the slope of the cdf is essentially 1, we can consider the 
#data to be uniformly random
```

### Q4. [Think Stats Chapter 5 Exercise 1](statistics/5-1-blue_men.md) (normal distribution of blue men)
This is a classic example of hypothesis testing using the normal distribution.  The effect size used here is the Z-statistic. 



### Q5. Bayesian (Elvis Presley twin) 

Bayes' Theorem is an important tool in understanding what we really know, given evidence of other information we have, in a quantitative way.  It helps incorporate conditional probabilities into our conclusions.

Elvis Presley had a twin brother who died at birth.  What is the probability that Elvis was an identical twin? Assume we observe the following probabilities in the population: fraternal twin is 1/125 and identical twin is 1/300.  

>> REPLACE THIS TEXT WITH YOUR RESPONSE

---

### Q6. Bayesian &amp; Frequentist Comparison  
How do frequentist and Bayesian statistics compare?

>> REPLACE THIS TEXT WITH YOUR RESPONSE

---

## <a name="section-e"></a>5.  Optional Exercises

The following exercises are optional, but we highly encourage you to complete them if you have the time.

### Q7. [Think Stats Chapter 7 Exercise 1](statistics/7-1-weight_vs_age.md) (correlation of weight vs. age)
In this exercise, you will compute the effect size of correlation.  Correlation measures the relationship of two variables, and data science is about exploring relationships in data.    

### Q8. [Think Stats Chapter 8 Exercise 2](statistics/8-2-sampling_dist.md) (sampling distribution)
In the theoretical world, all data related to an experiment or a scientific problem would be available.  In the real world, some subset of that data is available.  This exercise asks you to take samples from an exponential distribution and examine how the standard error and confidence intervals vary with the sample size.

### Q9. [Think Stats Chapter 6 Exercise 1](statistics/6-1-household_income.md) (skewness of household income)
### Q10. [Think Stats Chapter 8 Exercise 3](statistics/8-3-scoring.md) (scoring)
### Q11. [Think Stats Chapter 9 Exercise 2](statistics/9-2-resampling.md) (resampling)

---

## <a name="section-f"></a>6.  Recommended Reading

Read Allen Downey's [Think Bayes](http://greenteapress.com/thinkbayes/) book.  It is available online for free, or you can buy a paper copy if you would like.

[<img src="img/think_bayes.png" title="Think Bayes"/>](http://greenteapress.com/thinkbayes/) 

---

## <a name="section-g"></a>7.  More Resources

Some people enjoy video content such as Khan Academy's [Probability and Statistics](https://www.khanacademy.org/math/probability) or the much longer and more in-depth Harvard [Statistics 110](https://www.youtube.com/playlist?list=PL2SOU6wwxB0uwwH80KTQ6ht66KWxbzTIo). You might also be interested in the book [Statistics Done Wrong](http://www.statisticsdonewrong.com/) or a very short [overview](http://schoolofdata.org/handbook/courses/the-math-you-need-to-start/) from School of Data.
