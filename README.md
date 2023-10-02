
How Does a Bike-Share Navigate Speedy Success?
 

This is my final project to get the Data Analytics certification from Coursera. I will be using the data of the fictional company Cyclistic. This is public data that I can use to explore how different customer types are using Cyclistic bikes.

Tools
Microsoft SQL, Tableua.

About the company 
In 2016, Cyclistic launched a successful bike-share offering. Since then, the program has grown to a fleet of 5,824 bicycles that are geo tracked and locked into a network of 692 stations across Chicago. The bikes can be unlocked from one station and returned to any other station in the system anytime.

The project will consist of the following steps:
Ask, prepare, process, analyze, share, act.

Ask
How do annual members and casual riders use Cyclistic bikes differently? 
Why would casual riders buy Cyclistic annual memberships? 

Prepare
I downloaded data from https://divvy-tripdata.s3.amazonaws.com/index.html. 
I am using 12 months of Cyclistic data starting from March 2022 to February 2023. Type of these files is CSV and I imported it into SQL Server. It is important to make sure that all imported data types are of the same type in each file.
My data set consist of the following:
-	ride_id, type nvarchar(255)
-	rideable,_type type nvarchar(255)
-	started_at, type datetime
-	ended_at, type datetime
-	start_station_name, type nvarchar(255)
-	start_station_id, type nvarchar(255)
-	end_station_id, type nvarchar(255)
-	end_station_name, type nvarchar(255)
-	end_station_id, type nvarchar(255)
-	start_lat, type nvarchar(255)
-	start_lng, type nvarchar(255)
-	end_lng, type nvarchar(255)
-	member_casual, type nvarchar(255)


Merging all the tables into a single one

SELECT *
  INTO  bike 
FROM
(
        SELECT     *
    FROM         [dbo].['202203-divvy-tripdata$']
    UNION
    SELECT     *
    FROM         [dbo].['202204-divvy-tripdata$']
    UNION
    SELECT     *
    FROM         [dbo].['202205-divvy-tripdata$']
    UNION
    SELECT     *
    FROM         [dbo].['202206-divvy-tripdata$']
    UNION
    SELECT     *
    FROM         [dbo].['202207-divvy-tripdata$']
	UNION
	SELECT     *
    FROM         [dbo].['202208-divvy-tripdata$']
	UNION
	SELECT     *
    FROM         [dbo].['202209-divvy-publictripdata$']
	UNION
	SELECT     *
    FROM         [dbo].['202210-divvy-tripdata$']
	UNION
	SELECT     *
    FROM         [dbo].['202211-divvy-tripdata$']
	UNION
	SELECT     *
    FROM         [dbo].['202212-divvy-tripdata$']
	UNION
	SELECT     *
    FROM         [dbo].['202301-divvy-tripdata$']
	UNION
	SELECT     *
    FROM         [dbo].['202302-divvy-tripdata$']
	) a

Process data
 Сheck if the table is created
         select * from bike
Сleaning all data where end time less than start time or equal
      
         Delete
	  from bike
	  where ended_at<=started_at

 Cleaning the data where there is no ride_id, no information on coordinates (lat and lng), rideable type or customer type is not specified. I decided to leave the data where there is no information on the station (start and end), if the rows have information about date and time, customer type and station coordinates.
             
       Delete
	from bike
	where ride_id is NULL 
	or rideable_type is null 
	or started_at is null 
	or ended_at is null 
	or start_lat is null 
	or start_lng is null 
	or end_lat is null 
	or end_lng is null
	or member_casual is null

I will not use information from start_station_id and end_station_id

	Alter table bike drop column start_station_id 
	Alter table bike drop column end_station_id 

I will change the data type in latitude and longitude from nvarchar to float for later use in Tableua

	alter table bike
	alter column start_lat float
	alter table bike
	alter column end_lat float
       alter table bike
	alter column start_lng float
	alter table bike
	alter column end_lng float

The table containes 786366 rows in total. I will check if there are duplicate ride_id

	select distinct(ride_id)
	from bike
	-- or
	
	select ride_id, count(*)
	from bike
	group by ride_id
	having count(*)>1
	-- 
No duplicate identified.
Display all the data that is I require for further steps.


	select ride_id, rideable_type, started_at, convert(char(10),started_at, 104) as started_date, DATEPART(MONTH, started_at) as 'month',
	datediff(minute, started_at, ended_at) as trip_duration, DATENAME(DW, started_at) as day_of_the_week, start_station_name, end_station_name, 
	start_lat, start_lng, end_lat, end_lng, member_casual
	from bike

At this point, I successfully processed the data into SQL and move to Tableau.

Analyze
To get more detailed information about clients, I created calculated fields in Tableau. Which includes the count of all riders, the number of members, the number of casuals. 
I also calculated the duration of the trip using the coordinates. 





 

 



 


Then, I calculated the average trip_duration and average trip_lenth.
The data is now ready to be visualized.

Share

I created two dashboards that I can share with others. These dashboards are interactive and available through the following links.

https://public.tableau.com/app/profile/mariia.anokhina/viz/myproject1part1/HowdoesabikesharecompanynavigatespeedysuccessPart1?publish=yes


https://public.tableau.com/app/profile/mariia.anokhina/viz/myproject1part2/Dashboard2?publish=yes 
 


 


 
Summary
The total number of riders is 786 386.  Of these, 272 133 (34.61%) are casuals and 514 233 (65.39%) are members. 
The most popular types of bikes are the classic bike and electric bike. The docked bike is in demand only among casual riders. Also electric bikes are more popular among casuals than among members. 
If we look at the difference in trip duration, we will see that average members spend around 11.84 minutes to travel and casual riders spend around 24.69 minutes. If we look at the average distance, we see members travel 1.86 miles when casuals travel around 10.08 miles.
Now look at the distribution of riders by day of the week. We find that members use the bike more often between Monday and Friday, and whereas casuals are just the opposite. They have a higher demand on weekends. 
Now let’s look at the distribution of riders by months. Between May and September, I can observe a drop in demand among members. As a demand among casuals on the contrary increases during the same period. 
The most popular start and end stations among members and casuals are not the same. Which tells us that they prefer different locations. 
Since members use bikes more on weekdays for shorter distances and use bikes throughout the year except for the months that are popular for vacations, I can conclude by saying that they use bikes to commute to work and home. Whereas casuals use bikes for travelling for longer distance. 

Act

For the possible transformation of the casual into the member, I suggest the following actions:
1)	Offer casuals a discount on their second trip if they use it on weekdays.
2)	Increase the cost for casuals for summer rides offering them to change to buy a one-year pass with the same fare, regardless of the month. 
3)	Since casuals use bikes for longer distance, add fee after a certain mileage or time. At the same time, keep the fee the same for members. 






