# Conceptos y teoría

## Comandos de SQL.

Los comandos de SQL son instrucciones, se usan para comunicarse con la base de datos y para realizar acciones específicas, funciones y queries. Existen cinco principales tipos de comandos en SQL:
-	**Data Definition Language (DDL)**: Son los comandos relacionados con crear, alterar o eliminar información, están asociados a todo el database o al sever. Estos comandos son _'auto-committed'_, lo que significan que los cambios se guardan permanentemente. Los principales comandos son `CREATE`, `ALTER`, `DROP` y `TRUNCATE`.
-	**Data Manipulation Language (DML)**: Son los comandos para manipular la información de las tablas, son responsables de todas las modificaciones en la base de datos. Estos comandos son _'not auto-committed'_, lo que significan que los cambios son reversibles. Los principales comandos son `INSERT`, `UPDATE` y `DELETE`.
-	**Data Control Language (DCL)**: Son los comandos relacionados con otorgar o quitar privilegios a los usuarios para manipular las bases de datos. Los principales comandos son `GRANT` y `REVOKE`.
-	**Transaction Control Language (TCL)**: Son comandos que solo se pueden usar con comandos _DML_, se realizan automáticamente. Los principales comandos son `COMMIT`, `ROLLBACK` y `SAVEPOINT`.
-	**Data Query Language (DQL)**: Son los comandos para obtener datos de la base de datos. El principal comando es `SELECT`.

<br/>

---
## Queries.

Un _query_ es una solicitud de información en una base de datos. Los _queries_ están constituidos por _statements_, que son palabras especiales que realizan determinada acción.

La mayoría de las acciones en SQL se hacen a través de los _statements_. SQL no es sensible a mayúsculas y minúsculas, pero se recomienda poner los _statements_ en mayúsculas, así como el nombre de las funciones.

<br/>


### Orden de ejecución de un query:

El orden con el que se ejecutan las sentencias en un query de consulta es:
1. `FROM`
    1.	`JOIN`
2.	`WHERE`
3.	`GROUP BY`
    1.	`HAVING`
4.	_Aggregations functions_ (`SUM`, `COUNT`, `AVG`, etc.)
5.	`SELECT`
6.	`DISTINCT`.
7.	`ORDER BY`
8.	`LIMIT`

<br/>

### Orden al escribir queries:

Al escribir queries en PostgreSQL se debe respetar el siguiente orden:

1.	`SELECT`
2.	`FROM`
    1.	`JOIN`
3.	`WHERE`
4.	`GROUP BY`
    1.	`HAVING`
5.	`ORDER BY`
6.	`LIMIT`

### Mejores prácticas al escribir queries:

-	Finalizar el query con punto y coma (`;`).
-	Utiliza alias (`AS`) siempre.
-	Especificar explícitamente el tipo de _JOIN_ (solo usar `JOIN` es lo mismo que usar `INNER JOIN`, pero se recomienda explícitamente decir qué tipo de _JOIN_ es).
-	Los alias de las tablas en _JOIN_ se recomienda que sean la primer letra o una corta abreviación del nombre de la palabra.
-	Usar comentarios para decir qué hace el _query_ y aspectos importantes. Ya sean simples (`--`) o de múltiples líneas (`/* texto */`).
-	Poner en mayúsculas los comandos de SQL, ejemplo, `SELECT`, `FROM`, `WHERE`, etc.
-	Procurar no poner todo en una solo línea, como los `JOIN` y `ON` o las columnas en `SELECT`.
-	Identar las líneas, por ejemplo, múltiples columnas en `SELECT`.
-	Se recomienda usar _snake\_case_, esto es, para nombres largos usar '_' como separador de palabra. Ejemplo, "esto_es_un_ejemplo", en lugar de "estoesunejemplo" o "EstoEsUnEjemplo" (no usar _CamelCase_).
-	Usar `IN` o `ANY(ARRAY(...))` en lugar de `OR` anidados y `BETWEEN` en lugar de dos `AND`.
-	No abusar de los comentarios.
-	De preferencia no usar `*` en `SELECT`. Limitar los resultados.
-	No usar SQL como un lenguaje de programación, no lo es.
 
<br/>

---
## Subqueries:

Los _subqueries_ son _queries_ anidados, es decir un _query_ dentro de otro _query_. Los _queries_ internos se encierran entre paréntesis. Se pueden usar en las sentencias `WHERE`, `SELECT`, `FROM`, `GROUP BY`, etc. Se ejecutan antes que el _main query_. Pueden retornar valores escalares, columnas y tablas.

Cuando usar cada caso:
-	`SELECT`: Cuando el _subquerie_ va a regresar un solo valor, se usa para hacer cálculos. Es importante tener en cuenta que, si se van a filtrar los datos, se necesita filtrarlos tanto en el _subquery_ como en el _query_ principal.
-	`FROM`: Cuando se quiere transformar o filtrar una tabla que se puede usar para seleccionar campos o unir con otras tablas. También se puede utilizar para calcular _aggregates de aggregates_ (como los 3 máximos promedios). Es necesario ponerles un alias. En `SELECT` se tiene que seleccionar al menos la columna que se usará como **ID** en el **JOIN** y las otras que se usarán para cualquier otra cosa.
-	`WHERE`: Cuando se quiere filtrar información que se tiene que calcular aparte o para generar una lista que se pueda usar para filtrar, que se use con `IN`, `ANY`, `ALL`.

```{warning}
No se puede usar la sentencia `ORDER BY` en un _subquerie_.
```

Algunas buenas prácticas para trabajar con _subqueries_ son:
-	Hacer comentarios de qué hacen.
-	Indentar de manera correcta los queries.

<br/>

### Correlated Subqueries:

Son _subqueries_ que utilizan información del _outer query_, por lo tanto, no se puede correr de forma independiente, por lo que usa un _loop_ para evaluar el _subquery_ en cada fila , esto provoca que sea más lento.

El _subquerie_ se ejecuta una vez por cada fila en el _outer query_, a diferencia de un _query_ normal que solo se ejecuta una vez.

Es útil cuando hay limitaciones al unir _queries_ y _subqueries_.

<br/>

### Nested subqueries:

Son _subqueries_ dentro de otros _subqueries_ que pueden ser _correlated_ o _uncorrelated_. Son útiles cuando se harán múltiples y secuenciales transformaciones.

<br/>

--- 
## Common Table Expressions:

Son _queries_ declarados antes del _query_ principal. Se utilizan para hacer más sencilla la organización y lectura de los _queries_, también son útiles cuando el resultado de un _subquery_ se utilizará más de una vez en el query principal o para organizar los subqueries de manera secuencial. Las CTEs solo están disponibles en el _query_ en el que se especificaron, no es posible acceder a ellas fuera de ese _query_.

<br/>

### Queries Recursivos:

**Recursión**:

Es el proceso de usar una subrutina, función, procedimiento, etc, que se llama así misma de manera repetitiva hasta que se cumpla una condición. En SQL para hacer recursión se utilizan CTE recursivos.

Un recursive CTE consta de 5 partes:
1.	Definición del CTE.
2.	Definir el primer paso del proceso, al que se le denomina _anchor_.
3.	Usar `UNION ALL`/`UNION`
4.	Definir el proceso recursivo, al que se le denomina _recursive_, que hace referencia hacia sí mismo (al mismo CTE). 
5.	Llamar al _recursive CTE_ desde el _query_ principal.

SQL ejecuta el _recursive CTE_ de la siguiente manera:

1.	Ejecuta el _anchor_, dando un resultado $R_0$.
2.	Ejecuta el _recursive_ iterativamente, usando a $R_{i-1}$ como input y retorna $R_i$.
3.	Se repite el paso dos hasta que un set vacío sea retornado o hasta que se cumple una condición (_termination_).
4.	El resultado final será la unión de todos los resultados _R0_, _R1_, _R2_, etc.

Para una explicación ejemplificada revisar [esta página](https://www.postgresqltutorial.com/postgresql-recursive-query/).
