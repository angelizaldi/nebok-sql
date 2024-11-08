# Common Table Expression (CTE)

Son _queries_ declarados antes del _query_ principal. Se utilizan para hacer más sencilla la organización y lectura de los _queries_, también son útiles cuando el resultado de un _subquery_ se utilizará más de una vez en el query principal o para organizar los subqueries de manera secuencial. Las CTEs solo están disponibles en el _query_ en el que se especificaron, no es posible acceder a ellas fuera de ese _query_.

La sintaxis para crear una _CTE_ es:
```sql
-- Plantilla básica CTE
WITH cte_name [(col1, col2, ...)] AS (
 subquery)

SELECT *
FROM cte_name;

-- Definir múltiples CTEs
WITH cte_name1 AS (
 subquery),

cte_name2 AS(
subquery), 
...

SELECT col1, col2, ...
FROM ...;
```
- _cte_name_: Es el nombre que se le dio al CTE.
- _col1, col2, ..._: Son los nombres de las columnas que tendrá la CTE, es opcional ponerlo. Si no se pone, se usarán los nombres de las columnas del _subquery_.
- _subquery_: Es un _query_ que resulte en una tabla.
- Se puede hacer _joins_ de CTEs y otras tablas u otros CTEs.
- Se puede poner más de un CTE, únicamente separa con coma después del paréntesis:

<br/>

**Ejemplos**:

```sql
-- Uso de CTE para evitar doble subquery
WITH total_slots (facid, "Total Slots") AS (
  SELECT facid, SUM(slots)	
  FROM cd.bookings
  GROUP BY facid)

SELECT facid, "Total Slots"
FROM total_slots
WHERE "Total Slots" = (SELECT MAX("Total Slots") FROM total_slots);
```
- [Evitar doble subquery](https://pgexercises.com/questions/aggregates/fachours2.html): En este ejemplo, se puede observar el uso de CTE, que permite evitar tener que escribir dos _subqueries_, esto con el fin de mostar el _id_ del _facility_ con el mayor número de _slots_ reservados.

## Queries Recursivos

**Recursión** Es el proceso de usar una subrutina, función, procedimiento, etc, que se llama así misma de manera repetitiva hasta que se cumpla una condición. En SQL para hacer recursión se utilizan CTE recursivos.

Un _recursive CTE_ consta de 5 partes:
1. Definición del CTE.
2. Definir el primer paso del proceso, al que se le denomina _anchor_.
3. Usar `UNION ALL`/`UNION`
4. Definir el proceso recursivo, al que se le denomina _recursive_, que hace referencia hacia sí mismo (al mismo CTE).
5. Llamar al _recursive CTE_ desde el _query_ principal.

<br/>

SQL ejecuta el _recursive CTE_ de la siguiente manera:

1. Ejecuta el _anchor_, dando un resultado $R_0$.
2. Ejecuta el _recursive_ iterativamente, usando a $R_{i-1}$ como input y retorna $R_i$.
3. Se repite el paso dos hasta que un set vacío sea retornado o hasta que se cumple una condición (_termination_).
4. El resultado final será la unión de todos los resultados _R0_, _R1_, _R2_, etc.

Para una explicación ejemplificada revisar [esta página](https://www.postgresqltutorial.com/postgresql-recursive-query/).

La sintaxis para crear un query recursivo es
```sql
-- Plantilla básica de un recursive CTE
WITH RECURSIVE nomCTE AS(
CTE_query -- Parte no recursiva, anchor.
UNION [ALL]
CTE_query -- Parte recursiva, recursive.
[condicional] -- Termination
)
SELECT * FROM nomCTE;
```
- En la parte recursiva, el `FROM` será el mismo _CTE_, o sea,  _nomCTE_.
- Se puede usar `UNION` o `UNION ALL`, dependiendo como se quiera tratar a los registros duplicados.
- _condicional_ es una condición para detener la recursión, es opcional y debe de utilizar como variable a las mismas que se usan en el _anchor_ y _recursive_.
- No se puede usar `GROUP BY`, `HAVING`, `LEFT JOIN`, `RIGHT JOIN`, `OUTER JOIN`, `SELECT DISTINCT`, _Subqueires_ ni `LIMIT`.
- El número de columnas y los tipos de datos de las columnas deben de ser las mismas tanto en la parte de _anchor_ como en _recursive_.

<br/>

**Ejemplos**

```sql
-- Query recursivo simple 1
 WITH RECURSIVE recommenders(recommender) AS
(
       SELECT recommendedby
       FROM   cd.members
       WHERE  memid = 27
       UNION ALL
       SELECT     mems.recommendedby
       FROM       recommenders recs
       INNER JOIN cd.members mems
       ON         mems.memid = recs.recommender)

SELECT     recs.recommender,
           mems.firstname,
           mems.surname
FROM       recommenders recs
INNER JOIN cd.members mems
ON         recs.recommender = mems.memid
ORDER BY   memid DESC

-- Query recursivo simple 2
 WITH recursive downward AS
(
       SELECT memid
       FROM   cd.members
       WHERE  recommendedby = 1
       UNION
       SELECT     m.memid
       FROM       downward   AS d
       INNER JOIN cd.members AS m
       ON         d.memid = m.recommendedby)

SELECT     d.memid,
           m.firstname,
           m.surname
FROM       downward   AS d
INNER JOIN cd.members AS m
using     (memid)
ORDER BY   memid; 
```
- [Recursivo simple 1](https://pgexercises.com/questions/recursive/getupward.html): En este ejemplo en la parte recursiva se recupera quién recomendó a cada miembro, partiendo del miembro 27, notar que el nombre de la columnas _recommender_ se estableció en la CTE y no en `SELECT`. Posteriormente ya con los id de los miembros que recomendaron en cadena, se recupera sus nombres.
- [Recursivo simple 2](https://pgexercises.com/questions/recursive/getdownward.html): En este ejemplo en la parte recursiva se recupera todos los miembros que fueron recomendados por otro, partiendo del miembro 1. Notar que en la parte de _recursive_ la relación entre las tablas potencialmente es _one-to-many_. Posteriormente ya con los id de los miembros recomendados en cadena, se recupera sus nombres.