### create a table instructor
```
create table instructor(
id char(5),
name varchar2(20),
dept_name varchar(20),
salary numeric(8,2));
```

### create a table instructor with constraints
```
create table instructor(
    id char(5),
    name varchar(20) not null,
    dept_name varchar2(20),
    salary numberic(8,2),
    primary key (id),
    foreign key (dept_name) references department);
```

### add a column to the tabel
```
alter table instructor add email varchar2(20) not null;
```

### drop a column from  the table
```
alter table instructor drop column email;
```
- find names of all instructors:
```
select name from instructor;
```
- find department names  of all instructors and remove duplicates;
```
select distinct dept_name from instructor;
```
- the keyword all specifies that the duplicates should not be removed.
```
select all dept_name
from instructor
```
- get instructor details as monthly salary
```
select id,name,salary/12 from instructor;
```
- renaming the same as monthly salary
```
select id,name,salary/12 as monthly_salary from instructor;
```
- find all instructors in comp.sci dept
```
select name,dept_name from instructor
where dept_name='Comp. Sci.';
```
- name and dept_name is comp sci and sal >80000
```
select name,dept_name from instructor
where dept_name='Comp. Sci.' and salary >80000; 
```
- find names of all instructors who have taught some course and the course_id (natural join)
```
select name,course_id
from instructor,teaches
where instructor.id=teaches.id
```
- find names of all instructors in art department who have taught some course and the course_id (natural join)
```
select name,course_id
from instructor,teaches
where instructor.id=teaches.id and instructor.dept_name='Art';
```
- find names of all the instructors who have a higher salary than some instructor in 'Comp. Sci.'
```
select distinct t.name,t.salary from instructor  t,
instructor  s
where t.salary>s.salary and s.dept_name='Comp. Sci.';
```
- values for emp_super
```
create table emp_super(
person varchar2(20),
supervisor varchar2(20));
 insert into emp_super VALUES('bob','alice');
 insert into emp_super VALUES('mary','susan');
 insert into emp_super VALUES('alice','david');
 insert into emp_super VALUES('david','mary');
 ```
 - find supervisor of bob
 ```
select supervisor from emp_super where person='bob';
```
- find supervisor of supervisor of bob
- find all the supervisors(direct and indirect of bob)
- list in alphabetic order the names of all instructors
```
select distinct name
from instructor
order by name;
```
- sorting data on multiple attributes
```
select distinct dept_name,name
from instructor
order by dept_name,name;
```
- dept_name ascending and name descending
```
select distinct dept_name,name
from instructor
order by dept_name,name desc;
```
- dept_name in descending and name by asc 
```
select distinct dept_name,name
from instructor
order by dept_name desc,name;
```
- selecting number of tuples in output
```
select * from instructor fetch first 10 rows only;
```
- find names of all the instructor with salary between 90,000 and 100000
(90,000<=x<=100,000)
```
select name from instructor where salary between 90000 and 100000;
```
- in operator
```
select name from instructor 
where dept_name in ('Comp. Sci.','Biology');
```
- find courses in fall 2017 and spring 2018
```
select course_id,semester,year from section where semester='Fall' and year=2017
union
select course_id,semester,year from section where semester='Spring' and year=2018
```
- courses ran in fall 2017 and spring 2018 
```
select course_id from section where semester='Fall' and year=2017
intersect
select course_id from section where semester='Spring' and year=2018;
```
- courses in fall 2017 but not in spring 2018
```
(select course_id from section where semester='Fall' and year=2017)
minus
select course_id from section where semester='Spring' and year=2018;
```
- find the largest salary of all instructors
    - find salary that are less than the largest salary
    ```
    select distinct t.salary
    from instructor as t.instructor as s
    where t.salary<s.salary
    ```
    - find all the distinct salaries
    ```
    select distinct salary from instructor
    ```
    - answer = (2) - (1)
   or  
```
select max(salary) from instructors
```
- get average salary of instructors in comp.sci dept
```
select avg(salary) from instructor
where dept_name='Comp. Sci.'
```
- select total number of instructors who teach a course in spring 2018
```
select count(distinct id) from teaches where semester='Spring' and year=2018;
```
- find the number of tuples in course relation
```
select count(*)
from courses
```
- find average salary of instructors in each department
```
select dept_name,avg(salary) avg_salary from instructor
group by dept_name; 
```
- ## NOTE : attributes in select clause of aggregate function must appear in group list. the following query is wrong
```
select dept_name,id,avg(salary)
from instructor
group by dept_name;
```
- ### this is correct because we have included the id in group by clause
```
select dept_name,id,avg(salary)
from instructor
group by dept_name,id;
```
- find the names and average salaries of all departments whose average salary is greater than 42000
```
select dept_name,id,avg(salary)
from instructor
group by dept_name
having avg(salary)>42000;
```
    - where clause will apply condition on each row of the statement
    whereas having clause will put the condition after the grouping is done.
    - NOTE : predicate in having clause are applied after the formation of groups whereas predicates in the where clause are applied before forming groups.

- total all salaries
    ```
    select sum(salary)
    from instructor;
    ```
    - this ignores null amounts
    - result is null if there is no non-null amount
    - sum will ignore null
    - all aggregate except count(*) ignore tuples with null values
    - what if collection has only null values?
    - count returns 0, ie if we do count(is) and id had all the values as null, it returns 0.
    - all other aggregates returns null
- null table
```
create table nulltable (
id number,
val number
);
insert into nulltable values (1,null);
insert into nulltable values (2,null);
insert into nulltable values (3,null);
insert into nulltable values (4,null);
```
- for the above table count(*) is 4,sum(val)=null
- if we insert a tuple (null,4)
    - the sum becomes 4
    - count(*) becomes 5