# Insertar

En esta sección se revisan las principales sentencias relacionadas con la insersión de registros nuevos a una base de datos.

## INSERT

Se utiliza para añadir nuevas filas a una tabla existente.

:::{warning}
En esta sección no se cubren todos los detalles de la sentencia `INSERT`, para más información consultar la [documentación](https://www.postgresql.org/docs/current/sql-insert.html) de PostgreSQL.
:::

### INSERT ... VALUES

Se utiliza para insertar valores en un conjunto de columnas como un registro nuevo, indicando indivualmente los valores de cada columna y de cada fila nueva.

:::{warning}
Si la tabla es una _view_ es posible que no se pueda actualizar. Tener en cuenta que en realidad los valores se insertarán en las tablas de las cuales la _view_ depende. Además la definición de la _view_ debe de respetar los siguientes puntos:
- `SELECT` no puede contener `DISTINCT`.
- `SELECT` no puede contener funciones de agregación.
- `SELECT` no puede contener funciones de _sets_.
- `SELECT` no puede contener operadores de _sets_.
- `FROM` no puede ser múltiples tablas.
- `WHERE` no puede contener _subqueries_.
- El _query_ no puede contener `GROUP BY` ni `HAVING`.
- Las columnas calculadas no deben de ser actualizadas.
- Todas las columnas `NOT NULL` deben de ser incluidas en la _view_ para que `INSERT INTO` funcione.
:::

```sql
-- Plantilla básica del uso de INSERT INTO ... VALUES
INSERT INTO table_name ([col1 [type], col2 [type], ...]) 
    VALUES(val1, val2, ...)[, -- Una nueva fila
    (val1, [val2, ...]),      -- Otra fila nueva
    ...
    (val1[, val2, ...])];     -- Una última fila nueva
```
- _table_name_ es el nombre de la tabla, tabla temporal o _view_ donde se ingresarán los nuevos valores.
- _col1, col2, ..._ es el nombre de las columnas donde se ingresarán los valores.
- Si se van a ingresar valores a todas las columnas **no** es necesario poner el nombre de las columnas: <br/> `INSERT INTO table_name VALUES(val1[, val2, ...])`
- **Importante**: Si _col_ es de tipo `SERIAL`, se debe de omitir al insertar nuevos valores o en la parte de `VALUES()` se debe de utilizar la palabra reservada `DEFAULT`.
- _type_ es el tipo de dato de cada columna, es opcional ponerlo.
- _val1, val2, ..._ son los valores que se insertarán. Se debe de respetar el tipo de dato.
    - La cantidad de columnas debe de ser igual a la cantidad de valores.
    - Los valores y columnas se empatan por posición.
- Si se va a insertar más de una fila, los valores de cada fila se ponen entre paréntesis, se separa con coma cada fila y solo es necesario poner una vez `VALUES()`.
- En `VALUES()` si el valor es texto debe de ir entre comillas simples (`'ejemplo'`). Si el texto incluye comillas simples, se tiene que usar dos comillas simples seguida (`''ejemplo''`).
- Para insertar una fecha se tiene que poner entre comillas simples y respetar _ISO 8601_, además los elementos se separan por guión: `'YYYY-MM-DD'`.
- Las columnas de _table_ que no se pongan se les asignará un `NULL`.
- Cuando los registros nuevos se insertan de manera exitosa, la sentencia `INSERT INTO` retorna: <br/> `INSERT oid count`.
    - _oid_ es un identificador del objeto donde se insertaron los registros.
    - _count_ es la cantidad de registros exitosamente insertados.
- Se puede utilizar una clausula especial llamada `RETURNING` para que al momento de ingresar registros nuevos, se retorne información sobre esos _records_. <br/>`INSERT INTO table (col1 [type][, col2 [type], ...]) VALUES(val1[, val2, ...]) RETURNING *`
    - Si se quiere que se retorne los registros insertados como tal, usar un asterisco.
    - Se puede poner el nombre de alguna columna en específico o varias, separadas por coma.

<br/>

**Ejemplos**

```sql
-- Ejemplo básico de INSERT INTO
INSERT INTO cd.facilities
VALUES (9, 'Spa', 20, 30, 100000, 800);

-- Ejemplo de INSER TO múltiple
INSERT INTO cd.facilities
VALUES
    (9, 'Spa', 20, 30, 100000, 800),
	(10, 'Squash Court 2', 3.5, 17.5, 5000, 80);
```
- [INSERT INTO básico](https://pgexercises.com/questions/updates/insert.html): En este ejemplo se insetar un único registro nuevo a la tabla _cd.facilities_.
- [INSERT INTO múltiple](https://pgexercises.com/questions/updates/insert2.html): En este ejemplo se insetan dos registros nuevos a la tabla _cd.facilities_.

<br/>

### INSERT ... _query_

En lugar de `VALUES()` se puede usar un _query_, que retorne un _result set_ que tenga las mismas columnas (la misma cantidad, del mismo tipo y en el mismo orden) que la tabla en las que se van insertar los nuevos registros.

```sql
-- Plantilla básica de uso de INSERT INTO ... query
INSERT INTO table_name
SELECT val1, val2, val3, ...
[FROM other_table
...]
```
- _table_name_ es el nombre de la tabla donde se ingresarán los nuevos valores. Puede ser una tabla temporal.
- En lugar de que sea un _query_ utilizando otra tablas pueden ser valores escalares directamente. 
- Cuando los registros nuevos se insertan de manera exitosa, la sentencia `INSERT INTO` retorna: <br/> `INSERT oid count`
    - _oid_ es un identificador del objeto donde se insertaron los registros.
    - _count_ es la cantidad de registros éxitosamente insertados.

<br/>

**Ejemplos**

```sql
-- Ejemplo básico de INSERT INTO
INSERT INTO cd.facilities
SELECT 9, 'Spa', 20, 30, 100000, 800;

-- Ejemplo múltiple usando UNION
INSERT INTO cd.facilities
SELECT 9, 'Spa', 20, 30, 100000, 800
UNION
SELECT 10, 'Squash Court 2', 3.5, 17.5, 5000, 80;

-- Ejemplo con un subquery
INSERT INTO cd.facilities
SELECT (SELECT MAX(facid) + 1 FROM cd.facilities),
	    'Spa', 20, 30, 100000, 800;
```
- [INSERT INTO básico](https://pgexercises.com/questions/updates/insert.html): En este ejemplo se insetar un único registro nuevo de escalares a la tabla _cd.facilities_ con `SELECT`
- [INSERT INTO múltiple](https://pgexercises.com/questions/updates/insert2.html): En este ejemplo se insertan dos registros nuevos de escalares a la tabla _cd.facilities_ con `SELECT` y `UNION`.
- [INSERT INTO y subquery](https://pgexercises.com/questions/updates/insert3.html): En este ejemplo se utiliza un _subquery_ para añadir un valor calculado y escalares.

<br/>

---
## UPSERT

Se utiliza para ingresar nuevos registros a una tabla, pero en caso de que ya existan, actualizar los valores de los mismos.

```sql
INSERT INTO table_name (col_list) 
VALUES(val_list)
ON CONFLICT target action;
```
- _col_list_ son los nombres de las columnas donde se insertarán valores nuevos, separadas por comas.
- _val_list_ son lo valores que se insetarán, separados pos comas.
- _target_ puede ser:
    - El nombre de una columna.
    - `ON CONSTRAINT CONSTRAINT_NAME`:
        - _constraint_name_ es el nombre de una restricción.
    - `WHERE PREDICATE`:
        - Una clausula `WHERE`.
- _action_ puede ser:
    - `DO NOTHING`: Para no hacer nada si el registro ya existe.
    - `DO UPDATE SET col1 = val1, ... WHERE condición`: Actualiza algunos campos en la _table_.
