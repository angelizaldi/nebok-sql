# Agrupar

En esta sección se presentan las principales clausulas relacionadas con agrupar datos y realizar cálculos agregados con esos datos agrupados.

## GROUP BY

Agrupa los resultados de acuerdo a los valores únicos de una o más columnas. Normalmente se usa con {ref}`funciones-aggregate`, aunque no necesariamente. Las columnas que se utilicen en `GROUP BY` deben de ser seleccionadas en `SELECT` y no son a las que se les hará un _aggregate_. Esta sentencia va después de `WHERE`.
```sql
-- Plantilla básica del uso de GROUP BY
SELECT col1 [, col2, ...] [,aggregate_function(coli), ...]
FROM table_name
[WHERE ...]
GROUP BY col1 [, col2, ...]
```
- _col1, col2, ..._ son columnas. No pueden ser las que se usarán con las _aggregate functions_. En caso de que haya _aggregate functions_, todas las columnas que no se usarán con _aggregates functions_ deben de ir en `GROUP BY` (a veces no es necesario, cuando las relaciones entre las columnas son uno a uno).
- _aggregate_function(col)_ es el nombre de una {ref}`funciones-aggregate`.
- En PostgreSQL se puede usar los alias de las columnas en `GROUP BY`.
- En lugar de usar los nombres de las columnas se puede usar números (1, 2, ...), que hacen referencia a los campos en `SELECT` por el orden en los que fueron declarados.

<br/>

**Ejemplos**:

```sql
-- GROUP BY simple
SELECT facid, SUM(slots)
FROM cd.bookings
GROUP BY facid
ORDER BY facid;

-- GROUP BY múltiple y uso de alias
SELECT facid, EXTRACT(month FROM starttime) AS month, SUM(slots) AS "Total Slots"
FROM cd.bookings
WHERE EXTRACT(year FROM starttime) = 2012
GROUP BY facid, month
ORDER BY facid, month;
```
- [GROUP BY simple](https://pgexercises.com/questions/aggregates/fachours.html): En este se enlista el total de _slots_ por _facid_.
- [GROUP BY múltiple](https://pgexercises.com/questions/aggregates/fachoursbymonth2.html): En este ejemplo se enlista el total de _slots_ por _facid_ y por mes. Notar que en PostgreSQL se pueden usar los _alias_ de columnas en `GROUP BY`.

<br/>

---
## HAVING

Es un sustituto de `WHERE`, se usa cuando se usan _aggregate functions_ y `GROUP BY`, va después de `GROUP BY`. Se utiliza el _aggregate function_ como tal, no se puede poner el alias que se le dio al _aggregate_.
```sql
-- Plantilla básica del uso de HAVING
SELECT col1 [, col2, ...] [,aggregate_function(coli), ...]
FROM table_name
[WHERE ...]
GROUPY BY col1 [, col2, ...]
HAVING aggregate_function(coli) condición[, ...]
```
- _aggregate_function(col)_ es el nombre de la _agregate function_ que se utilizó en `SELECT` y la columna que se utilizó en la misma. No se pueden usar alias.
- _condición_ es cualquier comparación que utilice {ref}`operadores-comparacion` o cualquier otra expresión que retorne valores `BOOL`.

<br/>

**Ejemplos**:

```sql
-- Uso de HAVING
SELECT facid, SUM(slots) AS "Total Slots"
FROM cd.bookings
GROUP BY facid
HAVING SUM(slots) > 1000
ORDER BY facid;
```
- [HAVING](https://pgexercises.com/questions/aggregates/fachours1a.html): En este ejemplo se enlistan los _facilities_ que tienen más de 1000 _slots_ en total.

<br/>

---
## GROUPING SETS

Un _grouping set_ es un conjunto de columnas por las cuales se agrupa un _dataset_. Se calcula una _aggregate function_ con base a los valores únicos de ese _grouping set_, para ello se utiliza `GROUP BY`.

`GROUPING SETS` permite definir múltiples _grouping sets_ en un mismo _query_. Lo que permite mostrar la _aggregate function_ para distintos `GROUP BY` de manera más sencilla.
```sql
-- Plantilla de uso de GROUPING SETS
SELECT col1, col2, aggregate_function(coli)[, ...]
FROM table_name
GROUP BY GROUPING SETS((col1, col2), (col1), (col2), ())
```
- En esta plantilla se usan dos columnas para calcular los _grouping sets_, pero pueden ser más.
- _col1, col2_ tuvieron que haber sido seleccionadas en `SELECT`.
- `(col1, col2)`: Indica que se calculen los agregados de las combinaciones de valores únicos de _col1_ y _col2_. Equivaldría a: <br> `GROUP BY col1, col2`
- `(col1)`: Indica que se calculen los agregados para los valores únicos de _col1_, la misma lógica se aplica para `(col2)`. Equivaldría a: <br> `GROUP BY col1`
- `()`: Indica que se calcule el agregado de todos los valores. Equivaldría a no poner un `GROUP BY`.
-	No necesariamente se tiene que incluir los cuatro _grouping sets_, es solo un ejemplo de cómo se deben de poner, pero se puede poner desde uno, hasta los cuatro.


Para conceptualizar cómo funcionan los _grouping sets_ considerar que se tiene la siguiente tabla:

```{image} ../images/grouping-sets-long.png
:name: grouping-sets-long
:width: 125px
:align: center
``` 

Si se calcula un _pivot tabla_ de la tabla anterior, con el _aggregate_ `COUNT()`, donde además se cálcula los totales de filas, columnas y el gran total obtendríamos una tabla similar a la tabla izquierda en la siguiente imagen:

```{image} ../images/grouping-sets.png
:name: grouping-sets
:width: 500px
:align: center
``` 

La tabla de la derecha sería su equivalente en _SQL_ y que es resultado de aplicar _grouping sets_.
- `(col1, col2)`: Los agregados de las combinaciones de valores únicos de _col1_ y _col2_. Color azul claro <span style="color: #aad4d9">■</span>.
- `(col1)` y `(col2)`: Los agregados para los valores únicos de _col1_ y _col2_ respectivamente. Áreas azúl oscuro <span style="color: #30adb9">■</span> para _col1_ y café claro <span style="color: #cf9975">■</span> para _col2_.
- `()`: Los agregados de todo. Equivaldría a no poner un `GROUP BY`. Área café oscuro <span style="color: #ba6a30">■</span>.

<br/>

**Ejemplos**:
```sql
-- GROUPING SETS
SELECT facid, EXTRACT('month' FROM starttime) AS month, SUM(slots)
FROM cd.bookings
WHERE EXTRACT('year' FROM starttime) = 2012
GROUP BY GROUPING SETS((facid, month), (facid), ())
ORDER BY facid, month;
```
- [GROUPING SETS](https://pgexercises.com/questions/aggregates/fachoursbymonth3.html): En este ejemplo se enlistan el total de _slots_ por _facid_ y mes. Notar que el mismo resultado se podría obtener con un `ROLLUP(facid, month)`

<br/>

---
## CUBE
Es una subclase de `GROUP BY`, permite generar múltiples _groupings sets_ por default. Calcula los agregados de todas las combinaciones de valores únicos de las columnas que se pasen a la función.
```sql
-- Plantilla de uso de CUBE
SELECT col1, col2, ..., aggregate_function(coli)[, ...]
FROM table_name
GROUP BY CUBE(col1, col2, ...)
```
- `col1, col2, ...` tuvieron que haber sido seleccionadas en `SELECT`. No pueden ser las columnas a las que se les calculó un _aggregate function_.
- Por ejemplo, `GROUP BY CUBE(col1, col2, col3)` equivaldría a: <br> `GROUPING SETS ((c1, c2, c3), (c1, c2), (c1, c3), (c2, c3), (c1), (c2), (c3), ())` 

Se puede hacer `CUBE` parciales, por ejemplo:
```sql
-- Plantilla de uso de CUBE parcial
SELECT col1, col2, aggregate_function(coli)[, ...]
FROM table_name
GROUP BY col1, CUBE(col2, col3)
```
- En este ejemplo no se calcularían los totales para _col2_ ni _col3_, ni los grandes totales. Equivaldría a: <br> `GROUPING SETS ((c1, c2, c3), (c1, c2), (c1, c3), (c1))`
- Se puede pensar en esto como si primero se hace las combinaciones de `CUBE(col2, col3)` y después a cada resultado se le _concatena_ el _c1_ al principio.

<br/>

---
## ROLLUP
Es una subclase de `GROUP BY`, permite generar múltiples _groupings sets_. `ROLLUP` asume jerarquía entre las columnas, lo que permite que se puedan calcular subtotales y totales de manera sencilla. Utilizar solo cuando los datos están jerarquizados.
```sql
-- Plantilla de uso de ROLLUP
SELECT col1, col2, ..., aggregate_function(coli)[, ...]
FROM table_name
GROUP BY ROLLUP(col1, col2, ...)
```
- `col1, col2, ...` tuvieron que haber sido seleccionadas en `SELECT`. No pueden ser las columnas a las que se les calculó un _aggregate function_.
- Por ejemplo, `GROUP BY ROLLUP(col1, col2, col3)` asumiría que _col1 > col2 > col3_ (jerárquicamente), entonces equivaldría a: <br> `GROUPING SETS ((c1, c2, c3), (c1, c2), (c1), ())`

Se puede hacer `ROLLUP` parciales, por ejemplo:
```sql
-- ROLLUP parcial
SELECT col1, col2, col3, aggregate_function(coli)[, ...]
FROM table_name
GROUP BY col1, ROLLUP(col2, col3)
```
- En este ejemplo no se calcularían los subtotales para _c2_ ni los grandes totales. Equivaldría a: <br> `GROUPING SETS ((c1, c2, c3), (c1, c2), (c1))`
- Se puede pensar en esto como que primero se hace las combinaciones de `ROLLUP(col2, col3)` y después a cada resultado se le _concatena_ el _c1_ al principio.

<br/>

**Ejemplos**:
```sql
-- ROLLUP
SELECT facid, EXTRACT('month' FROM starttime) AS month, SUM(slots)
FROM cd.bookings
WHERE EXTRACT('year' FROM starttime) = 2012
GROUP BY ROLLUP(facid, month)
ORDER BY facid, month;
```
- [ROLLUP](https://pgexercises.com/questions/aggregates/fachoursbymonth3.html): En este ejemplo se enlistan el total de _slots_ por _facid_ y mes. Notar que el mismo resultado se podría obtener con un `GROUPING SETS((facid, month), (facid), ())`