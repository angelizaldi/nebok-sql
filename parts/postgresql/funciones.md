# Funciones

## Aggregate Functions.

-	Sirven para hacer cálculos con los datos en el _database_. 
-	Devuelven un solo valor.
-	Para cada columna se debe de volver a usar la función. 
-	Es muy recomendable usar `AS` para darle nombre.
-	Estas funciones son iguales en todas las plataformas de SQL. 
-	Se pueden usar en únicamente las sentencias `SELECT` y `HAVING`.

```{note}
Los valores `NULL` son ignorados en este tipo de funciones.
```

`AVG()` \- `NUMERIC`: Calcula el promedio de una columna.
```sql
AVG(col)
```
- `col` \- `NUMERIC`: Es la expresión o columna.

<br>

`ARRAY_AGG()` \- `ARRAY`: Recibe un conjunto de valores y retorna un _array_, en el cual cada elemento en el conjunto es asignado a un elemento del _array_. Es posible ordenar los valores antes de conformar el _array_.
```sql
ARRAY_AGG(expression [ORDER BY [sort_expression {ASC | DESC}], [...])
```
- `col` \- `VALUE`: Es la expresión o columna.
- _expression_: Es una expresión que retorna un valor de cualquier tipo aceptado por un _array_, por ejemplo la concatenación de dos columnas.
- _sort\_expression_ \- `{ASC | DESC}`: Es para indicar como ordenar los valores de _expression_.

<br>

`COUNT()` \- `NUMERIC`: Devuelve el número de filas en una columna/s específica de una tabla.
```sql
COUNT(col)
```
- `col` \- `VARIANT`: Es la expresión o columna.
- Casos especiales:
  - `COUNT(*)`: El número total de filas que tiene una tabla.
  - `COUNT(col)`: El número de valores no `NULL` de una columna.
  - `COUNT(DISTINCT col)`: El número de valores únicos que tienen una columna. Sin incluir `NULL`.

<br>

`SUM()` \- `NUMERIC`: Calcula la suma de una columna.
```sql
SUM(col)
```
- `col` \- `NUMERIC`: Es la expresión o columna.
- Es posible sumar valores distintos si se agrega la palabra `DISTINCT`.

<br>

`MAX()` \- `NUMERIC`: Calcula el valor máximo de una columna. Si son caracteres será por orden alfabético.
```sql
MAX(col)
```
- `col` \- `NUMERIC`, `TEXT`: Es la expresión o columna.

<br>

`MIN()` \- `NUMERIC`: Calcula el valor mínimo de una columna. Si son caracteres será por orden alfabético.
```sql
MIN(col)
```
- `col` \- `NUMERIC`, `TEXT`: Es la expresión o columna.

<br>

**Ejemplos**
```sql
-- Uso de MAX
SELECT MAX(joindate) AS latest
FROM cd.members;
```
- [Uso de MAX](https://pgexercises.com/questions/basic/agg.html).

