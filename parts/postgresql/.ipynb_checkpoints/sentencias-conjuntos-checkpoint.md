# Operaciones de conjuntos
 
Para visualizar las siguientes sentencias tomar como referencia la siguiente imagen.
 

## UNION

Aplica la unión de los campos por columnas de dos tablas distintas. `UNION` apila los resultados de dos _queries_ en una sola tabla. Basta con que las tablas de ambos _queries_ tengan el mismo número de columnas, sean del mismo tipo (cada columna con su respectiva de la otra tabla) y estén en el mismo orden. El nombre que se mostrará será el que se puso en el primer `SELECT`. Automáticamente elimina duplicados (registros que aparezcan igual en ambas tablas).
```SQL
-- Uso de UNION
SELECT COL1, COL2, ...
FROM TABLE1
UNION
SELECT COL1, COL2, ...
FROM TABLE2
```

```{image} ../images/sets-union.png
:name: sets-union
:width: 300px
:align: center
```
 
Para que no se eliminen los duplicados (en caso de que existan) se debe se usar `UNION ALL`:
```sql
-- Uso de UNION ALL
SELECT col1, col2, ...
FROM table1
UNION ALL
SELECT col1, col2, ...
FROM table2
```

```{image} ../images/sets-union-all.png
:name: sets-union-all
:width: 300px
:align: center
```
 
Se puede usar `ORDER BY` para ordenar los registros de la `UNION`, se pone al final:
```SQL
-- Ordenar resultado de UNION
SELECT col1, col2, ... FROM table1
UNION
SELECT col1, col2, ... FROM table2
ORDER BY col1, ...
```
- Aunque parezca que solo ordena los valores del segundo `SELECT`, en realidad ordena el resultado de la unión de ambos `SELECT`.

**Ejemplos**

```sql
-- UNION simple
SELECT surname
FROM cd.members
UNION
SELECT name
FROM cd.facilities;
```
- [Union simple](https://pgexercises.com/questions/basic/union.html).
 
## INTERSECT

Se puede elegir que solo se muestren las filas que tienen en común ambos _queries_ con `INTERSECT`, elimina los duplicados, deben de ser el mismo número de columnas y del mismo tipo. 
```SQL
SELECT COL1, COL2, ... FROM TABLA1
INTERSECT
SELECT COL1, COL2, ... FROM TABLA2
```

```{image} ../images/sets-intersection.png
:name: sets-intersection
:width: 300px
:align: center
```
 
Si no se quiere eliminar los duplicados usar `INTERSECT ALL`:
```SQL
SELECT COL1, COL2, ... FROM TABLA1
INTERSECT ALL
SELECT COL1, COL2, ... FROM TABLA2
```
 
Se puede usar `ORDER BY` para ordenar los registros de `INTERSECT`, se pone al final:
```SQL
SELECT COL1, COL2, ... FROM TABLE1
INTERSECT
SELECT COL1, COL2, ... FROM TABLE2
ORDER BY COLI
```
- Aunque parezca que solo orden los valores del segundo `SELECT`, en realidad ordena el resultado de la intersección de ambos `SELECT`.
 
## EXCEPT

Se puede elegir que solo se muestren las filas que están en la primera tabla, pero no están en la segunda tabla con `EXCEPT`, elimina los duplicados, deben de ser el mismo número de columnas y del mismo tipo:
```SQL
SELECT COL1, COL2, ... FROM TABLA1
EXCEPT
SELECT COL1, COL2, ... FROM TABLA2
```

```{image} ../images/sets-except.png
:name: sets-except
:width: 300px
:align: center
```
 
Se puede usar `ORDER BY` para ordenar los registros de  `EXCEPT`, se pone al final:
```SQL
SELECT COL1, COL2, ... FROM TABLE1
EXCEPT
SELECT COL1, COL2, ... FROM TABLE2
ORDER BY COLI
```
- Aunque parezca que solo orden los valores del segundo `SELECT`, en realidad ordena el resultado de ambos `SELECT`.
