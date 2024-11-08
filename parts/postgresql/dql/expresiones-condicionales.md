# Sentencias condicionales

## CASE WHEN

Es como un _If-Else_ anidado, sirve para verificar que algún valor cumpla determinadas condiciones y con base al resultado retornar algo. Normalmente va en `SELECT`, pero se puede poner en otras cláusulas, incluso dentro de funciones como _aggregates_. Se evalúa de manera iterativa, fila por fila.
```sql
-- Plantilla de CASE WHEN
CASE 
    WHEN condition1 THEN result1
	WHEN condition2 THEN result2
	...
	[ELSE else_result]
END [AS name]
```
-	Dependiento de cuál condición se haya cumplido, entonces se retornará _result1_, _result2_, etc.
-	Todos los _result_ deben de ser todos del mismo tipo de dato.
-	Se puede poner un `ELSE` en caso de que no se haya cumplido ninguna condición. Si no se pone y no se cumplió ninguna condición se retornará `NULL`.
-	Se puede poner más de una condición con operadores {ref}`operadores-logicos`.
-	Es necesario darle un alias cuando se pone en `SELECT`, pero en `WHERE` o dentro de alguna función no se le pone alias.

:::{caution}
Para poder filtrar los resultados de un `CASE` en `SELECT` es necesario escribir todo el `CASE` en `WHERE` y definir la condición ahí mismo. 
:::

Algunas funcionalidades:
-	Para categorizar variables numéricas.
-	Dentro de _aggregate functions_, el valor de `THEN` dependerá de la función, si es `COUNT()`, puede ser cualquier cosa como una columna, un número, una expresión, si es `SUM()` tiene que devolver lo que va a sumar, si es `AVG()` es lo que vas a promediar o resultados de 1 y 0 para calcular porcentajes, etc.

<br/>

**Ejemplos**:

```SQL
-- Ejemplo de categorización
SELECT name,
	CASE WHEN (monthlymaintenance > 100) THEN
		'expensive'
	ELSE 
		'cheap'
    END AS cost
FROM cd. facilities;


-- Ejemplo filtrado en WHERE
SELECT m.firstname || ' ' || m.surname AS member,
	   f.name AS facility,
	   CASE WHEN b.memid = 0 THEN
			b.slots * f.guestcost
	   ELSE
			b.slots * f.membercost
	   END AS cost
FROM cd.bookings AS b
LEFT JOIN cd.facilities AS f
ON b.facid = f.facid
LEFT JOIN cd.members AS m
ON b.memid = m.memid
WHERE (b.starttime BETWEEN '2012-09-14' AND '2012-09-15') 
	AND (CASE WHEN b.memid = 0 THEN
			b.slots * f.guestcost
	     ELSE
			b.slots * f.membercost
	     END > 30);


-- Ejemplo en una función aggregate
SELECT facs.name, sum(slots * CASE
			WHEN memid = 0 THEN facs.guestcost
			ELSE facs.membercost
		END) AS revenue
	FROM cd.bookings bks
	INNER JOIN cd.facilities facs
		ON bks.facid = facs.facid
	GROUP BY facs.name
ORDER BY revenue;  
```
- [Categorización simple](https://pgexercises.com/questions/basic/classify.html): En este ejemplo se realiza una categorización con base al _monthlymaintenance_.
- [Como filtro en WHERE](https://pgexercises.com/questions/joins/threejoin2.html): En este ejmplo se enlistan _facilities_ con un costo mayor a 30. Existe una alternativa para no poner el `CASE WHEN` en `WHERE`, revisar la solución en el link.
- [En una función aggregate](https://pgexercises.com/questions/aggregates/facrev.html): En ese ejemplo vemos el uso de un `CASE WHEN` dentro de una función, esto con el fin de calcular el ingreso total por _facility_.
