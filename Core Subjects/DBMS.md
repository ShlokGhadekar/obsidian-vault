### Relational Algebra

 *use : blueprint*
 Unary Operators (Work on 1 Table)
- **σ (Sigma) – Selection**: Filters **rows** based on a condition (like `WHERE` in SQL).
- **π (Pi) – Projection**: Selects specific **columns** and eliminates duplicates (like `SELECT` in SQL).
- **ρ (Rho) – Rename**: Renames a table or a column (like `AS` in SQL).
Binary Operators (Combine 2 Tables)
- **⋈ (Bowtie) – Join**: Combines related rows from two tables based on a common column.
- **∪ (Union) – Union**: Combines all unique rows from two compatible tables.
- **∩ (Intersection) – Intersection**: Keeps only the rows that appear in _both_ tables.
- **– (Minus) – Set Difference**: Keeps rows from the first table that are _not_ present in the second table.
- **× (Cross) – Cartesian Product**: Pairs every single row of the first table with every single row of the second table.

question 1.
![[Pasted image 20260904203825.png|243]]![[Pasted image 20260904203840.png|197]]
**Student** ⊳⊲(Number=ID) **Teaching Assistants**
(inner join of student and teaching assistant, ![[Pasted image 20260904203955.png|424]])

### JOIN in sql
![[Pasted image 20260904204611.png|197]]

1. Inner Join
```sql
SELECT *
FROM Student S
INNER JOIN Department D
ON S.Dept = D.Dept;
```
result - A and C
2. Left Join
```sql
SELECT *
FROM Student S
LEFT JOIN Department D
ON S.Dept = D.Dept;
```
result - A, B, C
