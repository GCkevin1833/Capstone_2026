-- create database finance database
create database finance;
-- Use database
use finance;
-- Created table for credit card data
create table cc_data (index_id INT not null primary key
,trans_date_trans_time VARCHAR(100)
,cc_num BIGINT
,merchant VARCHAR(50)
,category VARCHAR(50)
,amt DECIMAL(10,2)
,first_name VARCHAR(50)
,last_name VARCHAR(50)
,gender VARCHAR(50)
,street VARCHAR(50)
,city VARCHAR(50)
,state VARCHAR(50)
,zip INT
,lat INT
,longitude INT
,city_pop INT
,job VARCHAR(225)
,dob VARCHAR(50)
,trans_num VARCHAR(50)
,unix_time BIGINT
,merch_lat DECIMAL(9,6)
,merch_long DECIMAL(9,6)
,is_fraud TINYINT(1)
);

-- Loaded csv into table

load data infile "C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/cc_data.csv"
into table cc_data
FIELDS terminated by ','
enclosed by '"'
lines terminated by '\n'
ignore 1 rows
(index_id, trans_date_trans_time, cc_num, merchant, category, amt, first_name, last_name, gender, street, city, state, zip, 
lat, longitude, city_pop, job ,dob, trans_num, unix_time, merch_lat, merch_long, is_fraud);

-- Data check

select * from cc_data;
select count(*) from cc_data;
select count(distinct cc_num) from cc_data;

-- Created table for location

create table location (location_id INT primary key AUTO_INCREMENT
,cc_num BIGINT
,lat DECIMAL(9,6)
,longitude DECIMAL(9,6)
);

-- Imported the CSV file

load data infile "C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/location_data.csv"
into table location
FIELDS terminated by ','
enclosed by '"'
lines terminated by '\n'
ignore 1 rows
(cc_num, lat, longitude);

-- Data Check
select * from location;

-- Cleaning Data types
select c.trans_date_trans_time,
c.dob
from cc_data c;
-- changing my trans_date_trans_time from VARCHAR to DATETIME

-- Creating a new column
alter table cc_data
add column trans_datetime DATETIME;

-- Updating the column to have trans_date_trans_time in DATETIME FORMAT
update cc_data
set trans_datetime = STR_TO_DATE(trans_date_trans_time, '%d-%m-%Y %H:%i');

-- Checking if it was done correctly
select c.trans_date_trans_time,
c.trans_datetime
from cc_data c;

-- Dropping the old column in the data table
alter table cc_data drop column trans_date_trans_time;


-- Renaming the new column to Trans_date_trans_time
alter table cc_data rename column trans_datetime to trans_date_trans_time;

-- Repeating the process for dob
alter table cc_data
add date_of_birth DATE;

update cc_data
set date_of_birth = STR_TO_DATE(dob, '%d-%m-%Y' );

select c.date_of_birth
from cc_data c;

alter table cc_data drop column dob;

alter table cc_data
rename column date_of_birth to dob;

select c.dob
from cc_data c;

select*
from cc_data;

-- Calculate the total number of transactions in the cc_data table

select count(distinct c.trans_num)
from cc_data c;


-- Identify the top 10 most frequent merchants in the cc_data table

select c.merchant
,count(c.merchant)
from cc_data c
group by c.merchant
order by count(c.merchant) desc
limit 10;

-- Find the average transaction amount for each category of transactions in the cc_data table

select c.category
,avg(c.amt) as Avg_Transaction
from cc_data c
group by c.category
order by avg(c.amt) desc;

-- Determine the number of fradulent transactions and the percentage of total transactions they represent

select count(case when c.is_fraud = 1 then 1 END)as Fradulent_Transactions
,count(*) as Total_transactions
,round(count(case when c.is_fraud = 1 then 1 END) / COUNT(*) * 100,2) as Fraud_percentage
from cc_data c;


-- Checking for duplicates cc_num in locations table
select cc_num
,count(cc_num)
from location
group by cc_num
having count(cc_num) > 1;

-- Checking for duplicates in the index_id(primary key) cc_data table
select index_id
,count(index_id)
from cc_data
group by index_id
having count(index_id) > 1;

-- Checking for any blanks in the cc_data table
select count(*)
from cc_data
where index_id is null;

-- Checking for distinct index_id matches rows in cc_data table
select count(distinct index_id) as Unique_index
,count(index_id) as Index_id_total
from cc_data;

-- Comparing total distinct cc_num to total rows in location table
select count(distinct l.cc_num) as Unique_CC_num
,count(l.cc_num) as Total_CC_num
from location l;

-- Checking for any blanks in the cc_num column in the location table
select count(*)
from location 
where cc_num is null;

-- Comparing the distinct cc_num to rows in the cc_data table
select count(distinct cc_num)
,count(cc_num)
from cc_data;

-- Attempting to join both cc_data table w location table
select c.index_id
,c.cc_num as credit_card
,c.merchant as store
,c.category as category
,c.amt as amount_spent
,concat(c.first_name,' ',c.last_name) as name
,c.gender as gender
,c.street as street
,c.city as city
,c.state as state
,c.zip as zip
,c.lat as latitude
,c.longitude as longitude
,c.city_pop as city_population
,c.job as job
,c.trans_num as transaction_number
,c.merch_lat as store_lat
,c.merch_long as store_long
,c.is_fraud as fraud
,c.trans_date_trans_time as transaction_date_time
,c.dob as date_of_birth
,l.lat as latitude_l
,l.longitude as longitude_l
from cc_data c
left join location l on l.cc_num = c.cc_num;


-- Checking the unique credit card holder locations in the cc_data table
select count(distinct concat(lat,' ',longitude)) as unique_locations
from cc_data;

-- Checking the unique credit card holder locations in the location table
select count(distinct concat(lat,' ',longitude)) as unique_locations
from location;

-- Data check to see if there is repeating cc_num with lat/longitude in the location table
select *,concat(lat,' ',longitude), row_number() over (partition by cc_num,concat(lat,' ',longitude) order by cc_num) as row_num
from location
order by cc_num;

-- Checking to see if there is any correlation between the cc_data table and the location table
select distinct c.trans_num
,c.cc_num as ccnum_data
,l.cc_num as ccnum_lc
,concat(c.lat,' ',c.longitude) as cloc
,concat(l.lat,' ',l.longitude) as lloc
,c.is_fraud
,c.city
,c.merchant
,c.street
,c.zip
,c.state
,c.city_pop
,c.job
from cc_data c
left join location l on l.cc_num = c.cc_num
where is_fraud = 1
order by c.cc_num asc;

-- Confirming that a cc_num is tied to two different locations
select *
from location
where cc_num = 3529600000000000;
