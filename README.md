# Datathon
## Raw Data
- First two digits for year, e.g. "15"
- Next one or two letters for semester, "f" for fall, "s" for spring, "su" for summer, "w" for winter
- Next one or two letters for academic level, "f" for freshman, "s" for sophomore, "j" for junior, "se" for senior
## Description of Files
- Data Analyses.Rmd is the code
- DID.csv, Freshmen_China.csv, and Freshmen_India.csv are cleaned data
- Enrollment.png and Trend.png are graphs
- table.htm is the regression table

## Code (same as those in Analyses.Rmd)

#### Load Datasets and Packages
```{r}
library(readr)
China <- read_csv("Freshman_China.csv")
India <- read_csv("Freshman_India.csv")
Analyses <- read_csv("DID.csv")
library(tidyverse)
library(stargazer)
```

#### Data cleaning and manipulation
```{r}
China<-filter(China,between(Year,2001,2020))
India<-filter(India,between(Year,2001,2020))
Analyses<-mutate(Analyses,C=ifelse(Country=="China",1,0))
Analyses<-mutate(Analyses,Y=ifelse(Year>=2018,1,0))
Analyses<-mutate(Analyses,War=ifelse(Country=="China" & Year>=2018,1,0))
Analyses<-mutate(Analyses,C2=ifelse(Country=="China",1,0))
Analyses<-mutate(Analyses,Y2=ifelse(Year>=2019,1,0))
Analyses<-mutate(Analyses,War2=ifelse(Country=="China" & Year>=2019,1,0))
```

#### Graphs
```{r}
ggplot(data=China,mapping = aes(x=Year,y=Fall))+geom_line()+geom_point()
ggplot(data=Analyses,mapping = aes(x=Year,y=Enrollment,color=Country))+geom_line()+geom_point()
ggplot(data=Analyses,mapping = aes(x=Year,y=Enrollment,color=Country))+geom_smooth()+geom_point()
```

#### Difference-in-Difference Model
```{r}
## Model with treatment from 2018
Model1<-lm(Enrollment~C+Y+War,Analyses)
## Model with treatment from 2019
Model2<-lm(Enrollment~C2+Y2+War2,Analyses)
```

#### Regression Tables
```{r}
stargazer(Model1,Model2,title = "Difference-in-Difference Estimates",type="text",digits=3,out="table.htm",dep.var.labels = c("Freshmen Enrollment (Treatment from 2018)","Freshmen Enrollment (Treatment from 2019)"),covariate.labels = c("Country","Year","US-China Trade War"))
```
