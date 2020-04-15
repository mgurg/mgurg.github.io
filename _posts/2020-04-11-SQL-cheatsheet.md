---
layout: post
title: "SQL: cheatsheet"
categories: SQL
author: "Michał"
---

# Bazy danych - podstawowa teoria

Bazy danych 

- relacyjne (SQL)
- nierelacyjne (NoSQL)

  

## Główny podział poleceń w SQL

**Data Definition Language (DDL):**

- CREATE

- ALTER (add, delete, or modify columns in an existing table)

- DROP

- RENAME

- TRUNCATE

**Data Manipulation Language (DML)**

- SELECT ... FROM ...

- INSERT ... INTO ... VALUES ...

- UPDATE ... SET ... WHERE

- DELETE ... FROM ... WHERE ...


**Data Control Language (DCL)**

- REVOKE

- GRANT


**Transaction Control Language (TCL)**

- COMMIT

- ROLLBACK

### Primary key

Unikalny identyfikator - kolumna (lub zestaw kolumn) które zawiera(ją) unikalne wartości dla każdego rekordu w tabeli. Dozwolony jest jeden *primary key* na tabelę. Nie może być pusty (null). Mie każda tabela musi mieć PK.

### Foreign Key

identyfikuje relację pomiędzy tabelami

| Sales                      |      |      |      |      | Customers              |
| -------------------------- | ---- | ---- | ---- | ---- | ---------------------- |
| <u>**purchase_number**</u> |      | \|   | ---  | ->   | **<u>customer_id</u>** |
| date_of_purchase           |      |      |      |      | first_name             |
| customer_id (**FK**) ---   | ---  | \|   |      |      | last_name              |
|                            |      |      |      |      | email_address          |

parent table - referenced table

child table - referencing table



```sql
-- add FK
ALTER TABLE sales
ADD FOREIGN KEY (customer_id) REFERENCES customers(customer_id) ON DELETE CASCADE;

-- remove FK
ALTER TABLE sales
DROP FOREIGN KEY sales_ibfk2;
```



### Unique key

Musi zawierać unikalne wartości w przeciwieństwie do PK może mieć puste wartości (NULL). Może występować kilka *Unique Key* w jednej tabeli.

```sql
-- add UK
ALTER TABLE customers
ADD UNIQUE KEY (email_address);  

-- remove UK
ALTER TABLE customers
DROP INDEX email_address;
```



### Relacje

- Jeden do wielu

- wiele do jednego-

- wiele do wielu

- jeden do jednego

---

Całość można pogrupować jak poniżej:

- SELECT
- INSERT
- UPDATE
- DELETE
- funkcje agregujące
- JOIN
- Subqueries

---

  



## SELECT

Kolejność komend:

```sql
SELECT column_name(s)
FROM table_name
WHERE conditions
GROUP BY column_name(s)
HAVING conditions
ORDER BY column_name(s)
LIMIT number;
```



Podstawowe operacje:

1: `AND` / `OR` 

```sql
SELECT
   * -- column1, column2
FROM
   employees -- table name
WHERE -- opcjonalnie
   last_name = 'Elvis' AND (gender = 'M' OR gender = 'F'); -- AND, OR, IN etc.
/*
Kolejność operatorów AND > OR
*/

```

2: IN/ NOT IN

```sql
SELECT * FROM employees
WHERE
   first_name NOT IN ('John' , 'Mark', 'Jacob'); -- multiple conditoions, wszyscy poza
```

3: LIKE

```sql
SELECT   * FROM   employees
WHERE
   first_name LIKE('_ar%'); -- Mariusz, Marcin _ pojedynczy znak, % dowolny ciąg znaków
```

4: BETWEEN

```sql
SELECT   * FROM   salaries
WHERE
   salary BETWEEN 6600 AND 7000;
```

5: IS NOT NULL

```sql
SELECT   dept_name	FROM   departments
WHERE
   dept_no IS NOT NULL;
```

6: DISTINCT

```sql
SELECT DISTINCT -- jak pandas.unique(), zwraca uniklane wartości w kolumnie
   hire_date
FROM
   employees;
```

7: DESC/ASC - sortowanie rosnąco malejąco

```sql
SELECT
   *
FROM
   employees
ORDER BY hire_date DESC;
```

8: Alias -  AS (nadanie nowej nazwy)

```sql
SELECT
   salary, COUNT(emp_no) AS emps_with_same_salary --AS rename nowo stworzonej kolumny
FROM
   salaries
WHERE
   salary > 80000
GROUP BY salary
ORDER BY salary;
```

 9: HAVING - używane po GROUP By żeby doprecyzować zapytanie

```sql
SELECT
   emp_no, AVG(salary)
FROM
   salaries
GROUP BY emp_no
HAVING AVG(salary) > 120000
ORDER BY emp_no;
```

Aggregate functions: GROUP BY i HAVING

Ogólne warunki: WHERE (używane jest przed GROUP BY)

10: LIMIT

```sql
SELECT
   *
FROM
   dept_emp
ORDER BY salaries DESC -- odpowiednik pandas.head(10) .tail(10)
LIMIT 100;
```



## INSERT

Przykładowa składnia:

```sql
INSERT INTO employees
VALUES
(
   999903, -- brak apostrofów - Integer
   '1977-09-14',
   'Johnathan',
   'Creek',
   'M',
   '1999-01-01'
);
```

## UPDATE

```sql
UPDATE departments
SET

   dept_name = 'Data Analysis'
WHERE
   dept_no = 'd010'; -- bez tego warunku nadpisz ewszystkie rekordy
```

Pamiętać o COMMIT i ROLLBACK



## DELETE

ON DELETE CASCADE - przy usuwaniu rodzica usuwa wartości w tabeli dziecka

```
DELETE FROM departments
WHERE
   dept_no = '5';
```

DROP vs TRUNCATE vs DELETE

**DROP**:

- usuwa zawartość

- usuwa indexy
- usuwa constrains
- nie może być cofnięty poprzez ROLLBACK

**TRUNCATE**:

- odpowiednik DELETE bez zdefiniowanego WHERE
- usuwa zawartość, struktura zostaje zachowana
- wartości auto-increment zostaną zresetowane (1,2,3 - X - 5 -->  1, 2,3 )

**DELETE**:

- usuwa rekordy rzędami
- wartości auto-increment pozostają zachowane ()
- Jest wolniejsze od TRUNCATE (jeżeli używamy go w taki sam sposób, tzn. bez WHERE)



## AGG

Przykłady funkcji agregujących: COUNT() SUM() MIN() MAX() AVG()

```sql
SELECT
   COUNT(DISTINCT first_names) -- pandas.nunique()
FROM
   employees
```



```sql
COUNT() --  zlicza wartości, jako jedyna działa z non-numeruc data
COUNT(*) -- zlicza wartości (lącznie z polami typu NULL)
COUNT(DISTINCT) -- zlicza unikalne wartości

SUM()
MIN()
MAX()
AVG()

ROUND(#, decimal_places) -- decimal place jest opcjonalny

IFNULL(expression_not_null, expression_null)
COALESCE(expr_1, expr_2, exp_n3 ...)--IFNULL z więcej niż dwoma parametrami
```



## JOINS

1: INNER JOIN - zwraca tylko część wspólną

```sql
SELECT e.emp_no,
       e.first_name,
       e.last_name,
       dm.dept_no,
       e.hire_date
FROM   employees e
       JOIN dept_manager dm
         ON e.emp_no = dm.emp_no; 
```

2: LEFT JOIN - zwraca część wspólną + wartości z lewej tabeli które nie mają pokrycia w prawej

3: RIGHT JOIN - zwraca część wspólną + wartości z prawej tabeli które nie mają pokrycia w lewej (jak odwrócimy tabele w LEFT JOIN to otrzymamy to samo)

4: CROSS JOIN

5: UNION / UNION ALL

6: self join

## CASES

```sql
SELECT e.emp_no,
       e.first_name,
       e.last_name,
       CASE
         WHEN dm.emp_no IS NOT NULL THEN 'Manager'
         ELSE 'Employee'
       end AS is_manager
FROM   employees e
       LEFT JOIN dept_manager dm
              ON dm.emp_no = e.emp_no
WHERE  e.emp_no > 109990;  
```



## SQL VIEW

Wirtualna tabela utworzona z innej istniejącej tabeli lub tabel. "Migawka" z zapisanym stanem zdefiniowanego wcześniej zapytania. Jest ona uaktualniana automatycznie wraz ze zmieniającymi się danymi w oryginalnej tabeli.

```sql
CREATE OR replace VIEW v_manager_avg_salary
AS
  SELECT Round(Avg(salary), 2)
  FROM   salaries s
         join dept_manager m
           ON s.emp_no = m.emp_no;  
```



## Indeksy

s

```sql
SELECT *
FROM   salaries
WHERE  salary > 89000;

CREATE INDEX i_salary ON salaries(salary);

SELECT *
FROM   salaries
WHERE  salary > 89000;  
```



## Stored routines

- stored procedures (nie zwracają wartości) Użycie: `call` procedure; 
- functions (user defined, built-in - zwracają pojedynczą wartość) Użycie: `select` function;

Procedura:

```sql
DELIMITER $$

CREATE PROCEDURE avg_salary()
BEGIN
	SELECT
		AVG(salary)
	FROM
	salaries;
END$$

DELIMITER ;

--to use type:
CALL avg_salary;
CALL avg_salary();
CALL employees.avg_salary;
CALL employees.avg_salary(); 
```

Funkcja:

```sql
DELIMITER $$ 

CREATE FUNCTION emp_info(p_first_name varchar(255), p_last_name varchar(255)) 
RETURNS decimal(10, 2)

BEGIN
   DECLARE v_max_from_date date;
DECLARE v_salary decimal(10, 2);
SELECT
   MAX(from_date) INTO v_max_from_date 
FROM
   employees e 
   JOIN
      salaries s 
      ON e.emp_no = s.emp_no 
WHERE
   e.first_name = p_first_name 
   AND e.last_name = p_last_name;
SELECT
   s.salary INTO v_salary 
FROM
   employees e 
   JOIN
      salaries s 
      ON e.emp_no = s.emp_no 
WHERE
   e.first_name = p_first_name 
   AND e.last_name = p_last_name 
   AND s.from_date = v_max_from_date;
RETURN v_salary;

END $$ 
DELIMITER ;
 
SELECT
   EMP_INFO('Aruna', 'Journel');
```

