## Introduction:

This case study is based on Case 1 of the Google Data Analytics Professional Certificate: "How does a bike-share navigate speedy success?" Our role in this analysis is as junior data analysts on Cyclistic's marketing team, a bike sharing company located in Chicago. The Director of Marketing emphasizes that the company's future success relies on maximizing the count of annual members. Therefore, our objective is to investigate marketing strategies for converting recreational riders into annual members. To achieve this, it is crucial for us to gain an understanding of the distinct ways in which recreational riders and annual members utilize bikes.

## About the company 

Until now, Cyclistic’s marketing strategy relied on building general awareness and appealing to broad consumer segments. One approach that helped make these things possible was the flexibility of its pricing plans: single-ride passes, full-day passes, and annual memberships. Customers who purchase single-ride or full-day passes are referred to as casual riders. Customers who purchase annual memberships are Cyclistic members

Cyclistic’s finance analysts have concluded that annual members are much more profitable than casual riders. Although the pricing flexibility helps Cyclistic attract more customers, Moreno believes that maximizing the number of annual members will be key to future growth. Rather than creating a marketing campaign that targets all-new customers, Moreno believes there is a very good chance to convert casual riders into members. She notes that casual riders are already aware of the Cyclistic program and have chosen Cyclistic for their mobility needs.

Moreno has set a clear goal: Design marketing strategies aimed at converting casual riders into annual members. In order to do that, however, the marketing analyst team needs to better understand how annual members and casual riders differ, why casual riders would buy a membership, and how digital media could affect their marketing tactics. Moreno and her team are interested in analyzing the Cyclistic historical bike trip data to identify trends.

## Ask
* How do annual members and casual riders use Cyclistic bikes differently?
* Why would casual riders buy Cyclistic annual memberships?
* How can Cyclistic use digital media to influence casual riders to become members?
* Identity the business task, and consider the key stakeholders

Our goal is to Design marketing strategies aimed at converting casual riders into annual members. The key stakeholders are Cyclist executive team and The director of marketing.

## Prepare
We will use Cyclistic’s historical trip data from 2022 January to 2022 December to analyze and identify trends. The data can be found from this link: https://divvy-tripdata.s3.amazonaws.com/index.html

The datasets have a different name because Cyclistic is a fictional company. For the purposes of this case study, the datasets are appropriate and will enable you to answer the business questions. The data has been made available by Motivate International Inc. under this license: https://ride.divvybikes.com/data-license-agreement

Since the data contain 5,661,859 rows, using RStudio is the preferred choice over a spreadsheet. This decision is based on the fact that spreadsheets are not ideal for managing such large datasets effectively.

### Install and loading packages
```
# Since we have installed these packages, we can simply load them.
library(tidyverse)
library(ggplot2)
library(lubridate)
```

### Importing the data
```
# Import data from local storage. 
Jan_2022<-read.csv("C:\\Users\\maizh\\Downloads/202201-divvy-tripdata.csv",header=TRUE)
Feb_2022<-read.csv("C:\\Users\\maizh\\Downloads/202202-divvy-tripdata.csv",header=TRUE)
Mar_2022<-read.csv("C:\\Users\\maizh\\Downloads/202203-divvy-tripdata.csv",header=TRUE)
Apr_2022<-read.csv("C:\\Users\\maizh\\Downloads/202204-divvy-tripdata.csv",header=TRUE)
May_2022<-read.csv("C:\\Users\\maizh\\Downloads/202205-divvy-tripdata.csv",header=TRUE)
Jun_2022<-read.csv("C:\\Users\\maizh\\Downloads/202206-divvy-tripdata.csv",header=TRUE)
Jul_2022<-read.csv("C:\\Users\\maizh\\Downloads/202207-divvy-tripdata.csv",header=TRUE)
Aug_2022<-read.csv("C:\\Users\\maizh\\Downloads/202208-divvy-tripdata.csv",header=TRUE)
Sept_2022<-read.csv("C:\\Users\\maizh\\Downloads/202209-divvy-tripdata.csv",header=TRUE)
Oct_2022<-read.csv("C:\\Users\\maizh\\Downloads/202210-divvy-tripdata.csv",header=TRUE)
Nov_2022<-read.csv("C:\\Users\\maizh\\Downloads/202211-divvy-tripdata.csv",header=TRUE)
Dec_2022<-read.csv("C:\\Users\\maizh\\Downloads/202212-divvy-tripdata.csv",header=TRUE)
```

We need to examine the column names of each file and make the necessary arrangements before merging the data into one file.

```
# Check the column naes of each data set
colnames(Jan_2022)
colnames(Feb_2022)
colnames(Mar_2022)
colnames(Apr_2022)
colnames(May_2022)
colnames(Jun_2022)
colnames(Jul_2022)
colnames(Aug_2022)
colnames(Sept_2022)
colnames(Oct_2022)
colnames(Nov_2022)
colnames(Dec_2022)
```

With consistent column names across all data sets, we will be able to merge the data from the 12 months into a single data frame.

```
# Combine all 12 months data into a single data frame
Trips_2022<- bind_rows(Jan_2022, Feb_2022, Mar_2022, Apr_2022, May_2022, Jun_2022, Jul_2022,
                       Aug_2022, Sept_2022, Oct_2022, Nov_2022, Dec_2022)
```

### Process-Cleaning the data

Firstly, we need to Examine the Trips_2022 data frame to verify the accuracy of the data format and identity any inconsistencies.

```
# Check the data format for each columns.
str(Trips_2022)
```

The result shows that the "started_at" and "ended_at" columns are not having identical formatting types, as they are currently formatted as characters/chr while containing date-time data. It is necessary to convert these columns to the appropriate date-time format. Once the data is correctly formatted, it will enable precise analysis of the data set.

```
# Converting "started_at" and "ended_at" columns to date-time forma(YYYY-MM-DD HH:MM:SS). 
Trips_2022$started_at<- ymd_hms(Trips_2022$started_at)
Trips_2022$ended_at<- ymd_hms(Trips_2022$ended_at)
str(Trips_2022)
```

Now all the data have identical column names and formatting types. Secondly,  we need to check for any null and duplicate values within the data set and adjust them to ensure data integrity and completeness.

```
# Check for any null value
na<- any(is.na(Trips_2022))
print(na)

# Remove all the null value
Trips_2022<- Trips_2022 %>% 
              drop_na()

nna<- any(is.na(Trips_2022))
print(nna)
```

```
# Check for any duplicated value
duplicated_rows<- duplicated(Trips_2022[,c("ride_id", "started_at")])
summary(duplicated_rows)
```

Thirdly, we will verify if each value in the "ride_id" column contains exactly 16 characters. Furthermore, we will check the label names within the "rideable_type" and "member_casual" columns.

```
# Check if each value has 16 characters in ride_id
ride_id_length<- nchar(Trips_2022$ride_id)
summary(ride_id_length)
```

```
# Check the label name of "rideable_type" and "member_casual" columns.
table(Trips_2022$rideable_type)
table(Trips_2022$member_casual)
```

Now we have successfully resolve the potential problems in the data, ensuring the integrity and accuracy of the data set. Next, we can proceed to the analysis phase.

### Analysis and Share

To enhance our analysis and gain deeper insights, it is essential to add new columns of content to the data set. These additional columns can provide additional relevant metrics that will contribute to a more comprehensive understanding of the data.

```
# Add columns that contains day, month, year, hours etc.
Trips_2022$Date<- as.Date(Trips_2022$started_at)
Trips_2022$Month<- format(as.Date(Trips_2022$Date), "%m")
Trips_2022$Day<- format(as.Date(Trips_2022$Date), "%d")
Trips_2022$Year<- format(as.Date(Trips_2022$Date), "%Y")
Trips_2022$Day_of_Week<- format(as.Date(Trips_2022$Date), "%A")
Trips_2022$Hours<- format(as.POSIXct(Trips_2022$started_at), "%H")
```

```
# Calculate the length of each ride
Trips_2022$ride_length<- difftime(Trips_2022$ended_at, Trips_2022$started_at)
str(Trips_2022)
```

Base on the result, the "ride_length" column is not formatted as number, in order to run calculations,  we need to convert the column from factor to numeric.

```
# convert the ride_length to numeric format
Trips_2022<- Trips_2022 %>% 
  mutate(ride_length = as.numeric(as.character(ride_length)))
str(Trips_2022)
```

In the last step, we need to verify all the value in "ride_length" column are non-negative or do not exceed the duration of a day.

```
# Change the value of ride_length and find out if there is any negative value 
# or values that greater than a day
Trips_2022_v2<- Trips_2022 %>% 
  filter(!(ride_length < 0 | ride_length >= 86400))
dim(Trips_2022_v2)
```

### Visulaztion

```
# To prevent values in the graph from being displayed in scientific notation such as "2e+02," 
options(scipen = 100)
```

### Aggregate Rides and Trip Duration by Members and Casual Users

```
# Visualize the Total Rides Taken by Members and Casual Users
Trips_2022_v2 %>% 
  group_by(member_casual) %>% 
  summarize(number_of_ride =n())

Trips_2022_v2 %>% 
  group_by(member_casual) %>% 
  summarize(number_of_ride =n()) %>% 
ggplot(mapping= aes(x= 'Content', y=number_of_ride, fill= member_casual))+  
  geom_bar(stat= 'identity', 
           position ='stack', width = 1)+            
  coord_polar(theta = 'y')+          
  labs(x='', y='', title = 'Aggregate Rides by Membership Type: Members vs Casual Users')+  
  theme_void()+    
  geom_text(aes(label = number_of_ride), position = position_stack(vjust = 0.5))

# Visualize the Trip Duration by Members and Casual Users
Trips_2022_v2 %>% 
  group_by(member_casual) %>% 
  summarize(Total_trip_duration= sum(ride_length))

Trips_2022_v2 %>% 
  group_by(member_casual) %>% 
  summarize(Total_trip_duration= sum(ride_length))  %>% 
  ggplot(mapping= aes(x= 'Content', y=Total_trip_duration, fill= member_casual))+
  geom_bar(stat= 'identity', 
           position ='stack', width = 1)+
  coord_polar(theta = 'y')+          
  labs(x='', y='', title = 'Overall Trip Duration: Members vs. Casual Users')+  
  theme_void()+    
  geom_text(aes(label = Total_trip_duration), position = position_stack(vjust = 0.5))
```

![image](https://github.com/Ab1w/Google-Case-Study-Cyclistic/blob/main/Aggregate%20Rides%20by%20Membership.png)
![image](https://github.com/Ab1w/Google-Case-Study-Cyclistic/blob/ab23c07e7572879f14494a557d5fdfc48c334bc9/Overall%20Trip%20Duration.png)

* Surprisingly, Annual members demonstrate a higher trips compared to casual members, while casual members spend significantly more time on bicycles than annual members.


## Ride Count by Day of the Week by Members and Casual Users 

```
# Comparing the Number of Rides for Casual users and Members by Day of the Week
Trips_2022_v2$Day_of_Week <- ordered(Trips_2022_v2$Day_of_Week, 
                                     levels= c("Sunday", "Monday", "Tuesday", "Wednesday",
                                               "Thursday", "Friday", "Saturday"))

Trips_2022_v2 %>% 
  group_by(member_casual, Day_of_Week) %>% 
  summarize(number_of_ride =n(), .groups = "keep") %>% 
  arrange(Day_of_Week, member_casual)

Trips_2022_v2 %>% 
  group_by(member_casual, Day_of_Week) %>% 
  summarize(number_of_ride =n(), .groups = "keep") %>% 
ggplot(mapping = aes(x=Day_of_Week, y= number_of_ride, fill=member_casual))+
  geom_bar(position = "dodge", stat="identity")+
  labs(title = "Ride Count by Day of the Week: Members vs. Casual Users", 
       x="Days of Week", y="Number of ride")
```
![image](https://github.com/Ab1w/Google-Case-Study-Cyclistic/blob/ab23c07e7572879f14494a557d5fdfc48c334bc9/Ride%20Count%20by%20Day%20of%20the%20Week.png)

We found that weekly ridership for annual memberships remains relatively consistent throughout the weekdays but experiences a significant decrease over the weekend, especially on Sundays.
Weekly ridership among casual users is relatively low during weekdays but experiences a substantial increase during weekends, reaching its peak on Saturdays.

### Average Trip Duration by Day of the Week by Members and Casual Users 

```
# Comparing the Average Ride Duration by Day of the Week by Members and Casual Users 
Trips_2022_v2 %>% 
  group_by(member_casual, Day_of_Week) %>% 
  summarize(number_of_ride =n(),
            average_duration=mean(ride_length), .groups = 'keep') %>% 
  ggplot(mapping = aes(x=Day_of_Week, y=average_duration, fill=member_casual))+
  geom_bar(position = "dodge", stat="identity")+
  labs(title = 'Average Ride Duration: Members vs. Casual Users', y= 'Average_duration')
```

![image](https://github.com/Ab1w/Google-Case-Study-Cyclistic/blob/ab23c07e7572879f14494a557d5fdfc48c334bc9/Average%20Ride%20Duration.png)


The result indicates that the average trip duration for casual members is nearly twice as long as that of annual members over the span of one week. Based on this finding, it is reasonable to assume that annual members predominantly utilize bicycles for commuting purposes, while temporary members primarily opt for bicycles for sports or other leisure activities.

### Total Rides by Day of the Week by Members and Casual Users (Per Hour)

```
# Comparing Total Rides by Day of the Week by Members and Casual Users (Per Hour)
Trips_2022_v2 %>% 
  group_by(member_casual, Hours, Day_of_Week) %>% 
  summarize(number_of_ride =n(),
            .groups='keep') %>% 
  ggplot(mapping = aes(x=Hours, y=number_of_ride, fill=member_casual))+
  geom_bar(position = 'dodge', stat= 'identity')+
  labs(title = 'Total Rides by Day of the Week: Members vs. Casual Users (Per Hour)', y='Number of Rides')+
  facet_wrap(~Day_of_Week)
```
![image](https://github.com/Ab1w/Google-Case-Study-Cyclistic/blob/ab23c07e7572879f14494a557d5fdfc48c334bc9/Total%20Rides%20by%20Day%20of%20the%20Week.png)

The graph from above provides evidence that supports our hypothesis. It demonstrates a steady rise in usage during the weekday mornings, specifically between 6am and 8am, with the highest point reached at 8am. Moreover, the graph illustrates a consistent growth in usage from 3pm to 5pm, peaking at 5pm. Both trends align with the commuting time frame of office workers. These two trends consistently align with the specified commuting time period for office workers.

## Frequency of each rideable_type 

```
# Compare the frequency of each rideable_type.
Trips_2022_v2 %>% 
  group_by(member_casual,rideable_type) %>% 
  summarize(number_of_ride =n(), .groups = "keep") %>% 
  ggplot(mapping = aes(x=rideable_type, y=number_of_ride, fill=member_casual))+
  geom_bar(stat = 'identity', position = 'dodge')+
  labs(title = 'Number of Rides by Rideable Type and Member vs. Causal User', x= 'Rideable Type',
       y= 'Number of Rides')
```

![image](https://github.com/Ab1w/Google-Case-Study-Cyclistic/blob/ab23c07e7572879f14494a557d5fdfc48c334bc9/Number%20of%20Rides%20by%20Rideable%20Type%20and%20Member.png)

We found that annual members prefer classic and electric bikes but have no interest in docked bikes. On the other hand, casual users mostly prefer electric bikes, followed by classic bikes, but only small portion of them show a preference for docked bikes.

### Monthly Ride Count by Members and Casual Users

```
# Comparing the Number of Rides for Casual Users and Members by Each Month

Trips_2022_v2 %>% 
  group_by(member_casual, Month) %>% 
  summarize(number_of_ride =n(), .groups = 'keep') %>% 
  ggplot(mapping = aes(x=Month, y=number_of_ride, fill=member_casual))+
  geom_bar(position = 'dodge', stat='identity')+
  labs(title= "Monthly Ride Count by Members and Casual Users")
```

![image](https://github.com/Ab1w/Google-Case-Study-Cyclistic/blob/ab23c07e7572879f14494a557d5fdfc48c334bc9/Monthly%20Ride%20Count%20by%20Members%20and%20Casual%20Users.png)


we found that the ridership of both annual members and casual users experienced a recovery starting from April and continued to rise, reaching its peak in September. However, after September, there was a decline. The trends suggest a seasonal pattern in ridership, where the number of riders increases as temperatures rise (from April to September) for both members and casual users. Conversely, as temperatures drop and snowfall occurs, ridership experiences a decline.


### Act

* Conclusion

Based on our research, we have discovered several important findings. First, casual riders have a higher trip duration than members, which presents a significant opportunity to convert casual riders into members, which can greatly benefit the company. Secondly, casual riders have a higher trip duration on weekends, whereas members mainly ride on weekdays. Thirdly, we observed that only casual riders displayed interest in using docked bikes, whereas members preferred using classic bikes and electric bikes, but have not interest in docked bikes. Lastly, we identified a seasonal pattern in ridership. The number of rides starts to recover in spring, continues to increase during summer, and experiences a sharp decline in winter. The main differences between members and casual riders is that members utilize their bikes during weekday, while recreational riders primarily use their bikes on weekends. Furthermore, they also exhibit difference preferences for the type of bike.

* Recommendations

In order to develop a marketing strategy that effectively converts casual riders into annual memberships, there are some suggestion 

* 1. Considering the long durations of rides by casual riders, it would be beneficial to introduce membership discounts for long-distance cycling, such as providing the initial 5 kilometers of riding at no cost. This initiative aims to encourage more casual riders becoming members.

* 2. To attract more casual riders and encourage them to become members during the low passenger flow of winter, offering preferential monthly or quarterly membership prices can be an effective strategy.

* 3. The advertising budget should be increased during peak season (July-September) to attract more casual riders to become members. In the off-season (winter), the advertising budget should be reduced to control costs


