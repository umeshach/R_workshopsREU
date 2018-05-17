Simulating infectious diseases with R
========================================================
author: [NAME]
date: 
autosize: true
font-import: https://fonts.googleapis.com/css?family=PT+Sans
font-family: 'PT Sans', sans-serif;

***
<div class="midcenter"><div style="margin-left:-0px;margin-top:500px;"><img style="margin:0px; background-color:transparent; border:0px; box-shadow:none;" src="figs/logo.png"></img></div></div>



About Myself
========================================================

 - My research interest is [INSERT DESCRIPTION]
 - I use `R` in my research to:
   + organize data 
   + develop machine learning algorithms
   + 
 - I mostly work [in this disease system or approach] 
 - My contact info is [INSERT EMAIL]
 
Review of past workshops 
========================================================
**You should be able to...**

- write a function
- create an HTML doc with code and commentary  
- write code following best practices
- calculate summary statistics of a dataset
- create a figure from data 

Outline 
========================================================
left: 40%

**You should be able to...**
- iterate a calculation
- simulate populations
- source a function 

*** 

**Topics**
  1. Modeling Concepts
  2. Review of functions
  3. Converting ODEs to R code
  4. Simulate exponential growth of a population 
  
  *Break*
  
  5. Simulate an SIR model
  6. Revisiting workflows
  7. Iterations in `R`
  8. Wrap Up 


Introduction to ODE models 
========================================================
left: 50%
 ![ode](figs/SIRode.png)

***

 ![flow](figs/SIRFlow.jpg)


Terminology of ODE models 
========================================================
left: 50%
 <h1> $\frac{dN}{dt} = rN$ </h1>

***
What is (are) the

- independent variable?
- dependent variable?
- state variable?
- parameter(s)? 

Key points from the reading
========================================================

- What's the advantage of modeling infectious diseases?
- What are the different model types?
  -  continuous vs. discrete time
  -  stochastic vs. deterministic
- How would you categorize the SIR model presented?
- What is $R_0$ and how is it used? 
- What does it mean to *solve* an ODE? how is it done?


2. Anatomy of a function 
========================================================


```r
FunctionName <- function(arguement1, arguement2, ...){
  #some analysis
  return(outputOfFunction)
}
```

Example of a function
========================================================
 

```r
 PrevByParType <- function(dataset){
  dataset %>%
    drop_na(Prevalence) %>%
    group_by(Group) %>%
    summarise(MeanPrev = mean(Prevalence), SdErrPrev = sd(Prevalence)/sqrt(n()))
 }

PrevByParType(gmp)
```

3. Connecting concepts to code
========================================================
left: 50%

Based on the reading we know that ordinary differential equations (ODE) can be solved numerically by calculating the state variable at some time in the future. If the state is changing quickly, then the step size needs to be smaller to retain accuaracy. 

**This is called the Euler method.**

***

 ![Euler](figs/Euler.png)


Euler method in R
========================================================
left: 50%

ODEs can be solved in `R` using the `lsoda` function in the `deSolve` package. 

The `lsoda()` functions has specific format requirements. 

Open the W3_exercise.Rmd to continue 

***

 ![Euler](figs/Euler.png)

Step 1. Install and load deSolve package
========================================================


```r
# install package (if not already)
install.packages("deSolve")

# load library
library(deSolve)
```

Step 2. Pull up lsoda manual page
========================================================

Use the `?` search function 

 ![lsoda](figs/lsodaMan.png)

lsoda arguements
========================================================


Take a few minutes to decide what do the first 4 arguements mean

- y 
- times
- func
- params

lsoda arguements
========================================================

What do the first 4 arguements mean?

- y : Starting values for the state variable(s) 
- times : times that you want the state variable(s) recorded
- func : function describing how the state variable changes (Model)
- params : parameter values used in the model 

What are the special instructions for the func arguement?


func for lsoda
========================================================
The manual states that the function should be defined as: 


```r
func <- function(t,y,parms)
```

Let's work to write the exponential growth model $\frac{dN}{dt}= rN$ in this form 


switch over to `W3_Exercise.Rmd`

========================================================
<h1>Break</h1>

Simulating an SIR model 
========================================================

work in groups of 2-3 to build an SIR model and simulate data

Steps:

1. Using ExponentialGrowth as a guide write a `DensityDepSIR()`
2. Run a simulation using the parameter values (beta, gamma) and initial state conditions (S=,I=,R=)
3. Plot the simulation


If your group finishes move onto exploring parameter values. 


Lessons learned: Simulating an SIR model 
========================================================

- `lsoda()` solves ODEs
- entries within a vector can be named (`c(N=10)`)
- state values can be modeled as proportion of total population
- `ggplot()` expects data as a data.frame class object
- Tidyverse pipes (`%>%`) can be fed into `ggplot()`
- `gather()` converts wide data to long format
- ...

Keeping a clean workflow
========================================================
We touched on organization during the Reproducible Research workshop. Let's focus on the *scripts* folder.

 ![flow](figs/directory_orgScripts.png)

Keeping a clean workflow
========================================================
If you are writing functions that:
- are long
- used for different parts of your research
- won't be changing often. 

Then, a script might make for clean workflow. 

 ![flow](figs/directory_orgScripts.png)

What is a script?
========================================================

- stable code consisting of functions
- is saved as `.R` file type
- is loaded into the R environment using the `source()` command
- simplifies your `.Rmd` files 



Make a script
========================================================

1. Create a `.R` file 
2. Make some comments at the start of the file
3. Save file in scripts directory with an informative name


Make a script
========================================================
left: 30%
1. Create a `.R` file 

***

 ![](figs/newScript.png)
 
 
Make a script
========================================================

1. Create a `.R` file 
2. Make some comments at the start of the file


```r
# Writen on May 11, 2018 by Reni Kaul
# This script contains functions for the modeling R workshop; REU summer 2018. 
# Originally written to be part of the simulations work in W3_Exercise.Rmd

ExponentialGrowth <- function(t, y, params) {
# Computes the rate of change of the variable y over time
#
# Args:
#   t: vector of time points in the integration
#   y: vector of variables in the differential equation
#   params: vector of parameter values
```

*Never start a script with rm(list = ls())*

Make a script
========================================================

1. Create a `.R` file 
2. Make some comments at the start of the file
3. Save file in scripts directory with an informative name

- What is good name for this script? 
- Create a script folder inside the `W3_Modeling` and save your script


Use a script
========================================================

 *only for this demo Session > Restart R*

Source the script 


```r
source("scripts/YOURFILE.R")
```

What's in the Environment tab?

Iterating in R
========================================================
left: 50%

- The Euler method is an example of iteration or repeating a process over and over. 
- Iteration can be a common part of data analysis and is implimented in `R` using loops

***

   ![Euler](figs/Euler.png)


Loops come in a few different flavors
========================================================
   ![loop](figs/loops.png)

We will focus on a For loop


Exponential Model
========================================================

At the next time step the population size of an exponentially growing population is:

$N_{t+1} = N_t + rN_t$

What is the populations size at t+1?

Exponential Model at t+1
========================================================


```r
Nt <- 10 # starting population
r <- 0.02 # population increases 2% each time
Nt1 <- Nt + r*Nt

Nt1
```

```
[1] 10.2
```

Let's iterate this function to determine the population size 100 time steps in the future. 

Exponential Model at t+100
========================================================
left: 50%

For loop syntax is: 

   ![](figs/forloopsyntax.png)

***

which is coded as: 


```r
for(i in 1:10){
  print(i)
}
```

What is the output of this loop? 


Exponential Model at t+100
========================================================
left: 50%

For loop syntax is: 

   ![](figs/forloopsyntax.png)

***

which is coded as: 


```r
for(i in 1:10){
  print(i)
}
```

```
[1] 1
[1] 2
[1] 3
[1] 4
[1] 5
[1] 6
[1] 7
[1] 8
[1] 9
[1] 10
```


Exponential Model at t to t+100
========================================================

Starting with a vector `N.seq` where `N.seq[1]`$=N_t$ use the `N.seq[i]` and `N.seq[i+1]` to finish this loop.


```r
N.seq <- c(rep(0,100)) # make a vector where the calculations will be stored. 
N.seq[1] <- 10 # set the current population size to 10 

for(i in 1:100){
    # command to iterate N_t+1 calculation
}

N.seq # N at N_t through N_t+100 
```

Exponential Model at t to t+100
========================================================

Here we are using the value at $N_t$ (`N.seq[i]`) to calculate the value at the next time step $N_{t+1}$ which is saved in the next spot of the `N.seq` vectors at `N.seq[i+1]`


```r
N.seq <- c(rep(0,100)) # make a vector where the calculations will be stored. 
N.seq[1] <- 10 # set the current population size to 10 

for(i in 1:100){
    N.seq[i+1] <- N.seq[i] + (r*N.seq[i])
}

N.seq # vector of N at N_t through N_t+100 
```

*Concept check: what would N.seq look like if the for command was changed to for(i in 1:50)?*