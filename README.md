---
title: 'MA334-SP: Lab 1'
output:
  pdf_document: default
  html_notebook: default
---

### In this Lab, you will learn:

- how to create a list and use some basic methods e.g. length()
- how to create a data frame and perform some basic operations on the data frame
- sample from a list using simple random sampling
- use a loop
- plot a histogram
- load and run some basic commands using an R datasets package (mtcars)
- create a categorical variable from a continuous variable 
- how to use the dplyr (pronounced “dee-ply-er”) package and pipe operator
- sample from a data frame using stratified sampling

### Required packages:

- dplyr (install.packages("dplyr")) 

Install the dplyr package through, for example, "Tools" then "Install Packages" in the RStudio menu, or by typing install.packages("dplyr") in the console. Put "library(dplyr)" in your RStudio code. Requires a recent version of RStudio.

```{r}
#install.packages("dplyr") # Comment out once this has been installed
library(dplyr)
```

---

### Section 1.1: Types of data

\underline{Qualitative data}

```{r}
# Create a list of categorical variables
#?c # This is a generic function which combines its arguments.
pets<-c('cat','dog','hamster','rabbit','fish','guinea_pig') 
pets
# Find how many different types of pet are in the list
length(pets)
# Find the R data type
typeof(pets)
# Is the variable "pets" quantitative or qualitative?
# If qualitative, ordinal or nominal?
```

```{r}
# Suppose we have a list with repeated pet names e.g.:
pets<-c('cat','dog','hamster','rabbit','fish','guinea_pig','dog','hamster')
# Obtain a list with duplicate names removed using the unique() function
pet_names<-unique(pets)
pet_names
# How many unique pet names are there? Print out your answer in a complete sentence.
length(pet_names)
print(paste('There are',length(pet_names),'types of pet.'))
```

\underline{Quantitative data}

```{r}
# Create a list of numbers
numbers<-c(10,8,11,2,4,5)
# Find the data type
typeof(numbers)
# Sort the numbers in ascending order
sort(numbers) # ?sort
# Sort the numbers in descending order
sort(numbers,decreasing=TRUE)
```

\underline{Create a data frame}

```{r}
# Suppose the list "numbers" corresponds to the number of pets owned by children in a class.
# Create a data frame with this information
df<-data.frame(pet_names,numbers)
df
# Print out the column names
colnames(df)
# Change the name "numbers" to "tally"
colnames(df)[2]<-"tally"
df
# Is the variable "tally" discrete or continuous data?
# Print out the pet names
df$pet_names
# What is the largest number in "tally" (use the max funtion)
max(df$tally)
# Which element in the list holds the maximum value in "tally"? (use the which function)
which(df$tally==max(df$tally))
# Find which pet is the most common
most_common <- df$pet_names[which(df$tally==max(df$tally))]
# Print out the most common pet
print(paste("The most common pet in the class is a",most_common)) # use print() and paste() 
```
### Section 1.2: Sample and population

\underline{Example 1.1 in the book}

```{r}
# A large population contains equal proportions of 1,2,3,4 & 5
# Create a list
pop<-c(1,2,3,4,5) 
pop
```

\underline{Use simple random sampling to sample from the population}

```{r}
# Sample with replacement using a sample size of 4
n<-4
s<-sample(pop,n,replace=TRUE)
s
# Find the sample average (mean)
ave<-mean(s)
ave
# Notice that you get a different sample each time you run the code chunk
```

```{r}
# Set the random seed to reproduce values
set.seed(1)
s<-sample(pop,n,replace=TRUE)
s
# Notice that now you get the same set of numbers each time. 
# Try changing the seed and re-run the code chunk
```

```{r}
# Choose 40 samples from the population and compute the sample mean each time
times<-40
n<-4
# A loop generates samples and their averages: 
# First you need to create a list of 20 zeros to store the averages of each sample
ave<-rep(0,times=times) 
set.seed(1)
for(i in 1:times){ 
  s<-sample(pop,n,replace=TRUE) 
  ave[i]<-mean(s) 
} 

# Now plot a histogram of the sample means:
hist(ave,xlim=c(1,5.0))  
# add a vertical line for the population and sample means
abline(v=mean(pop),col="blue");abline(v=mean(ave),col="red")
```

Compare the population mean to the average over the sample means.
The average over the sample means tends to the population mean as we average over more and more samples. Try this! (Increase - or decrease - the variable "times' in the above code chunk).


\underline{Effect of sample size on the width of the sampling distribution}

```{r}
# Repeat the code chunk above, trying different values for the sample size, n e.g. 10, 30, 100
# What is the effect of the sample size on the width of the sampling distribution?
times<-100 # Generate 100 samples
n<-10
# A loop generates samples and their averages: 
# First you need to create a list of 20 zeros to store the averages of each sample
ave<-rep(0,times=times) 
set.seed(1)
for(i in 1:times){ 
  s<-sample(pop,n,replace=TRUE) 
  ave[i]<-mean(s) 
} 

# Now plot a histogram of the sample means:
hist(ave,xlim=c(1,5.0),main="Sampling distribution of the sample mean")  # Include a title
# add a vertical line for the population and sample means
abline(v=mean(pop),col="blue");abline(v=mean(ave),col="red")
```

### Using an R datasets package

\underline{Here we practice using the R datasets package \texttt{mtcars}}

```{r}
?mtcars # Read about the data set
# Load mtcars dataset
data(mtcars) # data() function loads specified data sets, or list the available data sets.

# Print out the first few rows
head(mtcars)
```
```{r}
# How many rows (observations) and columns (variables) are there?
dim(mtcars)
```

There are 32 observations and 11 variables.

```{r}
# Print out the variable names
names(mtcars) # names() : functions to get or set the names of an object.
mtcars # prints out mtcars
```


```{r}
# Create a new data frame, ND
ND <- mtcars[,c(1,2,4,6)] # read into ND all rows from mtcars, but just features in columns 1,2,4 & 6
head(ND)
ND
str(ND) # str() : function to compactly Display the Structure of an Arbitrary R Object
ND$mpg # prints out all mpg values
ND$cyl # prints out the cyl column
```

```{r}
attach(mtcars) # so we don't need to write "mtcars" each time
typeof(mpg) # What data type is mpg?
# Create a categorical variable from mpg (fuel_economy)
# First, bin the variable into the categories "Poor", "Normal" and "Good" using ifelse
?ifelse
fuel_economy <- ifelse(ND$mpg <= 15.43,"Poor",ifelse(ND$mpg <=22.8 , "Normal", "Good"))
# Create a new data frame with fuel_economy included 
mtcars_new <- mtcars # Create the data frame
mtcars_new$fuel_economy <- fuel_economy # Include the new variable
head(mtcars_new) # View the first few rows
# Remove the old variable (mpg)
mtcars_new <- mtcars_new[,-1]
head(mtcars_new)
# Alternatively, use the select() function provided by dplyr
#attach(mtcars_new)
# Before running the below, comment out the line mtcars_new <- mtcars_new[,-1] above and re-run the code chunk
#mtcars_new <- dplyr::select(mtcars_new,-mpg) 
#head(mtcars_new)
```

How many unique "number of cylinders" are there?

```{r}
unique(mtcars$cyl)
```

```{r}
# Use stratified sampling to sample 4 cars randomly from each of the 3 cylinder categories 
# (or "strata").

# dplyr is a data manipulation package for R that's designed for use with pipes.
# The pipe operator (%>%) in R feeds the results of one operation into the next.

# Use the pipe symbol (%>%) to group mtcars by cylinder type (group_by), 
# then pipe again to sample (using simple random sampling) from each strata (slice_sample)

?slice_sample # randomly selects rows
?group_by # converts an existing table into a grouped table

stratified <- mtcars %>%
  group_by(cyl) %>%
  slice_sample(n=4,replace=TRUE) # sample size is n, sampling with replacement
stratified # View the samples
```
