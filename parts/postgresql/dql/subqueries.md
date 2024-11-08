# Subqueries

Los _subqueries_ son _queries_ anidados, es decir un _query_ dentro de otro _query_. Los _queries_ internos se encierran entre paréntesis. Se pueden usar en las sentencias `WHERE`, `SELECT`, `FROM`, `GROUP BY`, etc. Se ejecutan antes que el _main query_. Pueden retornar valores escalares, columnas y tablas.

Cuando usar cada caso:
- `SELECT`: Cuando el _subquerie_ va a regresar un solo valor, se usa para hacer cálculos. Es importante tener en cuenta que, si se van a filtrar los datos, se necesita filtrarlos tanto en el _subquery_ como en el _query_ principal.
- `FROM`: Cuando se quiere transformar o filtrar una tabla que se puede usar para seleccionar campos o unir con otras tablas. También se puede utilizar para calcular _aggregates de aggregates_ (como los 3 máximos promedios). Es necesario ponerles un alias. En `SELECT` se tiene que seleccionar al menos la columna que se usará como _id_ en el _join_ y las otras que se usarán para cualquier otra cosa.
- `WHERE`: Cuando se quiere filtrar información que se tiene que calcular aparte o para generar una lista que se pueda usar para filtrar, que se use con `IN`, `ANY`, `ALL`, etc.

```{warning}
No se puede usar la sentencia `ORDER BY` en un _subquerie_.
```

Algunas buenas prácticas para trabajar con _subqueries_ son:
- Hacer comentarios de qué hacen.
- Indentar de manera correcta los queries.

<br/>

**Ejemplos**:

```sql
-- Subquery en FROM
SELECT member, facility, cost
FROM (SELECT m.firstname || ' ' || m.surname AS member,
   f.name AS facility,
    b.starttime AS ddate,
   CASE WHEN b.memid = 0 THEN
    b.slots * f.guestcost
  ELSE
   b.slots * f.membercost
  END AS cost
    FROM cd.bookings AS b
      LEFT JOIN cd.facilities AS f
    ON b.facid = f.facid
    LEFT JOIN cd.members AS m
    ON b.memid = m.memid) AS t
WHERE (ddate BETWEEN '2012-09-14' AND '2012-09-15') AND cost > 30
ORDER BY cost DESC;
```
- [Subquery en FROM](https://pgexercises.com/questions/joins/tjsub.html): En este ejemplo, primero se construye una tabla que contiene ciertas modificaciones con respecto a las tablas orginales, y a partir de esta nueva tabla se genera el _query_, esto con el fin de enlistar los _facilities_ costosos.

<br/>

---
## Correlated Subqueries

Son _subqueries_ que utilizan información del _outer query_, por lo tanto, no se puede correr de forma independiente, por lo que usa un _loop_ para evaluar el _subquery_ por cada fila del _outer query_, esto provoca que sea más lento.

El _subquerie_ se ejecuta una vez por cada fila en el _outer query_, a diferencia de un _subquery_ normal que solo se ejecuta una vez.

Es útil cuando hay limitaciones al unir _queries_ y _subqueries_.

<br/>

**Ejemplos**:

```sql
-- Correlated subquery en SELECT
SELECT DISTINCT m1.firstname || ' ' || m1.surname AS member, 
  (SELECT m2.firstname || ' ' || m2.surname
  FROM cd.members AS m2
  WHERE m1.recommendedby = m2.memid) AS recommender
FROM cd.members AS m1
ORDER BY member;

-- Ejemplo de un correlated subquery
SELECT 
 main.country_id,
    main.date,
    main.home_goal, 
    main.away_goal
FROM match AS main
WHERE 
 -- Filtrar el main query con el subquery
 (home_goal + away_goal) > 
        (SELECT AVG((sub.home_goal + sub.away_goal) * 3)
         FROM match AS sub
         WHERE main.country_id = sub.country_id);
```
- [Correlated subquery en SELECT](https://pgexercises.com/questions/joins/sub.html): En este ejemplo se utiliza un _correlated subquery_ para hacer un _join_ sin usar explícitamente un _join_. Básicamente para cada fila del _outer query_, verifica los registros en los que `m1.recommendedby` es igual a `m2.memid` del _inner query_, en el ejemplo esa relación es 1-1 o 1-0, por lo que por cada registro del _outer query_ retorna 1 o 0 registros. Ver imagen de referencia.
- En el otro ejemplo se utiliza un _correlated subquerie_ para examinar los partidos con resultados que son valores atípicos para cada país, es decir, 3 veces el promedio.

```{image} ../images/correlated-subquery.gif
:name: correlated-subquery-gif
:width: 400px
:align: center
```

<br/>

---
(subquery-any)=
## ANY

Se utiliza para comparar un valor escalar con grupo de valores retornados por un _subquerie_ (que debe de retornar únicamente una columna). Normalmente va en `WHERE`. Es equivalente a `IN`. Retorna los valores de _col_ en los que la comparación se cumple con al menos un valor del _subquerie_.
```sql
-- Verificar que al menos un registro satisfaga una condición
SELECT col1, col2, ...
FROM table_name
WHERE col OPERATOR ANY(subquery ...)
```
- _col_ es el nombre de una columna.
- _operator_ es cualquier operador que utilice {ref}`operadores-comparacion`.
- Equivale a usar `SOME`, es un sinónimo de `ANY`.

<br/>

---
(subquery-all)=
## ALL

Se utiliza para comparar un valor escalar con grupo de valores retornados por un _subquerie_ (que debe de retornar únicamente una columna). Normalmente va en `WHERE`. Retorna los valores de _col_ en los que la comparación se cumple con todos los valores del _subquerie_.
```sql
-- Verificar que todos los registros satisfagan una condición
SELECT col1, col2, ...
FROM table_name
WHERE col OPERATOR ALL(subquery ...)
```
- _col_ es el nombre de una columna.
- _operator_ es cualquier operador que utilice {ref}`operadores-comparacion`.

<br/>

---
(subquery-exists)=
## EXISTS

Se utiliza normalmente para verificar la existencia de filas en algún _subquery_. Normalmente se utiliza en `WHERE`. Si sí existen filas en el _subquery_ regresará `TRUE`, en caso contrario `FALSE`.
```sql
-- Verificar la existencia de registros
query...
WHERE EXISTS (subquery ...)

-- Verificar la no existencia registros
query...
WHERE NOT EXISTS (subquery ...)
```
- Existe su equivalente `NOT EXISTS`, que devuelve `TRUE` si está vacío y `FALSE` si no está vacío.
