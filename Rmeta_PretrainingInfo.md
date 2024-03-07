# Pre-training guidelines and readings

Some information to prepare for our training workshop. Please make sure that you read it carefully and get everything set up. For example, it would be great if you can install R and RStudio (including the necessary packages) before the training day so I can do any troubleshooting beforehand. Doing this will help in making the training day run more effectively. I would also highly recommend to have a second computer/screen/tablet as it will make your experience much better given that you can watch me one one screen and type on the other.

Before the training, it would be great to have some exposure to R programming language and the RStudio interface. The Youtube video below is only 40 mins and it seems like a good introduction (I just quickly skimmed it so I don't really know how good it is - if you have another resource, please let me know!)

[Learn R in 39 minutes, Youtube](https://www.youtube.com/watch?v=yZ0bV2Afkjc)

Please also read Chapter 1 of Introduction to Meta-analysis: https://www.agropustaka.id/wp-content/uploads/2020/04/agropustaka.id_buku_Introduction-to-Meta-Analysis.pdf

## R and R Studio
You must have a recent version of R (version 4.0 or later) and RStudio installed and tested on your computer. You can download the most recent version of R from [https://cran.r-project.org](https://aus01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fcran.r-project.org%2F&data=05%7C01%7Cmaria.castellanosreynosa%40jcu.edu.au%7Cf55ebef450c248d7958b08dad1b6187d%7C30a8c4e81ecd4f148099f73482a7adc0%7C0%7C0%7C638052877320240252%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C3000%7C%7C%7C&sdata=t%2B5%2FADta4bYaIuRXffeXb3NcqofVVsN1NO7%2FDdkQzSw%3D&reserved=0 "https://cran.r-project.org/"), and RStudio from [https://posit.co/download/rstudio-desktop/](https://aus01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fposit.co%2Fdownload%2Frstudio-desktop%2F&data=05%7C01%7Cmaria.castellanosreynosa%40jcu.edu.au%7Cf55ebef450c248d7958b08dad1b6187d%7C30a8c4e81ecd4f148099f73482a7adc0%7C0%7C0%7C638052877320240252%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C3000%7C%7C%7C&sdata=Fj8rlSmy%2BcBP7Cm0eFe7vfyxRST2xg7Tuf7GJHtm6jg%3D&reserved=0 "https://posit.co/download/rstudio-desktop/").
## R Packages
Please install *in advance* the following R packages that we will use during the workshop - you can copy and paste the following code.
```
# install packages
install.packages("remotes")
install.packages("tidyverse")
install.packages("meta")
install.packages("psych")
remotes::install_github("wviechtb/metafor")

# load packages

library(tidyverse)
library(meta)
library(psych)
library(metafor)

# If you are prompted to build packages from source, select "No"
```


## Workshop data 
We will be using a number of example datasets for the workshop. Before the training day, please download the csv files from my github folder:
- White et al 2023 TPB Charitable Donations - https://github.com/helloklaire/metaanalysis-materials/blob/main/White%20et%20al%202023%20TPB%20Charitable%20Donations.csv
- Herranz-Gomez et al 2024 - https://github.com/helloklaire/metaanalysis-materials/blob/main/Herranz-Gomez%20et%20al%202024.csv

Download the file by clicking the download button (see below)
![[Pasted image 20240220232128.png]]
## Caveats and other resources to learn more about meta-analysis

Meta-analysis is a powerful tool for evaluating the research evidence. This training is just a start and in no-way will try to teach you everything about meta-analyses. This training is conceptual and omit a lot of the more involved mathematical stuff. The focus is on understanding what meta-analysis is doing… and then we’ll spend most of the time applying this understanding in the R language. The idea is to bootstrap your knowledge as quickly as possible so that you can start with your own analyses and then turn to more technical texts if needed.

Therefore, in addition to learning how to code, it would also be useful to learn more about meta-analysis. The following books are key starting resources:
- Introduction to meta-analysis by Borenstein et al. https://www.agropustaka.id/wp-content/uploads/2020/04/agropustaka.id_buku_Introduction-to-Meta-Analysis.pdf
- Doing meta-analysis in R by Harrer et al. https://bookdown.org/MathiasHarrer/Doing_Meta_Analysis_in_R/
