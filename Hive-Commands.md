# Example 1 - Create a databse containing the records of students of university Using External and managed tables 
```sql
create database record;

show databases;

use record;

create table student(rno int, name string, city string) row format delimited fields terminated by ',';

show tables;
```
Shell Command
```
hadoop fs -ls /user/hive/warehouse/record.db
```
```
LOAD DATA LOCAL INPATH '/home/cloudera/input.txt' INTO TABLE student;

select * from student;

describe student;

describe extended student;
```
## Shell commands to display data of hive tables from HDFS
```
hadoop fs -mkdir /hive_demo/
hadoop fs -put external-input.txt  /hive_demo/
hadoop fs -ls /hive_demo/
hadoop fs -cat /hive_demo/external-input.txt
```
# Example 2 - Create a students external table and insert data into hdfs to display in table  
```
create external table student_external (rno int, name string, city string) row format delimited fields terminated by ',' LOCATION '/hive_demo/';
select * from student_external;
```
Shell Command
```
hadoop fs -put input2.txt  /hive_demo/
hadoop fs -put input.txt  /hive_demo/
hadoop fs -ls  /hive_demo/
hadoop fs -put input3.txt  /hive_demo/
```
```
select * from student_external;
select * from student_external where rno=104;
```

## Example -2 Creating a Database of University Students using external and managed tables

```
create database university;
use university;
create table students(rno int, name string, branch string, section string) row format delimited fields terminated by ',';
describe students;
```

```
hadoop fs -ls /user/hive/warehouse/
hadoop fs -ls /user/hive/warehouse/university.db
cat input-data.txt
```
```
LOAD DATA LOCAL INPATH '/home/cloudera/input-data.txt' INTO TABLE students;
select * from students;

create table studentsbybranch(rno int,name string,section string) partitioned by (branch string) clustered by (section) into 2 buckets row format delimited fields terminated by ',';
```
```
set hive.exec.dynamic.partition.mode=nonstrict;
set hive.exec.dynamic.partition=true;
set hive.enforce.bucketing=true;
```
```
from students insert overwrite table studentsbybranch partition(branch) select s.rno,s.name,s.section,s.branch distribute by branch;

hadoop fs -ls /user/hive/warehouse/university.db
hadoop fs -ls /user/hive/warehouse/university.db/studentsbybranch
hadoop fs -ls /user/hive/warehouse/university.db/studentsbybranch/branch=cse
hadoop fs -cat /user/hive/warehouse/university.db/studentsbybranch/branch=cse/000000_0
hadoop fs -cat /user/hive/warehouse/university.db/studentsbybranch/branch=cse/000001_0
```
```
select * from studentsbybranch limit 6;
```
# Example -3 Creating Mailid and Employee External tables
```
create table mailid(name string, email string) row format delimited fields terminated by ',';

load data local inpath '/home/cloudera/email.txt' into table mailid;

create table employee(name string, salary float, city string) row format delimited fields terminated by ',';

load data local inpath '/home/cloudera/emp.txt' into table employee;
```
```
select * from employee;
select * from mailid;
```
```
select e.name, e.city,e.salary,m.email from employee e join mailid m on e.name=m.name
select e.name, e.city,e.salary,m.email from employee e left join mailid m on e.name=m.name
select e.name, e.city,e.salary,m.email from employee e full outer join mailid m on e.name=m.name;
```
