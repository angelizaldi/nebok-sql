# Expresiones condicionales

## CASE WHEN

Es como un _IF-ELSE_ anidado, sirve para verificar que algún valor cumpla determinadas condiciones y con base a eso devolver algo. Normalmente va en `SELECT`, pero se puede poner en otros _statements_ incluyendo dentro de funciones como _aggregates_. En esencia genera un resultado por cada fila.
```sql
CASE 
WHEN condition1 THEN result1
		WHEN condition2 THEN result2
		...
		[ELSE default_result]
END AS name
```
-	Dependiento de cuál condición se haya cumplido, entonces se ejecutará _result1_, _result2_, etc.
-	Todos los _result _deben de ser todos del mismo tipo.
-	Se puede poner un `ELSE` en caso de que no se haya cumplido ninguna condición. Si no se pone y no se cumplió ninguna condicón se retornará `NULL`.
-	Se puede poner más de una condición con operadores lógicos.
-	Es necesario darle un alias cuando se pone en `SELECT`, pero en `WHERE` o dentro de alguna función no se le pone nombre.

Algunas funcionalidades:
-	Para categorizar variables numéricas.
-	Para poder filtrar los resultados de un `CASE` en `SELECT` es necesario escribir todo el `CASE` en `WHERE` y definir la condición ahí mismo. 
-	Dentro de _aggregate functions_, el valor de `THEN` dependerá de la función, si es `COUNT()`, puede ser cualquier cosa como una columna, un número, una expresión, si es `SUM()` tiene que devolver lo que va a sumar, si es `AVG()` es lo que vas a promediar o resultados de 1 y 0 para calcular porcentajes, etc.
-	Dentro de `AVG()` para calcular el porcentaje que cumple una condición, para ello en los `THEN` poner 1 o 0 dependiendo de la condición y `AVG()` calculará el porcentaje.

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
```
- [Categorización simple](https://pgexercises.com/questions/basic/classify.html).
