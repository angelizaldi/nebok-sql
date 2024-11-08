# Sentencias de JOIN

Se refiere al proceso de unir dos o más tablas a través de los valores de dos o más columnas que comparten en común, para efectos de este sitio se llamará a estas columnas como _ids_.

Es recomendable asignar un alias a las tablas, sobre todo si hay columnas con el mismo nombre en ambas tablas.

En general se recomienda que en el resto del _query_, a la hora de hacer referencia a alguna columna de cualquier tabla, se utilice primero el alias de la tabla, seguido por un punto, seguido por el nombre de la columna. No es necesario hacerlo así, pero es recomendable (solo es necesario si las tablas tienen columnas que comparten nombre). Por ejemplo en `SELECT` sería.
```sql
-- Especificar la tabla de cada columna
SELECT t1.col1, t1.col2, ..., t2.col1, t2.col2, ...
```

<br/>

---
## ON

Sirve para especificar la o las columnas que tienen en común. Al menos se debe de especificar un par.
```SQL
-- Plantilla básica del uso de ON
FROM left_table AS t1
JOIN right_table AS t2
ON t1.id = t2.id [AND ...]
```
-	En caso de que se quiera usar más de un par de columnas-id se debe de poner un `AND` y volver a poner otro par de columnas-id, y así con las que sean necesarias.
-	No siempre se usa el operador `=`, se pueden usar otros como `<>`, etc.
-	Incluso es posible agregar filtros para una sola tabla, sin embargo tener en cuenta que el _output_ podría incluir ciertos registros aunque sin información para aquellos que no cumplen la condición, eso dependerá del tipo de join. <br> `t1.col = val`

En PostgreSQL si el nombre del _id_ en ambas tablas es el mismo, se puede usar la función `USING()` en lugar de `ON`:
```sql
-- Uso de USING()
FROM left_table AS t1
JOIN right_table AS t2
USING(id)
```

<br/>

---
## INNER JOIN
    
Sirve para unir dos o más tablas en una sola, de acuerdo con la **intersección** del _id_ que se utilice. 
```sql
-- Uso de INNER JOIN
FROM left_table AS t1
INNER JOIN right_table AS t2
ON t1.id = t2.id

-- Es igual a solo usar JOIN.
FROM left_table AS t1
JOIN right_table AS t2
ON t1.id = t2.id

-- Unir más de dos tablas
FROM left_table
INNER join right_table
ON left_table.ID = right_table.ID
INNER JOIN another_table
ON left_table.ID = another_table.ID;
```
-	No estrictamente se tiene que unir _another_table_ con _left_table_. Se pueden unir de otras formas.
-	**Importante**: Cuando se vaya a unir más de dos tablas se tiene que asegurar que en `ON` se declaren todos los posibles _ids_.

```{image} ../images/join-inner.png
:name: join-inner
:width: 100px
:align: center
```

<br/>

---
### LEFT JOIN

Une dos tablas (o más) con base al _id_ de la tabla de la izquierda, devuelve todos los registros de la tabla de la izquierda y los registros de la tabla de la derecha en las que existe una coincidencia de _id_. Si el id no existe en la tabla de la derecha entonces habrá valores `NULL`s.
```sql
-- Uso básico de LEFT JOIN
FROM left_table AS t1
LEFT JOIN right_table AS t2
ON t1.id = t2.id
```
- Es lo mismo que usar `LEFT OUTER JOIN`.

```{image} ../images/join-left.png
:name: join-left
:width: 100px
:align: center
```

<br/>

---
### RIGHT JOIN

Une dos tablas (o más) con base al _id_ de la tabla de la derecha, devuelve todos los registros de la tabla de la derecha y los registros de la tabla de la izquierda en las que existe una coincidencia de _id_. Si el _id_ no existe en la tabla de la izquierda entonces habrá valores `NULL`s.
```sql
-- Uso básico de RIGHT JOIN
FROM left_table AS t1
RIGHT JOIN right_table AS t2
ON t1.id = t2.id
```
- Es lo mismo que usar `RIGHT OUTER JOIN`.

```{image} ../images/join-right.png
:name: join-right
:width: 100px
:align: center
```

<br/>

---
### FULL JOIN

Hace la unión de tablas, es decir, junta tanto los _ids_ de la tabla de la izquierda, con los _id_s de la tabla de la derecha. Devuelve todos los registros de ambas tablas. Si el _id_ no existe en alguno de las tablas habrá valores `NULL`. 
```sql
-- Uso básico de FULL JOIN
FROM left_table AS t1
FULL JOIN right_table AS t2
ON t1.id = t2.id
```
- Es lo mismo que usar `FULL OUTER JOIN`.

```{image} ../images/join-full.png
:name: join-full
:width: 100px
:align: center
```

<br/>

---
### SELF JOIN

Es un _join_ de una tabla consigo misma, puede ser de cualquier tipo de _join_ (`INNER`, `LEFT`, `RIGHT`, etc). Se utiliza con datos jerarquizados o para comparar registros. Se recomienda poner alias a las tablas.
```sql
-- Ejemplo de un SELF JOIN con INNER JOIN
FROM table_name AS t1
INNER JOIN table_name AS t2
ON t1.id1 = t2.id2
```

<br/>

---
### CROSS JOIN

Junta tablas con base a todas las combinaciones posibles de los _ids_ (producto cartesiano). No lleva el argumento `ON` ni `USING()`.
```sql
-- Producto cartesiano de dos tablas
FROM table_name_1 AS t1
CROSS JOIN table_name_2 AS t2

-- Lo anterior equivale a:
FROM table_name_1 AS t1, table_name_2 AS t2
```

```{image} ../images/join-cross.png
:name: join-cross
:width: 100px
:align: center
```

<br/>

---
### NATURAL JOIN

Es un _join_ que une los valores de las columnas que tienen el mismo nombre en dos tablas distintas. Es útil cuando el _join_ será entre dos columnas que tienen el mismo nombre, con `NATURAL JOIN` no se tendrá que especificar `ON` ni `USING()`.
```sql
-- Uso de NATURAL JOIN
FROM table_name_1 AS t1
NATURAL [INNER|LEFT|RIGHT] JOIN table_name_2 AS t2
```
-	Por default es `INNER JOIN`, pero también puede ser `LEFT` o `RIGHT`.
-	Se debe de tener cuidado cuando más de una columna tienen el mismo nombre en la otra tabla, porque puede alterar los resultados.

:::{caution}
En general se recomienda evitar este tipo de `JOIN`.
:::