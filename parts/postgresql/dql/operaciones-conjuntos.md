# Operaciones de conjuntos
 
Para visualizar las siguientes sentencias tomar como referencia la siguiente imagen.

```{image} ../images/conj-sql.png
:name: sets-sql
:width: 500px
:align: center
``` 
---
## UNION

Aplica la unión de los campos por columnas de dos tablas distintas. `UNION` apila los resultados de dos _queries_ en una sola tabla. Basta con que las tablas de ambos _queries_ tengan el mismo número de columnas, sean del mismo tipo (cada columna con su respectiva de la otra tabla) y estén en el mismo orden. 
```sql
-- Uso de UNION
SELECT col1, col2, ...
FROM table_name_1
UNION
SELECT col1, col2, ...
FROM table_name_2
```
- El nombre que se mostrará será el que se puso en el primer `SELECT`.
- Automáticamente elimina duplicados (registros que aparezcan igual en ambas tablas).

```{image} ../images/sets-union.png
:name: sets-union
:width: 100px
:align: center
```

<br/>
 
Para que no se eliminen los duplicados (en caso de que existan) se debe se usar `UNION ALL`:
```sql
-- Uso de UNION ALL
SELECT col1, col2, ...
FROM table_name_1
UNION ALL
SELECT col1, col2, ...
FROM table_name_2
```

```{image} ../images/sets-union-all.png
:name: sets-union-all
:width: 100px
:align: center
```

<br/>
 
Se puede usar `ORDER BY` para ordenar los registros de la `UNION`, se pone al final:
```sql
-- Ordenar resultado de UNION
SELECT col1, col2, ... FROM table_name_1
UNION
SELECT col1, col2, ... FROM table_name_2
ORDER BY col1, ...
```
- Se ordena el resultado de la unión de ambos `SELECT`.

**Ejemplos**

```sql
-- UNION simple
SELECT surname
FROM cd.members
UNION
SELECT name
FROM cd.facilities;
```
- [Union simple](https://pgexercises.com/questions/basic/union.html): En este simplemente se unen los resultados de dos queries.

<br/>

---
## INTERSECT

Se puede elegir que solo se muestren las filas que tienen en común ambos _queries_ con `INTERSECT`, elimina los duplicados, deben de ser el mismo número de columnas y del mismo tipo. 
```sql
-- Uso de INTERSECT
SELECT col1, col2, ... FROM table_name_1
INTERSECT
SELECT col1, col2, ... FROM table_name_2
```

```{image} ../images/sets-intersection.png
:name: sets-intersection
:width: 100px
:align: center
```

<br/>

Si no se quiere eliminar los duplicados usar `INTERSECT ALL`:
```sql
-- Uso de INTERSECT ALL
SELECT col1, col2, ... FROM table_name_1
INTERSECT ALL
SELECT col1, col2, ... FROM table_name_2
```
 
Se puede usar `ORDER BY` para ordenar los registros de `INTERSECT`, se pone al final:
```sql
-- Ordenar resultado de INTERSECT
SELECT col1, col2, ... FROM table_name_1
INTERSECT
SELECT col1, col2, ... FROM table_name_2
ORDER BY col1, ...
```
- Se ordena el resultado de la intersección de ambos `SELECT`.

<br/>

---
## EXCEPT

Se puede elegir que solo se muestren las filas que están en la primera tabla, pero no están en la segunda tabla con `EXCEPT`, elimina los duplicados, deben de ser el mismo número de columnas y del mismo tipo:
```sql
-- Uso de EXCEPT
SELECT col1, col2, ... FROM table_name_1
EXCEPT
SELECT col1, col2, ... FROM table_name_2
```

```{image} ../images/sets-except.png
:name: sets-except
:width: 100px
:align: center
```

<br/>


Se puede usar `ORDER BY` para ordenar los registros de  `EXCEPT`, se pone al final:
```sql
-- Ordenar resultado de EXCEPT
SELECT col1, col2, ... FROM table_name_1
EXCEPT
SELECT col1, col2, ... FROM table_name_2
ORDER BY col1, ...
```
- Se ordena el resultado de ambos `SELECT`.
