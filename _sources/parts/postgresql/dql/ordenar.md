# Ordenar

## ORDER BY

```{important}
Cuando se ordena texto con `ORDER BY`, primero pondrá aquellos resultados que tienen espacios antes de la palabra, después las que inician con minúsculas y después las que empiezan con mayúsculas.
```

```{important}
Si se ordena con base a dos o más columnas, se prioriza el orden de los valores de la primer columna que se ponga, en caso de que haya empates en esa columna, esos empates se ordenan con base a los valores de la segunda columna y así con el resto de las columnas.
```

Se utiliza para ordenar los resultados del _query_ con base a una o más columnas. Por default ordena los valores de manera ascendente. 
```sql
-- Ordenar el result set con base a valores de las columnas
SELECT col1 [, col2, ...]
FROM table_name
ORDER BY col1 [ASC|DESC] [, col2 [ASC|DESC], ...];
```
- _col1, col2, ..._ son los nombres de las columnas. No estrictamente deben de ser seleccionadas en `SELECT`, pero sí deben estar en la tabla del `FROM`.
- _table_name_ es el nombre de la tabla.
- En lugar de usar los nombres de las columnas se pueden usar números (_1, 2, ..._), que hacen referencia a los campos en `SELECT` por orden en los que fueron declarados.
- Se puede ordenar por _columnas calculadas_ en `SELECT` o utilizar los alias para hacer referencia a las columnas.
- Se puede decir explícitamente que se ordene de manera ascendente con `ASC`. <br> `ORDER BY col1 ASC [, col2 ASC, ...]`
- Para ordenar de manera descendente se usa `DESC` después del nombre de cada columna donde se quiera aplicar. <br/> `ORDER BY col1 DESC [, col2 DESC, ...]`
- Se puede usar `ASC` o `DESC` de manera simultanea en columnas diferentes en un mismo _query_.

<br/>

**Ejemplos**:
```sql
-- Ordenar simple
SELECT DISTINCT surname
FROM cd.members
ORDER BY surname
LIMIT 10;

-- Ordenar con dos columnas
SELECT DISTINCT m2.firstname AS firstname, 
				m2.surname AS surname
FROM cd.members AS m1
INNER JOIN cd.members AS m2
ON m1.recommendedby = m2.memid
ORDER BY surname, firstname;
```
- [Ordenar simple](https://pgexercises.com/questions/basic/unique.html): En este ejemplo se ordena con base a una única columna.
- [Ordenar múltiple](https://pgexercises.com/questions/joins/self.html): En este ejemplo se ordena con base a dos columnas diferentes.

