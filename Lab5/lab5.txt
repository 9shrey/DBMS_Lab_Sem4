@"C:\Users\ugcse.PG-CP.000\Desktop\Shrey_AIML_A2_45\DBS_Lab\Lab5\company.sql"

-- 1.Retrieve the birth date and address of the employee(s) whose name is ‘John B. Smith’.
select bdate, address from employee where fname='John' and minit='B' and lname='Smith';

BDATE     ADDRESS
--------- ------------------------------
09-JAN-65 731 Fondren, Houston, TX

-- 2. Retrieve the name and address of all employees who work for the ‘Research’ department.
select fname, minit, lname, address from employee natural join department where dname='Research';

FNAME           M LNAME           ADDRESS
--------------- - --------------- ------------------------------
Richard         K Marini          98 Oak Forest, Katy, TX
John            B Smith           731 Fondren, Houston, TX
Franklin        T Wong            638 Voss, Houston, TX
Alicia          J Zelaya          3321 Castle, Spring, TX
Jennifer        S Wallace         291 Berry, Bellaire, TX
Ramesh          K Narayan         975 Fire Oak, Humble, TX
Joyce           A English         5631 Rice, Houston, TX
Ahmad           V Jabbar          980 Dallas, Houston, TX
James           E Borg            450 Stone, Houston, TX

9 rows selected.

-- 3.For every project located in ‘Stafford’, list the project number, the controlling department number, and the
-- department manager’s last name, address, and birth date.
select pnumber, dnumber, lname, address, bdate from (Project join Dept_locations on Project.plocation=Dept_locations.dlocation) join 
(Department join Employee on Department.mgr_ssn=Employee.ssn) using(dnumber)
where plocation='Stafford';

   PNUMBER    DNUMBER LNAME           ADDRESS
    BDATE
---------- ---------- --------------- ------------------------------ ---------
        10          4 Wallace         291 Berry, Bellaire, TX         20-JUN-41

-- 4.For each employee, retrieve the employee’s first and last name and the first and last name of his or her
-- immediate supervisor.
Select T.lname as supervisor_lname, T.fname as supervisor_fname, S.lname as employee_lname, S.fname as
employee_fname from employee T,employee S where T.ssn=S.super_ssn;

SUPERVISOR_LNAM SUPERVISOR_FNAM EMPLOYEE_LNAME  EMPLOYEE_FNAME
--------------- --------------- --------------- ---------------
Marini          Richard         Marini          Richard
Wong            Franklin        Smith           John
Borg            James           Wong            Franklin
Wallace         Jennifer        Zelaya          Alicia
Borg            James           Wallace         Jennifer
Wong            Franklin        Narayan         Ramesh
Wong            Franklin        English         Joyce
Wallace         Jennifer        Jabbar          Ahmad

8 rows selected.

-- 5.Make a list of all project numbers for projects that involve an employee whose last name is ‘Smith’, either as a
-- worker or as a manager of the department that controls the project.
select pnumber from Project where pnumber in (select dnumber from Department where mgr_ssn in (select ssn from Employee
where lname='Smith')) or pnumber in (select pnumber from works_on where essn in (select ssn from Employee where
lname='Smith'));

   PNUMBER
----------
         1
         2
         3
        10
        20
        30

6 rows selected.

-- 6.Show the resulting salaries if every employee working on the ‘ProductX’ project is given a 10 percent raise.
select fname, lname, salary*1.01, minit, ssn from employee where ssn in (Select essn from (works_on join project on Works_on.pno = project.pnumber) where pname='ProductX');

FNAME           LNAME           SALARY*1.01 M SSN
--------------- --------------- ----------- - ---------
John            Smith                 30300 B 123456789
Joyce           English               25250 A 453453453

-- 7.Retrieve a list of employees and the projects they are working on, ordered by department and, within each
-- department, ordered alphabetically by last name, then first name.
Select fname, lname, employee.dno, pname, project.pnumber from (employee join works_on on works_on.essn=employee.ssn) join project on
works_on.pno=project.pnumber order by employee.dno, fname, lname;

FNAME           LNAME                  DNO PNAME
     PNUMBER
--------------- --------------- ---------- --------------- ----------
Ahmad           Jabbar                   4 Computerization          10
Ahmad           Jabbar                   4 Newbenefits     30
Alicia          Zelaya                   4 Computerization          10
Alicia          Zelaya                   4 Newbenefits     30
James           Borg                     4 Reorganization   20
Jennifer        Wallace                  4 Reorganization   20
Jennifer        Wallace                  4 Newbenefits     30
Franklin        Wong                     5 Computerization          10
Franklin        Wong                     5 Reorganization   20
Franklin        Wong                     5 ProductY         2
Franklin        Wong                     5 ProductZ         3

FNAME           LNAME                  DNO PNAME
     PNUMBER
--------------- --------------- ---------- --------------- ----------
John            Smith                    5 ProductX         1
John            Smith                    5 ProductY         2
Joyce           English                  5 ProductX         1
Joyce           English                  5 ProductY         2
Ramesh          Narayan                  5 ProductZ         3

16 rows selected.

-- 8.Retrieve the name of each employee who has a dependent with the same first name and is the same sex as the
-- employee.
select fname, lname from Employee join Dependent using(sex) where Employee.fname = Dependent.Dependent_name;

no rows selected

-- 9.Retrieve the names of employees who have no dependents.
select fname, lname from employee where ssn not in (select essn from dependent);

FNAME           LNAME
--------------- ---------------
Richard         Marini
Ahmad           Jabbar
Alicia          Zelaya
James           Borg
Joyce           English
Ramesh          Narayan

6 rows selected.

-- 10.List the names of managers who have at least one dependent.
select fname, lname from Employee where ssn in (select essn from Dependent) and ssn in (select mgr_ssn from
Department);

FNAME           LNAME
--------------- ---------------
Franklin        Wong
Jennifer        Wallace

-- 11.Find the sum of the salaries of all employees, the maximum salary, the minimum salary, and the average
-- salary.
Select max(salary), sum(salary), min(salary), avg(salary) from employee;

MAX(SALARY) SUM(SALARY) MIN(SALARY) AVG(SALARY)
----------- ----------- ----------- -----------
      55000      318000       25000  35333.3333

-- 12.For each project, retrieve the project number, the project name, and the number of employees who work on
-- that project.
select Project.pnumber, pname, count (*) from Project join Works_on on Project.pnumber = Works_on.pno group by
project.pnumber, pname order by project.pnumber;

   PNUMBER PNAME             COUNT(*)
---------- --------------- ----------
         1 ProductX                 2
         2 ProductY                 3
         3 ProductZ                 2
        10 Computerization          3
        20 Reorganization           3
        30 Newbenefits              3

6 rows selected.

-- 13.For each project on which more than two employees work, retrieve the project number, the project name, and
-- the number of employees who work on the project.
select Project.pnumber, pname, count (*) from Project join Works_on on Project.pnumber = Works_on.pno group by
project.pnumber, pname having count(*)>2 order by project.pnumber;

   PNUMBER PNAME             COUNT(*)
---------- --------------- ----------
         2 ProductY                 3
        10 Computerization          3
        20 Reorganization           3
        30 Newbenefits              3

-- 14.For each department that has more than five employees, retrieve the department number and the number of
-- its employees who are making more than 40,000.
select dname, count(*) from Department, employee where Department.dnumber = employee.dno and salary >40000 and
department.dnumber in (select dnumber from employee group by dnumber having count(*) > 5) group by dname;

DNAME             COUNT(*)
--------------- ----------
Administration           2
