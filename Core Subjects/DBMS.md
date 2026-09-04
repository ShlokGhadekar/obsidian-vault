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
![[Pasted image 20260904203825.png]]