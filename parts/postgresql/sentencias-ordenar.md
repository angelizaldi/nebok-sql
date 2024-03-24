# Sentencias para Ordenar

## ORDER BY

```{important}
Cuando se orden texto con `ORDER BY`, primero pondrá aquellos resultados que tienen espacios antes de la palabra, después las que inician con minúsculas y después las que empiezan con mayúsculas, incluso si son la misma palabra.
```

```{important}
Si se ordena con base a dos o más columnas, se priorizan el orden de los valores de la primer columna que se ponga, en caso de que haya empates en esa columna, esos empates se ordenan con base a los valores de la segunda columna y así con el resto de las columnas.
```

Se utiliza para ordenar los resultados del _query_ con base a una o más columnas. Por default ordena los valores de manera ascendente. 
```sql
-- Ordenar el result set con base a valores de las columnas
SELECT col1 [, col2, ...]
FROM table_name;
ORDER BY col1 [ASC|DESC] [, col2 [ASC|DESC], ...];
```
- _col1, col2, ..._ son los nombres de las columnas. No estrictamente deben de ser seleccionadas en `SELECT`, pero sí deben estar en la tabla del `FROM`.
- _table_name_ es el nombre de la tabla.
- En lugar de usar los nombres de las columnas se pueden usar números (_1, 2, ..._), que hacen referencia a los campos en `SELECT` por orden en los que fueron declarados.
- Se puede ordenar por _columnas calculadas_ en `SELECT` o utilizar los alias para hacer referencia a las columnas.
- Se puede decir explícitamente que se ordene de manera ascendente con `ASC`. <br> `ORDER BY col1 ASC [, col2 ASC, ...]`
- Para ordenar de manera descendente se usa `DESC` después del nombre de cada columna donde se quiera aplicar. <br/> `ORDER BY col1 DESC [, col2 DESC, ...]`.
- Se puede usar `ASC` o `DESC` de manera simultanea en columnas diferentes en un mismo _query_.

**Ejemplos**:
```sql
-- Ordenar simple
SELECT DISTINCT surname
FROM cd.members
ORDER BY surname
LIMIT 10;
```
- [Ordenar simple](https://pgexercises.com/questions/basic/unique.html).

