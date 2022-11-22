- find names of the buildings in which every individual classroom has capacity less than 100(removing duplicates)

```
select distinct building 
from classroom
where capacity<100;
```
- find name and budget  of all students of departments which have a budget<0.1 million
```
select name,budget from 
student s,department d 
where d.dept_name=s.dept_name
and d.budget<100000
```
- find names of all instructors whose dept is finance or whose dept is in 
any of the following buildings:Watson,Taylor 
```
select name from instructor i,department d
where 
i.dept_name=d.dept_name
and (d.dept_name='Finance' or d.building in ('Watson','Taylor'));
```
- find titles of all courses whose course_id has three alphabets 
indicating the department.
```
select title,course_id from course
where course_id like '___-%';
```
- list of all students in alphabetic order of departments and within
each department, in decreasing order ot total credits
```
select name,dept_name,tot_cred
from student
order by dept_name,tot_cred desc;
```
- from teaches , find the id's of all courses taught in the fall or spring of 2018
```
select course_id from teaches where semester in ('Fall','Spring')
and year=2018;
```
- for the same question,find using union operator
```
select course_id from teaches
where semester='Fall' and year=2018
union
select course_id from teaches where semester='Spring' and year =2018;
```
- find the names of all instructors who taught in either the computer science department or the finance department and whose salary is <80000

```
select name from instructor where dept_name in ('Comp. Sci.','Finance') 
and salary<80000;
```
- from classroom find names and the average capacity of each building whose average capacity is >25
```
select building,avg(capacity) from classroom 
group by building
having avg(capacity)>25;
```
- find the least salary drawn by any instructor among all the instructors
```
select min(salary) as least_salary
from instructor;
```
- find the maximum credits obtained by any student among all the students
```
select max(tot_cred) as max_credits from student
```
- from section , find number of courses run in each building
```
select count(course_id),building from section
group by building;
```
- find the total credits offered by each department
```
select sum(credits),dept_name from course 
group by dept_name;
```
- ## intermediate SQL
- nested subqueries
    - subqueries in where clause
        - for membership
        - comparisions
        - cardinality
    - find courses offered in fall 2009 and in spring 2010(intersect)
    ```
    select distinct course_id
from section
where semester='Fall' and year =2009
and 
course_id in (select course_id from section
where semester ='Spring' and year=2010);
```
    - in fall 2009 but not in spring 2010
    ```
    select distinct course_id from section
where semester='Fall' and year=2009
and course_id not in (select course_id from section
where semester='Spring' and year=2010);
```
- find total numebr of distinct students who have take course sections taught by the instructor with id=10101
```
select count(distinct id) from takes 
where (course_id,semester,year) in
(select course_id,semester,year  from teaches 
where id=10101);
```
- find names of instructors with salary greater than that of some(atleast one) instructor in Biology department
```
select t.name from instructor s,instructor t
where s.salary >t.salary and s.dept_name='Biology';
or 
select name from instructor 
where salary>some(select salary from instructor 
where dept_name='Biology');
```
- find names of all instructors whose salary is greater than the salary of all the instructors in Biology department
```
select name from instructor 
where salary>all(select salary from instructor where dept_name='Biology');
```
- using exists clause find all the courses taught in both the fall 2009 and in spring 2010
```
select course_id from section  S where semester='Fall' and year=2009 and exists (select * from section  T where semester='Spring' and year=2010 and S.course_id=T.course_id);
```
- find all the students who have taken all courses offered in bio dept
```
select distinct s.id,s.name from
student s
where not exists (
select course_id from course where dept_name ='Biology'
minus
select t.course_id from takes t where s.id=t.id);
```
- getting right parenthesis missing error,
- logic here is to see if(exists) any of the  courses in bio dept  are exempted from the courses taken by the student,if not exist , select them
- test for absence of duplicate tuples: unique
- find all courses that were offered atmost  once in 2009
```
select t.course_id
from course t
where unique (select r.course_id from section r
where t.course_id=r.course_id and r.year=2009);
```
- ## subqueries in the from clause
    - find the average instructors' salaries of those departments where the average salary is greater than 42000
    ```
     select dept_name,avg_salary 
 from (select dept_name,avg(salary) as avg_salary
 from instructor
 group by dept_name)
 where avg_salary>42000;
    ```
    - we do not need to use the having clause
   - ## with clause
        - provides a way of defining a temporary relation whose definition is available only ot the query in which the with clause occurs.
   - find all the dept with max budget
    - https://oracle-base.com/articles/misc/with-clause
    - find all dept where total salary is greater than the average if the total salary at all departments
    - need to do

- ## subqueries in select
    - list of all departments along with number of instructors in each department
    ```
    select dept_name,(select count(*) 
from instructor where department.dept_name=instructor.dept_name) num_instructor from department;
```
    - runtime error if subquery returns more than one result tuple
-
delete all tuples in instructor relation for those instructors with a dept located in Watson building
```
delete from instructor
where dept_name in (select dept_name from department where building='Watson')
```
- delete all instructors whose salary is less than the average salary of instructors
    ```
    delete from instructor
    where salary<(select avg(salary) from instructor)
    ``` 
- add all instructors to the student relation with tot_credits set to 0
```
insert into student
select id,name,dept_name,0 from instructor
```
- insert salaries of instructors where salary is over 100000 by 3% and all others by a 5%
```
update instructor
    set salary=salary*1.03 where salary>100000;
update instructor
    set salary=salary*1.05
    where salary<=100000;
    ```
- ### case statemet
```
update instructor
set salary=case
when salary<=100000
then salary*1.05
else salary*1.03
end
```
- # joins
    - cross join 
    ```
    select * from employee cross join department
    ```
    or
    ```
    select * from employee,department;
    ```
    - inner join only if they exist clearly in both
    - will have l+r columns (l-left table , r- right table including the common table)
    ```
    course inner join prereq
    ```
    - outer join
        - avoids the loss of information
        - computes the join and then add tuples
        - left outer join
            - makes sure all of a is covered
            - https://youtu.be/_-JZS7mqo6k?t=664
        - right outer join
        - natural full outer join -> all info preserved
    - ## views
        - a view of instructors without their salary
        ```
        create view faculty as
        select id,name,dept_name
        from instructor
        ```
        - find all instructors in biology department
        ```
        select name from faculty
        where dept_name='Biology'
        ```
        - create a view of department salary totals
        ```
        create view departments_total_salary(dept_name,total_salary) as select dept_name,sum(salary)
        from instructor
        group by dept_name;
        ```
        - can create views using other views
        ```
        create view physics_fall_2009 as
        select course.course_id,sec_id,building ,room_number
        from course,section
        where course.course_id=section.course_id
        and course.dept_name='Physics'
        and section.semester='Fall'
        and section.year='2009'
        ```
        ```
        create view physics_fall_2009_watson as select
        course_id,room_number from 
        physics_fall_2009
        where building='Watson';
        ```
- materialize a view -> means create a new table with all tuples in the result
- if relations used in query are updated, the materialized view result becomes out of date.
- need to maintain the view by updating the view whenever the underlying relations are updated.
- ## transaction
    - create table with check(p)
    - ensure that semester is one of fall,winter,spring or summer
    ```
    create table section1(
  course_id varchar(8),
  sec_id varchar(8),
  semester varchar(6),
  year numeric(4,0),
  building varchar(15),
  room_number varchar(7),
  time_slot_id varchar(4),
  primary key(course_id,sec_id,semester,year),
  check (semester in ('Fall','Winter','Spring','Summer'))
)
    )
    ```
    - cascading actions in referential integrity
    ```
    create table course(
        course_id char(5) primary key,
        title varchar(20),
        dept_name varchar(20) references department
    )
    create table course(
        ...
        dept_name varchar(20),
        foreign key (dept_name) references department
        on delete cascade
        on update cascade,
    )
    ```
    alternative actions to cascade: no action,set null,set default
    - ## index creation
    ```
    create table student(
        ID varchar(5),
        name varchar(20) not null,
        dept_name varchar(20),
        tot_cred numeric(3,0) default 0,
        primary key (id))
    ```
    ```
    create index studentid_index on student(ID)
    ```
    - indices are data structures used to speed up access to records with specified values for index attributes
    ```
    select * from student
    where id='12345'
    ```
    - can be executed by using index to find the required record without looking at all records of student
- User defined types
    ### learn more on this
    - create type construct in SQL creates user-defined type (alias, like typedef in C)
    ```
    create type dollars as numeric(!2,2) final
    ```
    ```
    create table department(
        dept_name varchar(20),
        building varchar(15),
        budget dollars
    );
    ```
    - ## authorization
        - read 
        - insert
        - update
        - delete
    - for modify the database schema
            - index
            - resources
            - alteration
            - drop
     - privileges 
        - select
        - insert
        - update
        - delete
        - all privileges
    - revoke authorization
        - 
        ```
        revoke select on branch from u1,u2,u3
        ```
    - role
        - `create role instructor`
        - `grant instructor to amit`
        - privileges can be granted to roles:
            - `grant select on takes to instructor`
        - roles can be granted to users as well as other roles
            - `create role teaching_assistant
            grant teaching_assistant to instructor`
            - instructor inherits all privilages of teaching assistant
        - chain of roles
            - create role dean;
            - grant instructor to dean;
            - grant dean to Satoshi;
    - Authorization on views
        - 
        ```
        create view geo_instructor as
        (select * from instructor
        where dept_name='Geology');
        grant select on geo_instructor to geo_staff;
        ```
    - transfer of privileges
        ```
        grant select on department to Amit with grant option
        ```