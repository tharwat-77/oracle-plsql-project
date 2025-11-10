# oracle-plsql-project
“Comprehensive PL/SQL package with salary management, premium calculation, and CSV export utilities.”






















 ----------------------------------------- craeting a view for the first function --------------------------------------------------
 create or replace view emp_sal_vw as 
select j.MAX_SALARY , j.MIN_SALARY ,   e.SALARY as emp_sal , m.SALARY as mgr_sal , e.EMPLOYEE_ID
from jobs j , employees e , employees m 
where j.JOB_ID = e.JOB_ID
and e.MANAGER_ID = m.EMPLOYEE_ID
;
/
 ----------------------------------------- creating a log table for procedure 2  --------------------------------------------------
create table emp_sal_log(
emp_id number ,
tr_date date default sysdate ,
tr_user varchar2(200) default user ,
salary_before varchar2(200) ,
salary_after varchar2(200)
);
/
 ----------------------------------------- creating a setup table for procedure 4   -----------------------------------------------
create table  EMP_SAL_ELMS_SETUP
(
 ELM_SAL_CAT varchar2(20),
 ELM_ID number ,
 ELM_DESC varchar2(200),
  ELM_EFF varchar2(20),
 ELM_VAL number 
);
/
 ----------------------------------------- inserting the values  -----------------------------------------------
INSERT ALL
    INTO EMP_SAL_ELMS_SETUP (ELM_SAL_CAT, ELM_ID, ELM_DESC, ELM_EFF, ELM_VAL) VALUES ('A', 1, 'income_tax', '-', 0.20)
    INTO EMP_SAL_ELMS_SETUP (ELM_SAL_CAT, ELM_ID, ELM_DESC, ELM_EFF, ELM_VAL) VALUES ('A', 2, 'soc_ins', '-', 0.09)
    INTO EMP_SAL_ELMS_SETUP (ELM_SAL_CAT, ELM_ID, ELM_DESC, ELM_EFF, ELM_VAL) VALUES ('A', 3, 'Med_ins', '-', 0.05)
    INTO EMP_SAL_ELMS_SETUP (ELM_SAL_CAT, ELM_ID, ELM_DESC, ELM_EFF, ELM_VAL)  VALUES ('A', 4, 'other_ded', '-', 0.05)
    INTO EMP_SAL_ELMS_SETUP (ELM_SAL_CAT, ELM_ID, ELM_DESC, ELM_EFF, ELM_VAL)  VALUES ('A', 5, 'mob_allw', '+', 0.03)
    INTO EMP_SAL_ELMS_SETUP (ELM_SAL_CAT, ELM_ID, ELM_DESC, ELM_EFF, ELM_VAL)  VALUES ('A', 6, 'trns_allw', '+', 0.10)
    INTO EMP_SAL_ELMS_SETUP (ELM_SAL_CAT, ELM_ID, ELM_DESC, ELM_EFF, ELM_VAL)  VALUES ('B', 1, 'income_tax', '-', 0.22)
    INTO EMP_SAL_ELMS_SETUP (ELM_SAL_CAT, ELM_ID, ELM_DESC, ELM_EFF, ELM_VAL) VALUES ('B', 2, 'soc_ins', '-', 0.10)
    INTO EMP_SAL_ELMS_SETUP (ELM_SAL_CAT, ELM_ID, ELM_DESC, ELM_EFF, ELM_VAL) VALUES ('B', 3, 'Med_ins', '-', 0.08)
    INTO EMP_SAL_ELMS_SETUP (ELM_SAL_CAT, ELM_ID, ELM_DESC, ELM_EFF, ELM_VAL)  VALUES ('B', 4, 'other_ded', '-', 0.07)
    INTO EMP_SAL_ELMS_SETUP (ELM_SAL_CAT, ELM_ID, ELM_DESC, ELM_EFF, ELM_VAL)  VALUES ('B', 5, 'mob_allw', '+', 0.05)
    INTO EMP_SAL_ELMS_SETUP (ELM_SAL_CAT, ELM_ID, ELM_DESC, ELM_EFF, ELM_VAL) VALUES ('B', 6, 'trns_allw', '+', 0.12)
    INTO EMP_SAL_ELMS_SETUP (ELM_SAL_CAT, ELM_ID, ELM_DESC, ELM_EFF, ELM_VAL) VALUES ('C', 1, 'income_tax', '-', 0.25)
    INTO EMP_SAL_ELMS_SETUP (ELM_SAL_CAT, ELM_ID, ELM_DESC, ELM_EFF, ELM_VAL) VALUES ('C', 2, 'soc_ins', '-', 0.10)   
    INTO EMP_SAL_ELMS_SETUP (ELM_SAL_CAT, ELM_ID, ELM_DESC, ELM_EFF, ELM_VAL) VALUES ('C', 3, 'Med_ins', '-', 0.07)       
    INTO EMP_SAL_ELMS_SETUP (ELM_SAL_CAT, ELM_ID, ELM_DESC, ELM_EFF, ELM_VAL) VALUES ('C', 4, 'trns_allw', '+', 0.15)  
    INTO EMP_SAL_ELMS_SETUP (ELM_SAL_CAT, ELM_ID, ELM_DESC, ELM_EFF, ELM_VAL) VALUES ('D', 1, 'income_tax', '-', 0.27)
    INTO EMP_SAL_ELMS_SETUP (ELM_SAL_CAT, ELM_ID, ELM_DESC, ELM_EFF, ELM_VAL) VALUES ('D', 2, 'soc_ins', '-', 0.14)   
    INTO EMP_SAL_ELMS_SETUP (ELM_SAL_CAT, ELM_ID, ELM_DESC, ELM_EFF, ELM_VAL) VALUES ('D', 3, 'Med_ins', '-', 0.07)       
    INTO EMP_SAL_ELMS_SETUP (ELM_SAL_CAT, ELM_ID, ELM_DESC, ELM_EFF, ELM_VAL) VALUES ('D', 4, 'trns_allw', '+', 0.25)      
SELECT 1 FROM DUAL;
/
 ----------------------------------------- creating a view for procedure 4 & function 6  -----------------------------------------------
create or replace view emp_sal_cat_vw as
select employee_id , first_name || ' ' || last_name as full_name , salary ,
CASE
                    WHEN SALARY BETWEEN 0 AND 5000 THEN 'A'
                    WHEN SALARY BETWEEN 5001 AND 10000 THEN 'B'
                    WHEN SALARY BETWEEN 10001 AND 20000 THEN 'C'
                    ELSE 'D'
                END  as sal_cat
from employees;     
/
 ----------------------------------------- creating a lod table for procedure 4 after  -----------------------------------------------
create table dist_emp_sal_elms_tarns (
emp_id number,
sal_cat varchar2(200),
elm_id number,
elm_val number,
trans_month varchar2(20) 
);
/
------------------------------------------------------ creating a view to use it in the next view ----------------------------------------
create or replace view elm_val_grouped_vw as
select emp_id, sum(elm_val) as elm_val_grouped 
from dist_emp_sal_elms_tarns
group by emp_id;
/
------------------------------------------------------ creating a view to get the required data from procedure 4 -----------------------------
create or replace view emp_sal_cat_elm_vw as
select employee_id , full_name , sal_cat , salary as current_salary , salary + elm_val_grouped as salary_after
from emp_sal_cat_vw , elm_val_grouped_vw
where employee_id = emp_id ;
/
------------------------------------------------------ creating a view for function 5 & function 6  -----------------------------
create or replace force view full_address_vw as
select e.employee_id ,  l.street_address ||'-'|| l.city ||'-'|| c.country_name ||'-'|| r.region_name  as full_address ,
d.department_id , c.country_id
from employees e , departments d , locations l , jobs j , countries c , regions r
where e.department_id = d.department_id
and l.location_id = d.location_id
and e.job_id = j.job_id
and c.country_id=l.country_id 
and r.region_id = c.region_id;
/

----------------------------------------- creating a log table for function 12  --------------------------------------------------
create table date_format (
format_id number,
format_style varchar2(100) 
);
/
 ----------------------------------------- inserting the values for function 12 -----------------------------------------------
 insert all 
into date_format( format_id , format_style) values ( 1 , 'dd/mm/yyyy')
into date_format( format_id , format_style) values ( 2 , 'dd-mm-yyyy')
into date_format( format_id , format_style) values ( 3 , 'dd/mm-yyyy')
into date_format( format_id , format_style) values ( 4 , 'dd-my-yyyy')
into date_format( format_id , format_style) values ( 5 , 'FMdd/mm/yyyy')
into date_format( format_id , format_style) values ( 6 , 'day/MM/YYYY')
into date_format( format_id , format_style) values ( 7 , 'dy/mm/yyyy')
select 1 from dual;
/
----------------------------------------- creating a log table for function 13  --------------------------------------------------
create table emps_top_sal (
    tr_number     number,
    tr_date          date  default sysdate  ,          
    emp_id           number,           
    comp_top_sal     varchar2(200),     
    emp_dept_top_sal varchar2(200),      
    emp_job_top_sal  varchar2(200),       
    emp_loc_top_sal  varchar2(200),      
    emp_cat_top_sal  number              
);
/
------------------------------ creating sequence for function 13  ---------------------------
CREATE SEQUENCE  emps_top_sal_seq
START WITH  01
INCREMENT BY 1
NOCACHE
NOCYCLE;
/
------------------------------ creating sequence for procedure 14 -------------------------
CREATE SEQUENCE inv_num_seq
START WITH  10000
INCREMENT BY 1
NOCACHE
NOCYCLE;
/
-------------------------- creating a table to get the invoice details for procedure 14  -------------------------
create table prem_D
(
item_id  varchar2(100),
prem_num number , 
prem_amt number , 
prem_date date ,
prem_flag varchar2(100)
);
/
-------------------------- creating a table to get the invoice head for procedure 14  -------------------------
create table prem_H
(
itm_code  varchar2(100),
item_price number , 
prm_months number , 
disc_amt number ,
dep_amt number ,
mop varchar2(10),
inv_num number
);
/


create or replace package Omar_Tharwat_PKG
is 

FUNCTION increase_emp_sal_check ( p_emp_id     NUMBER,  p_incr_flag  VARCHAR2,  p_incr_value NUMBER  ) RETURN VARCHAR2 ;
--#1
procedure increase_emp_sal (  p_emp_id     NUMBER,  p_incr_flag  VARCHAR2,  p_incr_value NUMBER  ) ;
--#2
 function is_number ( p_number varchar2 ) return boolean ;
--#3 
procedure dist_emp_sal_elms_pro(p_emp_id  number) ;
--#4
 function get_full_addrees  ( p_id number , p_flag varchar2) return varchar2;
--#5
function get_emp_count_by (p_value varchar2,p_ref   varchar2) return number;
--#6
function get_sal_diff ( p_employee_id NUMBER , p_flag NUMBER) RETURN VARCHAR2;
--#7
FUNCTION is_manager ( p_emp_id NUMBER, p_flag   VARCHAR2) RETURN VARCHAR2;
--#8
FUNCTION get_sal_weight (p_emp_id  number,p_flag    VARCHAR2 ) RETURN VARCHAR2;
--#9
 function get_dept_name( p_id number , p_flag varchar2) return varchar2;
--#10
  FUNCTION get_manager_name (p_id   NUMBER,p_flag VARCHAR2) RETURN VARCHAR2;
--#11
   function my_to_char ( p_date date , p_format_id number ) return varchar2;
--#12
TYPE emp_top_sal_rec IS RECORD (
    emp_dept          EMPLOYEES.SALARY%TYPE,
    emp_job           EMPLOYEES.SALARY%TYPE,
    emp_location      EMPLOYEES.SALARY%TYPE,
    emp_sal_cat       EMPLOYEES.SALARY%TYPE,
    emp_whole_company EMPLOYEES.SALARY%TYPE
);
  function get_top_sal(p_emp_id  number) return emp_top_sal_rec;
 --#13
  PROCEDURE calc_premiums (
    p_itm_code     VARCHAR2,
    p_itm_price    NUMBER,
    p_prem_months  NUMBER,
    p_mop          VARCHAR2,
    p_discount     NUMBER DEFAULT 0, 
    p_deposit      NUMBER DEFAULT 0   
);
--#14
PROCEDURE Export_data(p_table_view_name IN VARCHAR2);
--#15
end ;
/

CREATE OR REPLACE PACKAGE BODY Omar_Tharwat_PKG
IS
-- forward declaration for a function would be used in function 7
 FUNCTION get_managers_chain (p_employee_id NUMBER , p_flag NUMBER) RETURN number;
--#1 
    FUNCTION increase_emp_sal_check (
        p_emp_id     NUMBER,
        p_incr_flag  VARCHAR2,
        p_incr_value NUMBER
    ) RETURN VARCHAR2
    IS
        v_max_salary   NUMBER;
        v_min_salary   NUMBER;
        v_emp_sal      NUMBER;
        v_mgr_sal      NUMBER;
        v_salary_after NUMBER;
    BEGIN
        SELECT max_salary,
               min_salary,
               emp_sal,
               mgr_sal
        INTO   v_max_salary,
               v_min_salary,
               v_emp_sal,
               v_mgr_sal
        FROM   emp_sal_vw
        WHERE  employee_id = p_emp_id;

        IF p_incr_flag = 'A' THEN
            v_salary_after := v_emp_sal + p_incr_value;

            IF v_salary_after BETWEEN v_min_salary AND v_max_salary
               AND v_salary_after < v_mgr_sal THEN
                RETURN 'Y';
            ELSE
                RETURN 'N';
            END IF;
        ELSIF p_incr_flag = 'P' THEN
            v_salary_after := v_emp_sal + v_emp_sal * p_incr_value;

            IF v_salary_after BETWEEN v_min_salary AND v_max_salary
               AND v_salary_after < v_mgr_sal THEN
                RETURN 'Y';
            ELSE
                RETURN 'N';
            END IF;
        ELSE
            raise_application_error(-20001, 'Invalid increase flag. Must be P or A');
        END IF;

    EXCEPTION
        WHEN NO_DATA_FOUND THEN
            RETURN 'Y';
        WHEN OTHERS THEN
            RAISE;
    END increase_emp_sal_check;
    
 --#2
 procedure increase_emp_sal (  p_emp_id     NUMBER,  p_incr_flag  VARCHAR2,  p_incr_value NUMBER  )   
   is 
           v_salary_after NUMBER;
           v_emp_sal      NUMBER;
           
           
 begin 
         SELECT salary
        INTO v_emp_sal
        FROM   employees
        WHERE  employee_id = p_emp_id;
        
        IF increase_emp_sal_check(p_emp_id, p_incr_flag, p_incr_value) = 'Y' THEN 
        
            IF p_incr_flag = 'A' THEN
                v_salary_after := v_emp_sal + p_incr_value;
            ELSIF p_incr_flag = 'P' THEN
                v_salary_after := v_emp_sal + v_emp_sal * p_incr_value;
            END IF;
            
            UPDATE employees 
            SET salary = v_salary_after
            WHERE employee_id = p_emp_id;
            
            INSERT INTO emp_sal_log (emp_id, salary_before, salary_after)
            VALUES (p_emp_id, v_emp_sal, v_salary_after);

        ELSE 
            RAISE_APPLICATION_ERROR(-20003, 'Invalid salary increase - out of range ');
        END IF;
        
    EXCEPTION
        WHEN NO_DATA_FOUND THEN
            RAISE_APPLICATION_ERROR(-20004, 'Employee ID ' || p_emp_id || ' not found');
        WHEN OTHERS THEN
            RAISE;
    END increase_emp_sal;
    
--#3
function is_number ( p_number varchar2 )
return boolean 
is
v_number number;
begin 
v_number := to_number(p_number); 
return true ;
exception 
when VALUE_ERROR then 
return false ;

end is_number;
    
--#4  
 PROCEDURE dist_emp_sal_elms_pro(P_EMP_ID  NUMBER)

 IS
 
    CURSOR c_emps IS
        SELECT employee_id, salary, sal_cat
        FROM emp_sal_cat_vw
        WHERE employee_id = NVL(P_EMP_ID, employee_id);
    
    CURSOR c_elm IS
        SELECT elm_sal_cat, elm_id, elm_desc, elm_eff, elm_val
        FROM EMP_SAL_ELMS_SETUP;
    
    v_salary_cat VARCHAR2(10);
    v_elm_calc_val NUMBER;
    v_last_month  VARCHAR2(100);
    
BEGIN

FOR emp IN c_emps LOOP

v_salary_cat := emp.sal_cat;

        FOR v_elm IN c_elm LOOP
            IF v_elm.elm_sal_cat = v_salary_cat THEN
                IF v_elm.elm_eff = '+' THEN
                    v_elm_calc_val := emp.salary * v_elm.elm_val;
                ELSIF v_elm.elm_eff = '-' THEN
                    v_elm_calc_val := emp.salary * v_elm.elm_val * -1;
                ELSE
                    v_elm_calc_val := 0;
                END IF;
                v_last_month := to_char( last_day (sysdate), 'DD-MM') ;
                INSERT INTO dist_emp_sal_elms_tarns (emp_id, sal_cat, elm_id, elm_val, trans_month)
                VALUES (emp.employee_id,v_salary_cat ,  v_elm.elm_id, v_elm_calc_val, v_last_month);
                
            END IF;
        END LOOP;
        
    END LOOP;
    
 end dist_emp_sal_elms_pro ;  
 --#5
function get_full_addrees  ( p_id number , p_flag varchar2)
return varchar2
is
cursor c_address_emps is
select employee_id , full_address , department_id
from full_address_vw
where employee_id = p_id
;
cursor c_address_depts is
select employee_id , full_address , department_id
from full_address_vw
where department_id = p_id
;
v_address varchar2(2000);
begin

if p_flag = 'E' then 
for emps in c_address_emps loop
v_address := emps.full_address ;
end loop;
elsif p_flag = 'D' then 
for depts in c_address_depts loop
v_address := depts.full_address ;
end loop;
end if;
return v_address;
end get_full_addrees ;
--#6
function get_emp_count_by (p_value varchar2,p_ref   varchar2) return number
is
    v_emp_count number;
begin
    if p_ref = 'D' then
        select count(*)
        into v_emp_count
        from employees
        where department_id = p_value;

    elsif p_ref = 'J' then
        select count(*)
        into v_emp_count
        from employees
        where job_id = p_value;

    elsif p_ref = 'M' then
        select count(*)
        into v_emp_count
        from employees
        where manager_id = p_value;

    elsif p_ref = 'S' then
        select count(*)
        into v_emp_count
        from emp_sal_cat_vw
        where sal_cat = p_value;

    elsif p_ref = 'C' then
        select count(*)
        into v_emp_count
        from full_address_vw
        where country_id = p_value;
        
    end if;

    return v_emp_count;
end get_emp_count_by ;
--#7
function get_sal_diff ( p_employee_id NUMBER , p_flag NUMBER) RETURN VARCHAR2
IS
v_result VARCHAR2(200);
v_manager number ;
v_emp_sal number ;
v_mgr_sal number ;
begin
if p_flag = 1 then 
v_manager := get_managers_chain(p_employee_id,1);

select salary
into v_emp_sal
from employees
where employee_id = p_employee_id;

select salary
into v_mgr_sal
from employees
where employee_id = v_manager ;

elsif p_flag = 2 then
v_manager := get_managers_chain(p_employee_id,2);

select salary
into v_emp_sal
from employees
where employee_id = p_employee_id;

select salary
into v_mgr_sal
from employees
where employee_id = v_manager ;

elsif p_flag = 3 then
v_manager := get_managers_chain(p_employee_id,3);

select salary
into v_emp_sal
from employees
where employee_id = p_employee_id;

select salary
into v_mgr_sal
from employees
where employee_id = v_manager ;
end if;

v_result:= v_emp_sal - v_mgr_sal;
return v_result;

EXCEPTION
        WHEN NO_DATA_FOUND THEN
            RAISE_APPLICATION_ERROR(-20005, 'No manager found in this level for the employee' || p_employee_id );
        WHEN OTHERS THEN
            RAISE;
end get_sal_diff ;
--#8
FUNCTION is_manager ( p_emp_id NUMBER, p_flag   VARCHAR2) RETURN VARCHAR2
IS
    v_count NUMBER := 0;
BEGIN
    IF p_flag = 'E' THEN
        SELECT COUNT(*)
        INTO v_count
        FROM employees 
        WHERE manager_id = p_emp_id;

    ELSIF p_flag = 'D' THEN
        SELECT COUNT(*)
        INTO v_count
        FROM departments 
        WHERE manager_id = p_emp_id;
    END IF;

    IF v_count > 0 THEN
        RETURN 'Y';
    ELSE
        RETURN 'N';
    END IF;

END is_manager;
--#9
FUNCTION get_sal_weight (p_emp_id  number,p_flag    VARCHAR2 ) RETURN VARCHAR2
IS
    v_emp_sal  number;
    v_sum_sal   NUMBER;
    v_result    NUMBER;
BEGIN
    SELECT e.salary
    INTO v_emp_sal
    FROM employees e
    WHERE e.employee_id = p_emp_id;

    IF p_flag = 'D' THEN
        SELECT SUM(e2.salary)
        INTO v_sum_sal
        FROM employees e1, employees e2
        WHERE e1.employee_id = p_emp_id
          AND e1.department_id = e2.department_id;

    ELSIF p_flag = 'J' THEN

        SELECT SUM(e2.salary)
        INTO v_sum_sal
        FROM employees e1, employees e2
        WHERE e1.employee_id = p_emp_id
          AND e1.job_id = e2.job_id;

    ELSIF p_flag = 'M' THEN

        SELECT SUM(e2.salary)
        INTO v_sum_sal
        FROM employees e1, employees e2
        WHERE e1.employee_id = p_emp_id
          AND e1.manager_id = e2.manager_id;

    ELSIF p_flag = 'C' THEN

        SELECT SUM(salary)
        INTO v_sum_sal
        FROM employees ;

    ELSIF p_flag = 'S' THEN

        SELECT SUM(v.salary)
        INTO v_sum_sal
        FROM emp_sal_cat_vw v, emp_sal_cat_vw v2
        WHERE v2.employee_id = p_emp_id
          AND v.sal_cat = v2.sal_cat;

    ELSE
        RETURN 'Invalid flag';
    END IF;

    v_result := ROUND((v_emp_sal / v_sum_sal) * 100, 2);

    RETURN v_result ;
    
END get_sal_weight;
--#10
function get_dept_name( p_id number , p_flag varchar2) return varchar2
is
v_dept_name  varchar2(1000);

begin
if p_flag = 'D' then
 select department_name
 into v_dept_name
 from departments
 where department_id = p_id ;
 
elsif p_flag = 'E' then
select d.department_name
into v_dept_name
from departments d , employees e
where e.employee_id = p_id
and d.department_id = e.department_id;
else
        return 'Invalid flag';
    end if;

return v_dept_name;

EXCEPTION
        WHEN NO_DATA_FOUND THEN
            RAISE_APPLICATION_ERROR(-20007, 'There is no department with this ID');
        WHEN OTHERS THEN
            RAISE;

end get_dept_name;
--#11
FUNCTION get_manager_name (p_id   NUMBER,p_flag VARCHAR2) RETURN VARCHAR2
IS
    v_mgr_name VARCHAR2(200);
BEGIN
    IF p_flag = 'E' THEN

        SELECT m.first_name || ' ' || m.last_name
        INTO v_mgr_name
        FROM employees e, employees m
        WHERE e.employee_id = p_id
          AND e.manager_id = m.employee_id;

    ELSIF p_flag = 'D' THEN

        SELECT m.first_name || ' ' || m.last_name
        INTO v_mgr_name
        FROM departments d, employees m
        WHERE d.department_id = p_id
          AND d.manager_id = m.employee_id;

    ELSE
        RETURN 'Invalid flag';
    END IF;

    RETURN v_mgr_name;

EXCEPTION
    WHEN NO_DATA_FOUND THEN
            RAISE_APPLICATION_ERROR(-20008, 'No manager found');
        WHEN OTHERS THEN
            RAISE;
END get_manager_name;
--#12
function my_to_char ( p_date date , p_format_id number ) return varchar2
is
v_format_style varchar2(100);
v_result varchar2(100) ;
begin

select format_style
into v_format_style
 from date_format
 where format_id = p_format_id;
 
 v_result:= to_char(p_date , v_format_style);
 
 return v_result;

end my_to_char;
--#13
function get_top_sal (p_emp_id number) return emp_top_sal_rec
 is
    v_sal_dept          NUMBER;
    v_sal_job           NUMBER;
    v_sal_location      NUMBER;
    v_sal_sal_cat       NUMBER;
    v_sal_whole_company NUMBER;
    v_tr_number         NUMBER;
    v_result            emp_top_sal_rec;
 
 begin 
        SELECT max(e2.salary)
        INTO v_sal_dept
        FROM employees e1, employees e2
        WHERE e1.employee_id = p_emp_id
          AND e1.department_id = e2.department_id; 

        SELECT max(e2.salary)
        INTO v_sal_job
        FROM employees e1, employees e2
        WHERE e1.employee_id = p_emp_id
          AND e1.JOB_ID = e2.JOB_ID;

    SELECT MAX(e.salary)
    INTO v_sal_location
    FROM employees e, departments d, locations l
    WHERE e.department_id = d.department_id
      AND d.location_id   = l.location_id
      AND d.location_id = (
            SELECT d2.location_id
            FROM employees e2, departments d2
            WHERE e2.department_id = d2.department_id
              AND e2.employee_id   = p_emp_id );

         SELECT max(v.salary)
        INTO v_sal_sal_cat
        FROM emp_sal_cat_vw v, emp_sal_cat_vw v2
        WHERE v2.employee_id = p_emp_id
          AND v.sal_cat = v2.sal_cat;
          
         select max(salary)
         into v_sal_whole_company
         from employees;

    v_result.emp_dept          := v_sal_dept;
    v_result.emp_job           := v_sal_job;
    v_result.emp_location      := v_sal_location;
    v_result.emp_sal_cat       := v_sal_sal_cat;
    v_result.emp_whole_company := v_sal_whole_company;

select emps_top_sal_seq.nextval 
into v_tr_number
from dual;

insert into emps_top_sal
( tr_number , emp_id , comp_top_sal , emp_dept_top_sal ,emp_job_top_sal , emp_loc_top_sal , emp_cat_top_sal )
values
( v_tr_number , p_emp_id , v_sal_whole_company , v_sal_dept , v_sal_job , v_sal_location , v_sal_sal_cat);

 RETURN v_result;
END get_top_sal;
--#14
PROCEDURE calc_premiums (
    p_itm_code     VARCHAR2,
    p_itm_price    NUMBER,
    p_prem_months  NUMBER,
    p_mop          VARCHAR2,
    p_discount     NUMBER DEFAULT 0, 
    p_deposit      NUMBER DEFAULT 0   
) IS
    v_prem_amt          NUMBER;
    v_prem_num          NUMBER;
    v_due_date          DATE := SYSDATE;
    v_current_due_date  DATE := SYSDATE;
    v_inv_num           NUMBER;
    v_months_to_add     NUMBER;
    v_dis_amt           NUMBER;
BEGIN

    IF p_mop = 'M' THEN
        v_months_to_add := 1;
        v_prem_num := p_prem_months;
    ELSIF p_mop = 'Q' THEN
        v_months_to_add := 3;
        v_prem_num := TRUNC(p_prem_months / 3);
    ELSIF p_mop = 'S' THEN
        v_months_to_add := 6;
        v_prem_num := TRUNC(p_prem_months / 6);
    ELSIF p_mop = 'A' THEN
        v_months_to_add := 12;
        v_prem_num := TRUNC(p_prem_months / 12);
    ELSE
        RAISE_APPLICATION_ERROR(-20001, 'Invalid Payment Mode (p_mop). Must be M, Q, S, or A.');
    END IF;

    v_dis_amt := p_discount;

    v_prem_amt := (p_itm_price - v_dis_amt - p_deposit) / v_prem_num;

SELECT inv_num_seq.NEXTVAL
INTO v_inv_num
FROM dual;

    INSERT INTO prem_H (
        itm_code, item_price, prm_months, disc_amt, dep_amt, mop, inv_num
    ) VALUES (
        p_itm_code, p_itm_price, p_prem_months, v_dis_amt, p_deposit, p_mop, v_inv_num
    );

    IF p_discount <> 0 THEN
        INSERT INTO prem_D (item_id, prem_num, prem_amt, prem_date, prem_flag)
        VALUES (p_itm_code, 0, 0, trunc( sysdate), 'C');
    END IF;

    IF p_deposit <> 0 THEN
        INSERT INTO prem_D (item_id, prem_num, prem_amt, prem_date, prem_flag)
        VALUES (p_itm_code, 0, 0, trunc( sysdate), 'D');
    END IF;

    FOR x IN 1..v_prem_num LOOP
        v_current_due_date := ADD_MONTHS(v_due_date, (x-1) * v_months_to_add);

        INSERT INTO prem_D (item_id, prem_num, prem_amt, prem_date, prem_flag)
        VALUES (p_itm_code, x, v_prem_amt, TRUNC(v_current_due_date), 'P');
    END LOOP;

END calc_premiums ; 
--#15
PROCEDURE Export_data(p_table_view_name IN VARCHAR2) IS
    v_file_handle  UTL_FILE.FILE_TYPE;
    v_sql          VARCHAR2(1000);
    v_cursor       SYS_REFCURSOR;
    v_line         VARCHAR2(4000);
    v_col_val      VARCHAR2(4000);
    v_desc         DBMS_SQL.DESC_TAB;
    v_col_cnt      INTEGER;
    v_cursor_id    INTEGER;
BEGIN
    v_file_handle := UTL_FILE.FOPEN('MY_DIR', p_table_view_name || '_export.csv', 'W');

    v_sql := 'SELECT * FROM ' || p_table_view_name;

    v_cursor_id := DBMS_SQL.OPEN_CURSOR;
    DBMS_SQL.PARSE(v_cursor_id, v_sql, DBMS_SQL.NATIVE);
    DBMS_SQL.DESCRIBE_COLUMNS(v_cursor_id, v_col_cnt, v_desc);

    FOR i IN 1..v_col_cnt LOOP
        DBMS_SQL.DEFINE_COLUMN(v_cursor_id, i, v_col_val, 4000);
    END LOOP;

    v_col_cnt := DBMS_SQL.EXECUTE(v_cursor_id);

    v_line := NULL;
    FOR i IN 1..v_col_cnt LOOP
        v_line := v_line || v_desc(i).col_name || ',';
    END LOOP;
    v_line := RTRIM(v_line, ',');
    UTL_FILE.PUT_LINE(v_file_handle, v_line);

    WHILE DBMS_SQL.FETCH_ROWS(v_cursor_id) > 0 LOOP
        v_line := NULL;
        FOR i IN 1..v_col_cnt LOOP
            DBMS_SQL.COLUMN_VALUE(v_cursor_id, i, v_col_val);
            v_line := v_line || NVL(v_col_val, 'NULL') || ',';
        END LOOP;
        v_line := RTRIM(v_line, ',');
        UTL_FILE.PUT_LINE(v_file_handle, v_line);
    END LOOP;

    DBMS_SQL.CLOSE_CURSOR(v_cursor_id);
    UTL_FILE.FCLOSE(v_file_handle);

EXCEPTION
    WHEN OTHERS THEN
        IF DBMS_SQL.IS_OPEN(v_cursor_id) THEN
            DBMS_SQL.CLOSE_CURSOR(v_cursor_id);
        END IF;
        IF UTL_FILE.IS_OPEN(v_file_handle) THEN
            UTL_FILE.FCLOSE(v_file_handle);
        END IF;
        RAISE;
END Export_data ;

FUNCTION get_managers_chain (p_employee_id NUMBER , p_flag NUMBER) RETURN number
IS
    v_result number;
BEGIN
    IF p_flag = 1 THEN
        SELECT m.EMPLOYEE_ID
        INTO v_result
        FROM employees e, employees m
        WHERE e.manager_id = m.employee_id
          AND e.employee_id = p_employee_id;

    ELSIF p_flag = 2 THEN
        SELECT m2.EMPLOYEE_ID
        INTO v_result
        FROM employees e, employees m1, employees m2
        WHERE e.manager_id = m1.employee_id
          AND m1.manager_id = m2.employee_id
          AND e.employee_id = p_employee_id;

    ELSIF p_flag = 3 THEN
        SELECT m3.EMPLOYEE_ID
        INTO v_result
        FROM employees e, employees m1, employees m2, employees m3
        WHERE e.manager_id = m1.employee_id
          AND m1.manager_id = m2.employee_id
          AND m2.manager_id = m3.employee_id
          AND e.employee_id = p_employee_id;

    END IF;

    RETURN v_result;

EXCEPTION
    WHEN NO_DATA_FOUND THEN
        RETURN null;
END;

END Omar_Tharwat_PKG;
/
