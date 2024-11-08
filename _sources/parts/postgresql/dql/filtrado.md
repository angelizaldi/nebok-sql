# Filtrar

En esta sección se presenta brevemente la cláusula relacionada para filtrar los datos en un _query_ y algunas otras cláusulas útiles para crear condicionales de filtrado.

## WHERE

Permite filtrar valores, ya sean valores numéricos, texto, fecha, etc. Va después de `FROM` y antes de `GROUP BY`. 
```sql
-- Uso general de WHERE
SELECT col1 [, col2, ...]
FROM table_name
WHERE condition;

-- WHERE con múltiples condiciones, ejemplo con AND
SELECT col1 [, col2, ...]
FROM table_name
WHERE (condition1) AND (condition2);
```
- _col1, col2, ..._ son los nombres de las columnas. No estrictamente deben de ser seleccionadas en `SELECT`, pero sí deben estar en la tabla del `FROM`.
    - **IMPORTANTE**: No se puede usar alias de columnas en `WHERE`. En caso que sea una columna calculada se tiene que poner el cálculo completo.
- _table_name_ es el nombre de la tabla.
- _conditions_ son un conjunto de condiciones que indican cuáles filas mostrar y cuáles no. Estas condiciones se suelen definir utilizando las mismas columnas de las tablas y {ref}`operadores-comparacion` u {ref}`operadores-membresia`.
- Se pueden anidar varias condiciones con los operadores Lógicos `AND` u `OR`.
- Se pueden utilizar funciones dentro de las condiciones.
- Si la condición es de tipo fecha, usar una cadena con la fecha respetando ISO 8601. <br/> `WHERE date_column >= 'YYYY-MM-DD';`
- Si son más de una condición se recomienda usar paréntesis para diferenciarlas, ejemplo: <br/> `WHERE (condition1) AND (condition2)`

<br/>

**Ejemplos**:

```SQL
-- Ejemplo con una condición
SELECT *
FROM cd.facilities
WHERE membercost > 0;

-- Ejemplo con más de una condición
SELECT facid, name, membercost, monthlymaintenance
FROM cd.facilities
WHERE (membercost > 0) AND (membercost < monthlymaintenance/50);
```
- [Filtrar con una condición](https://pgexercises.com/questions/basic/where.html): En este ejemplo se filtran los registros para mostrar solo aquellos en los que `membercost > 0`.
- [Filtrar con dos condiciones](https://pgexercises.com/questions/basic/where2.html): En este ejemplo se utiliza el operador `AND` para verificar que se cumplan dos condiciones a la vez.

<br/>

---
(dql-filtrado-in)=
## IN

El operador `IN` permite especificar múltiples valores en `WHERE`. Equivale a vectorizar muchas condiciones con `OR`.
```sql
-- Verificar que los valores estén en un conjunto de valores
WHERE col IN (val1, val2, ...)

-- Verificar que los valores NO estén en un conjunto de valores
WHERE col NOT IN (val1, val2, ...)
```
- _val1, val2, ..._ son valores que posiblemente _col_ puede tomar.
- En lugar de poner valores se puede poner un _sub-query_ que retorne una sola columna.
- Se puede negar el vector de posible valores con `NOT`.

<br/>

**Ejemplos**:

```sql
-- Ejemplo de uso de IN ()
SELECT * 
FROM cd.facilities
WHERE facid IN (1, 5);
```
- [Filtrar con una condición](https://pgexercises.com/questions/basic/where4.html): En este ejemplo se verifica que _facid_ sea 1 o 5.

<br/>

---
(dql-filtrado-between)=
## BETWEEN

El operador `BETWEEN` permite verificar que los valores (numéricos, fechas y cadenas) de una columna estén dentro de un rango determinado. Es inclusivo, eso significa que incluye los valores extremos.
```sql
-- Verificar que los valores estén entre un rango
WHERE col BETWEEN val1 AND val2

-- Verificar que los valores NO estén entre un rango
WHERE col NOT BETWEEN val1 AND val2
```
- _val1_ y _val2_ son valores, normalmente numéricos y _val1 < val2_.
- Se puede negar el rango con `NOT`.
- **Importante**: Si _col_ es de tipo `TIMESTAMP` y _val1_ y _val2_ se especifican como _'YYYY-MM-DD'_, entonces en ambos casos tendrán la hora en la media noche (_'YYYY-MM-DD 00\:00:00'_), además considerar que se incluirán ambos extremos. Si no se quiere incluir _val2_ considerar usar _'YYYY-MM-DD 23\:59:59'_

<br/>

**Ejemplos**:

```sql
-- BETWEEN con TIMESTAMP
SELECT b.starttime AS start, f.name AS name 
FROM cd.bookings AS b
INNER JOIN cd.facilities AS f
ON b.facid = f.facid
WHERE (f.name LIKE 'Tennis Court%') AND (b.starttime BETWEEN '2012-09-21' AND '2012-09-22')
ORDER BY start;
```
- [BETWEEN con TIMESTAMP](https://pgexercises.com/questions/joins/simplejoin2.html): En este se filtra para que una columna de tipo `TIMESTAMP`, sea igual a un día determinado ('2012-09-21').

<br/>

---
## IS NULL

Un valor `NULL` (nulo) se utiliza para indicar que hay información perdida. `NULL` no es un valor por lo que no se puede verificar que un valor sea `NULL` con un operador de comparación. Para verificar que un valor sea `NULL` es necesario usar `IS NULL`, que retorna `TRUE` si sí es y `FALSE` si no es.
```sql
-- Verificar los valores que son NULL
WHERE col IS NULL

-- Verificar los valores que NO son NULL
WHERE col IS NOT NULL
```
_col_ es el nombre de alguna columna.

<br/>

---
(dql-filtrado-like)=
## LIKE

Se utiliza junto con `WHERE` para filtrar columnas de caracteres de acuerdo a un patrón, para ello se usan {ref}`anexos-wildcards`.
```sql
-- Verificar que una cadena cumpla un patrón
WHERE col LIKE 'pattern'

-- Verificar que una cadena NO cumpla un patrón
WHERE col NOT LIKE 'pattern'

-- Verificar que una cadena cumpla alguno de varios patrones
WHERE col LIKE ANY(ARRAY['pattern1', 'pattern2', ...])
    
-- Verificar que una cadena cumpla un patrón con notación ~~
WHERE col ~~ 'pattern'

-- Verificar que una cadena NO cumpla un patrón con notación !~~
WHERE col !~~ 'pattern'
```
- _'pattern'_ es una cadena que utiliza {ref}`anexos-wildcards` junto con determinados caracteres que se quiere que tenga el patrón.
- También se puede negar el patrón con el operador `NOT`, para solo elegir los registros que no cumplen cierto patrón.
- Existen operadores `~~` y `!~~` que equivalen a `LIKE` y `NOT LIKE` respectivamente.
- Se puede filtrar con base a más de un patrón utilizando la función `ARRAY()`.

<br/>

**Ejemplos**:

```SQL
-- Ejemplo de LIKE con wildcards
SELECT *
FROM cd.facilities
WHERE name LIKE '%Tennis%'; -- Equivale a: WHERE name ~~ '%Tennis%'
```
- [Filtrar con base a cadenas](https://pgexercises.com/questions/basic/where3.html): En este ejemplo se verifica que _name_
 contenga la palabra _Tennis_.
<br/>

---
## ILIKE

Si en lugar de usar `LIKE` se usa `ILIKE`, entonces las búsquedas que se hagan no serán sensibles a minúsculas y mayúsculas, esto quiere decir que si se busca `'%apple%'` pueden aparecer resultados como _'APPLE'_ o _'Apple'_ o cualquier otra combinación de mayúsculas y minúsculas. 
```sql
-- Verificar que una cadena cumpla un patrón, no sensible a mayúsculas/minúsculas
WHERE col ILIKE 'pattern'

-- Verificar que una cadena NO cumpla un patrón, no sensible a mayúsculas/minúsculas
WHERE col NOT ILIKE 'pattern'

-- Verificar que una cadena cumpla un patrón con notación no sensible a mayúsculas/minúsculas, con notación ~~*
WHERE col ~~* 'pattern'

-- Verificar que una cadena NO cumpla un patrón con notación no sensible a mayúsculas/minúsculas, con notación !~~*
WHERE col NOT !~~* 'pattern'
```
- _'pattern'_ es una cadena que utiliza {ref}`anexos-wildcards` junto con determinados caracteres que se quiere que tenga el patrón.
- También se puede negar el patrón con la sentencia `NOT`, para solo elegir las columnas que no cumplen cierto patrón.
- Existen operadores `~~*` y `!~~*` que equivalen a `ILIKE` y `NOT ILIKE` respectivamente.

<br/>

**Ejemplos**

```sql
-- Uso simple de ILIKE
SELECT *
FROM cd.facilities
WHERE name ILIKE 'tennis%'; -- Equivale a: WHERE name ~~* '%Tennis%'
```
- [Uso simple de ILIKE](https://pgexercises.com/questions/string/case.html): En este ejemplo, se mostrarán todos los resultados cuyo nombre empiece con 'tennis', independientemente de la combinación de letras en mayúsculas y minúsculas.

<br/>

---
## LIMIT

Se utiliza para especificar que se muestren solo los primeros _N_ registros.
```sql
-- Limitar el número de registros retornados
SELECT col [, ...]
FROM table_name
LIMIT N

-- Omitir M registros y mostrar los siguientes N
SELECT col1 [, ...]
FROM table_name
LIMIT N OFFSET M;
```
- Va ubicado al final del _query_, limita el resultado a _N_ filas.
- _N_ es un número entero positivo.
- Se puede especificar que omita los primeros _M_ registros y mostrar los siguientes _N_ registros con `OFFSET`.

<br/>

---
## FETCH

Sirve para limitar el número de filas a mostrar, como `LIMIT`, pero forma parte de _SQL standard_, lo que permite que sea compatible con otras plataformas de SQL.
```sql
-- Uso de FETCH
SELECT col1 [, ...]
FROM table_name
[OFFSET M]
FETCH FIRST [N] ROW|ROWS ONLY;
```
- `OFFSET` es opcional, es para indicar que omita los primeros _M_ registros.
- En lugar de `FIRST` se puede usar `NEXT`, tienen la misma función.
- Si no se pone _N_, por default mostrará solo un registro.
- Se pone `ROW` si _N = 1_, y `ROWS` si _N > 1_.

<br/>

---
(dql-filtrado-posix)=
## POSIX Regular Expressions

Se utiliza junto con `WHERE` para filtrar columnas de caracteres de acuerdo a una expresión regular, para ello se usan {ref}`table-caracteres-especiales`. A diferencia de `SIMILAR TO`, con estos operadores se utiliza la notación común de expresiones regulares. Para realizar este tipo de filtrados se utilizan los siguientes operadores:
- `~`: La cadena coincide con el patrón, sensible a mayúsculas y minúsculas.
- `~*`: La cadena coincide con el patrón, insensible a mayúsculas y minúsculas.
- `!~`: La cadena no coincide con el patrón, sensible a mayúsculas y minúsculas.
- `!~*`: La cadena no coincide con el patrón, insensible a mayúsculas y minúsculas.

:::{caution}
A diferencia de `SIMILAR TO` en este tipo de búsqueda el patrón no tiene que coincidir con toda la cadena para que haya una coincidencia. Basta que alguna subcadena coincida con el patrón para que haya una coincidencia.
:::

```sql
-- Verificar que una cadena cumpla un patrón, sensible a mayúsculas y minúsculas
WHERE col ~ 'pattern'

-- Verificar que una cadena NO cumpla un patrón, sensible a mayúsculas y minúsculas
WHERE col !~ 'pattern'

-- Verificar que una cadena cumpla un patrón, insensible a mayúsculas y minúsculas
WHERE col ~* 'pattern'

-- Verificar que una cadena NO cumpla un patrón, insensible a mayúsculas y minúsculas
WHERE col !~* 'pattern'
```
- _'pattern'_ es una cadena que utiliza secuencias especiales de {ref}`table-caracteres-especiales` junto con determinados caracteres que se quiere que tenga el patrón.

<br/>

---
## SIMILAR TO (regex)

Se utiliza junto con `WHERE` para filtrar columnas de caracteres de acuerdo a una expresión regular. Es un híbrido entre `LIKE` y la notación de expresiones regulares común (_POSIX_). Es posible utilizar {ref}`anexos-wildcards`, a diferencia de _POSIX_ que utiliza la notación común de _regex_.
 
:::{caution}
En PostgreSQL para que se retorne una coincidencia la cadena debe de coincidir por completo con el patrón. No es posible que subcadenas coincidan con el patrón.
:::

```sql
-- Verificar que una cadena cumpla un patrón
WHERE col SIMILAR TO 'pattern'

-- Verificar que una cadena NO cumpla un patrón
WHERE col NOT SIMILAR TO 'pattern'

-- Verificar que una cadena cumpla alguno de varios patrones
WHERE col SIMILAR TO ANY(ARRAY['pattern1', 'pattern2', ...])
```
- _'pattern'_ es una cadena que utiliza _wildcards_ junto con determinados caracteres que se quiere que tenga el patrón.
- También se puede negar el patrón con el operador `NOT`, para solo elegir los registros que no cumplen cierto patrón.
- Se puede filtrar con base a más de un patrón utilizando la función `ARRAY()`.
