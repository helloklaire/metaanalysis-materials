# Training day syllabus [DRAFT]
Work in progress - the information here may not be the final information presented on the training day. This document may be peppered with mistakes - so proceed with caution.

## Topics

We will break down the topics today into several pieces:
1) Some theory
2) Data structure
3) Workspace set-up
4) Data import
5) Data cleaning
6) Pooling the overall effect
7) Forest plots

## Some theory
I won't go too much into the theory as we will be focusing on coding today, but we'll do a bit so that you are understanding the basis of the code/functions  that we will encounter.

Look at the image below:
![Pasted image 20240312140224](https://github.com/helloklaire/metaanalysis-materials/assets/96212126/6a42c14a-9bcd-4ad0-a33a-90748c7c483c)

The figure above is a forest plot. According to Borenstein et al., a forest plot is *a graphical display designed to illustrate the relative strength of treatment effects in multiple quantitative scientific studies addressing the same question*. A bit of a mouthful - don't worry we will go through what this means below.

In this case, the studies we're interested are the impact of high dose versus standard dose of statins in preventing death and myocardial infarction (image from Borenstein et al). Below are the different components of the image:

- **Study Name**: This is a list of the individual studies included in the meta-analysis. There are four studies named "Prove-it," "A to Z," "TNT," and "Ideal."

- **The axis**: The line marked from 0.80 to 1.25 is the scale for the risk ratios. The point of no effect (RR=1) is marked and usually set in the middle of the axis. 

- **Effect size:** As Borenstein et al. wrote in the Introduction to Meta-analysis, *the effect size, a value which reflects the **magnitude** of the treatment effect or (more generally) the **strength** of a relationship between two variables, is the unit of currency in a meta-analysis.* We extract this information from individual studies and we compute a **pooled effect**.
	- In this example, we are using the risk ratio as our effect size measure. It measures the relative risk of the outcome occurring in the treatment group (high-dose statins) compared to the control group (standard-dose statins). A risk ratio of 1 implies no difference between groups. In our forest plot, all RRs are less than 1, suggesting a reduction in risk with high-dose statins.
	- For measures of effect size based on ratios (as in this example) a ratio of 1.0 represents no difference between groups. For measures of effect based on differences (such as mean difference) or relationships (such as correlation), an effect size of 0 represents no differences between groups or no relationship between variables, respectively.
	
- **95% Confidence Interval (CI):** The confidence interval provides a range of values within which we can be 95% confident the true effect lies. It estimates the ***precision*** of your effect size. In our image, horizontal lines represent the CIs for each study. The wider a line, the more uncertain the estimate. If the line crosses the vertical line at RR=1, it suggests that the effect is not statistically significant at the 0.05 level.

- **Relative Weight**: This reflects the contribution of each study to the overall meta-analysis result. It is often based on the inverse of the variance of the effect size; larger studies with more precise estimates have a higher weight. In the plot, the "Ideal" study has the highest weight at 37%. The size of the box also reflects the relative weight of each study. Larger boxes indicate a greater weight in the meta-analysis.

- **p-value**: This is the probability that the observed effect could have occurred by chance if there was actually no effect. Typically, a p-value less than 0.05 is considered statistically significant. In our image, the TNT study has a p-value of 0.002, indicating a statistically significant result. You will probably also notice that the confidence intervals for the TNT study does not cross the RR=1.

- **Summary Effect (Diamond):** The diamond represents the combined results of the meta-analysis - I like calling this the **pooled effect** (although in the Borenstein et al. book, they call it Summary effect). The pooled effect is *nothing more than the **weighted mean** of the individual effects.* However, as Borenstein et al. stated, the mechanism we use to assign weights *the depends on our on our assumptions about the distribution of effect sizes from which the studies were sampled.* There are two mechanisms that we typically use:
	- **Fixed-effects model** - we assume that all studies share the same true effect size and the pooled effect is our estimate of this common effect size.
	- **Random-effects model** - we assume that effect sizes varies from study to study, and therefore there's a distribution of these effect sizes. The poooled effect, then, represents the mean of this distribution.
	- In the image above, the center of the diamond is the pooled risk ratio, and the width represents the confidence interval. Here, the pooled RR is 0.85, favouring high-dose statins, and the diamond does not cross the line of no effect (RR=1), indicating a significant result.
## Data structure

Again, before we begin coding, we will go through some basic ideas behind data structure. This will involve showing datafiles from current or past meta-analysis I'm currently/I've conducted. We will look at data extraction tables to the tables I've imported into R.

We will go through the following:
- What raw data extraction looks like.
- How we can set up the data extraction so that the data we are importing into R is readable.
	- Having columns for each outcomes.
	- Having columns for each subgroups.
- Raw data extraction to meta-analytic data.

## Workspace setup - create R project and start R markdown file

Okay we are getting close to some actual coding :D Let's prep our R Studio environment. We want our data analysis to be reproducible, therefore, we will use the Project function in R Studio to create an environment for our projects. **Do this for each project you will do with R.** 

Steps: 
1) Open R Studio
2) Go to File > New Project...
3) Click New Directory > New Project > Name your directory into something that makes sense
4) Make sure renv is clicked (this creates a virtual environment for your project, making your project organised as all of your project's dependencies are in one place)
5) Once the project is created, open an R Markdown file
6) Name your document into something that makes sense > Leave all the default settings
7) Remove all the pre-populated text but keep the r setup chunk
8) You are now ready to code. Wooohoo!

Within the first chunk of your R markdown file, set up your R workspace by installing all the packages we need:
```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)

# install packages
install.packages("remotes")
install.packages("tidyverse")
install.packages("psych") 
remotes::install_github("wviechtb/metafor")

# load packages

library(tidyverse)
library(psych)
library(metafor)

# set the working directory with the source file - this makes files easier to "call" into R. Make sure that the datafiles you are calling into R are inside the R project folder.

setwd(dirname(rstudioapi::getActiveDocumentContext()$path))

```

### Important shortcuts

- CTRL + ALT + I creates a new chunk in R

## Data import

In the next chunk, we will import our data file. Let's call in the **Herranz-Gomez et al 2024.csv** file. This data is from a meta-analysis that looked at the benefit of exercise in patients with cancer who are receiving chemotherapy. The aim of their study is: *to compare the effectiveness of different exercise modalities in reducing CRF in patients with cancer undergoing chemotherapy*.

We will store this data in an object called df. 

```
df <- read.csv("Herranz-Gomez et al 2024.csv", stringsAsFactors = FALSE) # this code will only work if the datafile is already inside the R project folder
```

Sometimes, you may have many variables within your dataset that you don't need. There are two ways that you go about this:

2) Import the whole dataset to R and clean the datafile inside R.
##### Option 1: Cleaning your data before importing into R
Before you import any data file into your R workspace, it's a good idea to clean it up. Only include columns that relates to your analysis. This may mean just having the author of the paper / paper ID, N, any statistics that relates to your effect size calculation, variables for your sub group analysis. It will make the coding much easier.
##### Option 2: Import the whole dataset to R and clean the datafile inside R.
Data cleaning (also called as data wrangling) is a really big topic and I won't be able to cover everything here. Hadley's book R for Data Science gives a really good introduction on what R can do in regards to cleaning and transforming your dataset so it's ready to be analysed. Specifically, read Chapter 3 and Chapter 5 of his book OR the whole section on Transform: https://r4ds.hadley.nz/

As beginners, I would suggest the first option because you don't need to worry about coding much. However, if you want to try Option 2, you can play around with the select(), filter() or subset() functions in Tidyverse. 

For our training purposes, we will specifically look at data cleaning that you may need for your meta-analysis.
## Looking at the data

Given that we did not set up the data ourselves, we need to understand what's going on with the data. Let's open up the datafile in R by writing the code:

``` 
glimpse(df)
```

It seems like .e represents the data for the experimental group, whereas .c represents the data for the control group. Therefore, n.e = sample size of experimental group, etc. The glimpse() function also tells us what variable types each column represents.

We want the data analysis to run smoothly and that includes making sure that the data we are using is appropriate for the analysis. You have to tell R to recode your data to its appropriate form. When conducting a meta-analysis, we would usually need two types of data - numeric and factor. 

We can change our variables into its appropriate forms. 

```
# change variables into numeric and factors by using the as.numeric() / as.factor() functions
```

We also want to omit any missing data. 

```
df1 <- na.omit(df)
```

## Pooling the overall effect

As you can see in this data file, we don't have the effect size yet - we only have the means and standard deviations of the individual studies. Therefore, we need to calculate an effect size between the groups. Here, we have two options - we can either calculate Cohen's D or Standardised Mean Difference (SMD) or Hedges *g*. To know more about the differences between the two, read Chapter 4 of Introduction to Meta-analysis.

The following code will calculate the pooled effect sizes and effect sizes variances and it will create a new column in your data.

### escalc Function
```
# The following code will calculate the pooled effect sizes and effect sizes variances and it will create a new column in your data named yi and vi

df_smd <- escalc(measure = "SMD",
                 m1i = df1$mean.e,
                 m2i = df1$mean.c,
                 sd1i = df1$sd.e,
                 sd2i = df1$sd.c,
                 n1i = df1$n.e,
                 n2i = df1$n.c,
                 data = df1)
```

- `escalc` is a function for calculating effect sizes.
- `measure = "SMD"` specifies that you are calculating the Standardized Mean Difference (SMD) as the effect size.
- `m1i`, `m2i` refer to the means of the experimental (e) and control (c) groups, respectively.
- `sd1i`, `sd2i` refer to the standard deviations of the experimental and control groups, respectively.
- `n1i`, `n2i` refer to the sample sizes of the experimental and control groups, respectively.
- `data = df1` specifies the dataset where these values can be found.

After using the `escalc` function from the `metafor` package in R to calculate effect sizes, the output data frame will include several columns, among which `yi` and `vi` are key to meta-analyses:

- **`yi`**: This represents the calculated **effect size**s for each study or comparison within your dataset. The nature of these effect sizes (e.g., Standardized Mean Difference, Odds Ratios, etc.) depends on the `measure` argument you specified in the `escalc` function. There are many options that you can use for the `measure` argument. For more information: https://wviechtb.github.io/metafor/reference/escalc.html
    
- **`vi`**: This column represents the **variances of the effect sizes (`yi`).** The variance of an effect size is crucial for weighting studies in a meta-analysis. Studies with lower variance (hence higher precision) are given more weight under the assumption that they provide more reliable estimates of the effect size. The variance is used in various meta-analytic models to estimate the overall effect size and its confidence interval, among other statistics.

## Fixed effects versus random effect models

I am not going into details between fixed and random effects. But in short, fixed effects models have strict assumptions about the population that the samples (and therefore their effect sizes). Fixed effects models assume that the samples come from the the same larger population. Given that experiments in psychology/social sciences uses very different methodologies. it’s easy to see how meta-analyses in our discipline will violate the assumptions of a fixed effects model.

Therefore, all of the meta-analysis I have conducted (and will probably conduct in the future) will be random effect models.

Random effects models account for the variation we get both **within** a study as well as **between**the many different studies in our meta-analysis.

```
# calculate pooled effect sizes using random effects model. what we are doing here is also a meta-regression

model_results <- rma(yi,
					vi,
					method = "REML", # this tells R to use a random effects model. REML is also the default
					data = df_smd)

model_results # presents us the results of the model
```

Here's what each part of the function call is doing:

- `rma()`: This is the function from the `metafor` package to fit meta-analytic models. It can handle both fixed and random effects models, depending on the arguments passed.
- `yi`: Stands for the effect sizes calculated from each study included in the meta-analysis. We got this column from our previous code.
- `vi`: Represents the variances associated with each effect size (`yi`). We got this column from our previous code.
- `method = "REML"`: Specifies that the Restricted Maximum Likelihood (REML) method should be used to estimate the parameters of the random effects model. Similar to the `measure` argument above, there are many options that you can use for the `method` argument. For more information: https://wviechtb.github.io/metafor/reference/rma.html
- `data = df_smd`: Indicates the dataset (`df_smd`) that contains the effect sizes (`yi`) and their variances (`vi`). This dataset is used for the analysis.

**Mathematical Concept Behind Random Effects Model:**
The random effects model acknowledges that the studies included in the meta-analysis are not all drawn from the same exact population, but rather from populations with potentially different effect sizes. This model adds an additional term to account for the variance between studies (often called the between-study variance or tau-squared, τ²).

### Breakdown of the results 
  
#### Heterogeneity Measures

- **`tau^2 (estimated amount of total heterogeneity): 0.0324 (SE = 0.0195)`**
    - `tau^2` represents the estimate of the total amount of heterogeneity, or variability, between the effect sizes of the studies included in the meta-analysis that is not due to sampling error. 

- **`I^2 (total heterogeneity / total variability): 39.54%`**
    - `I^2` quantifies the proportion of the total variability in effect size estimates that is due to heterogeneity rather than chance. An `I^2` of 39.54% suggests that approximately 39.54% of the observed variance reflects differences in true effect sizes rather than sampling error.
    
- **`H^2 (total variability / sampling variability): 1.65`**
    - `H^2` is the ratio of total variability to sampling variability (within-study variance). An `H^2` greater than 1 indicates the presence of heterogeneity. Here, `H^2` of 1.65 suggests some heterogeneity among the studies.

#### Test for Heterogeneity

- **`Q(df = 37) = 73.1292, p-val = 0.0004`**
    - This is the result of the Q-test for heterogeneity, which tests the null hypothesis that all studies share a common effect size. A significant p-value (here, p = 0.0004) rejects the null hypothesis, indicating that there is significant heterogeneity among the studies' effect sizes.

#### Model Results

- **`estimate: 0.3002`**
    - The estimated overall effect size, indicating the magnitude and direction of the effect. An estimate of 0.3002 suggests a positive effect of the intervention or exposure studied across the included studies.
- **`pval: <.0001`**
    - The p-value for the test of the null hypothesis that the true effect size is zero. A p-value of less than 0.0001 strongly suggests that the observed effect is statistically significant.
- **`ci.lb: 0.2012`, `ci.ub: 0.3992`**
    - The lower and upper bounds of the 95% confidence interval for the estimated effect size. This interval suggests that we can be 95% confident that the true effect size lies between 0.2012 and 0.3992. The fact that this interval does not include 0 further supports the significance of the effect.

## Forest plots

A  forest plot is a commonly used visualisation technique in meta-analyses, showing the results of the individual studies (i.e., the estimated effects or observed outcomes) together with their (usually 95%) confidence intervals.  You have seen an example of the forest plot above.

You can create forest plot in R using the following code from metafor:

```
### forest plot 
forest(model_results, header="Study", mlab="", shade=TRUE)
```

## Sub-group analysis versus independent meta-analyses


https://www.metafor-project.org/doku.php/tips:comp_two_independent_estimates
## Publication bias
https://www.metafor-project.org/doku.php/plots:funnel_plot_with_trim_and_fill
