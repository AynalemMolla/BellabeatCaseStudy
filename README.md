# Summary 
According to OMB's 2019 study, 61 % of the population own a smart device. This means an increase in market size for high-tech companies like Bellabeat. The customer will use a smart device to monitor their well-being and membership will increase. Bellabeat can use this trend in its digital promotion and e-commerce distribution strategies.
##ASK
###Business task
Finding new growth opportunities by analyzing Smart device usage and coming up with marketing strategy recommendations. 
Key project stakeholders are Urška Sršen co-founder and chief creative officer, and Sando Mur co-founder and member of executive management. Finally, members of the marketing analytic team.
## PREPARE
The data for this project is publicly available and downloaded to a folder specifically designated for the project. The downloaded folder has 18 Comma-separated value(CSV)  files which are structured and organized in a wide data format. The data is collected firsthand(record of observation by smart device) from 30 willing Fitbit users. The data fulfills most of the good aspects of data ethics like ownership, consent, privacy, and openness, which is available and accessible to everyone. 
Completeness and accuracy of data checked to verify the integrity of the data. The data will help the analyst to get insight into the usage of smart devices, especially well-being.  
There are some limitations with the data and additional sources might be used to maintain the integrity of the analysis. Second, minutes, hours, and days are used to organize the data which need to be reorganized to have the same standard of measurement. 
RStudio Desktop version was used for this analysis. First, I started by installing and loading tidyverse package. 
```{r Package}
install.packages("tidyverse")

```

loading

```{r loading}
library(tidyverse)
```

Then, the CSV files were uploaded,

```{r upload}
dailyActivity_merged <- read.csv("dailyActivity_merged.csv")


sleepDay_merged <- read.csv("sleepDay_merged.csv")


weightLogInfo_merged <- read.csv("weightLogInfo_merged.csv")


```

Each data structure was checked using the view() function.
Furthermore,  functions like;
```{r data}
str(dailyActivity_merged)

glimpse(dailyActivity_merged)

```

Used to see the overall structure of the data. 

##PROCESS
Due to the size of some the data frame, RStudio were used for all data analysis purposes. 
Data integrity throughout its life-cycle is maintained by using the same format, and data constraints which determine validity,  by making sure it is aligned with the business objective or checking data errors. This makes sure that the data is valid, accurate, complete, and consistent.  
###Cleaning
I started by installing a package of here,skimr and janitor, to start the cleaning process. 
```{r package}
install.packages("here")
library("here")
install.packages("janitor")
library("janitor")
install.packages("skimr")
library("skimr")
```
Using janitor package, variable name cleaned 
```{r cleaning}
 clean_names(dailyActivity_merged)
 clean_names(sleepDay_merged)
 clean_names(weightLogInfo_merged)
```
Here, the date data types are mentioned as characters, which need adjustment. Using lubiredate package, we can change the data type as below, 
```{r View two}
View(dailyActivity_merged)
class(dailyActivity_merged$ActivityDate)
mdy(weightLogInfo_merged$Date)

```

To drop the hour’s value from the below two data frames on sleep and weight, which will make it possible to join with activity data.
```{r formating}
sleepDay_merged$SleepDay <- format(mdy_hms(sleepDay_merged$SleepDay), format= "%m/%d/%y")

weightLogInfo_merged$Date <- format(mdy_hms(weightLogInfo_merged$Date), format = "%m/%d/%y")

```

```{r formating two}
sleepDay_merged$SleepDay <- mdy(sleepDay_merged$SleepDay)

View(sleepDay_merged)

str(sleepDay_merged)
```

####Rename,
```{r rename}
dailyActivity_merged <- dailyActivity_merged %>%
  rename(Date= ActivityDate)

sleepDay_merged <- sleepDay_merged %>%
  rename(Date= SleepDay)

```


In addition, on  wightloginfo data, the IsManuaLReport variable has two values as confirmed below,
```{r formating three}
class(weightLogInfo_merged$IsManualReport)

unique(weightLogInfo_merged$IsManualReport)

```

Which has to be changed to a logical(Boolean) data type as below,

```{r boolean}
weightLogInfo_merged$IsManualReport <- as.logical(weightLogInfo_merged$IsManualReport)

```

####Duplicate 
First, I need to check if there are duplicate values.
```{r duplicate}
dailyActivity_merged[duplicated(dailyActivity_merged),]

sleepDay_merged[duplicated(sleepDay_merged),]

weightLogInfo_merged[duplicated(weightLogInfo_merged),]

```

A duplicate was found with sleepDay_merged Data, which needs to be removed,
```{r cleaing duplicate}
sleepDay_merged <- distinct(sleepDay_merged)
```

##ANALYSIS
Summarizing and understanding the data frame.
How many participants,
```{r analysis}
n_distinct(dailyActivity_merged$Id)

n_distinct(sleepDay_merged$Id)


```

Summary of selected variables observation 

```{r summary}
dailyActivity_merged%>%
select(TotalSteps,TotalDistance,SedentaryMinutes)%>%
summary()

sleepDay_merged%>%
select(TotalSleepRecords,TotalMinutesAsleep,TotalTimeInBed)%>%
summary()

```
###Relationship between variables 
Total steps and sedentary minutes, which are negative 
```{r relation}
ggplot(data=dailyActivity_merged)+ geom_point(mapping = aes(x=TotalSteps,y=SedentaryMinutes))
```
