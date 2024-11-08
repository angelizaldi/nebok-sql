# Actualizar

En esta sección se revisan las principales sentencias relacionadas con la actualización de objetos existentes en la base de datos.

## REFRESH MATERIALIZED VIEW

:::{warning}
En esta sección no se cubren todos los detalles de la sentencia `REFRESH MATERIALIZED VIEW`, para más información consultar la [documentación](https://www.postgresql.org/docs/current/sql-refreshmaterializedview.html) de PostgreSQL.
:::

Se utiliza para actualizar (refrescar) una _materialized view_.

```sql
-- Actualizar una view materializada
REFRESH MATERIALIZED VIEW [CONCURRENTLY] view_name [WITH [NO] DATA];
```
- _view_name_ es el nombre de la tabla virtual materializada.
- `CONCURRENTLY` se utiliza para indicar que mientras se refreca la _view_ no se ponga en _read only_, permitiendo que se ejecuten otros procedimientos al mismo tiempo.
- `WITH [NO] DATA` se utiliza para indicar que no se generen nuevos datos y solo se actualicen los metadatos.

<br/>

---
(dml-actualizar-update)=
## UPDATE

:::{warning}
En esta sección no se cubren todos los detalles de la sentencia `UPDATE`, para más información consultar la [documentación](https://www.postgresql.org/docs/current/sql-update.html) de PostgreSQL.
:::

Se utiliza para modificar registros actuales de una tabla.

```sql
-- Plantilla básica del uso de UPDATE
UPDATE table_name
SET col1 = new_val1, col2 = new_val2, ...
[WHERE condicion;]
```
- _table_name_ es el nombre de la tabla o _view_.
    - **Importante**: Si la tabla es una _view_ tener en cuenta que en realidad lo que se va a actualizar son las tablas de las que la _view_ depende. 
- En `SET` se establece qué columnas se actualizaran con los nuevos valores.
- No siempre es necesario usar `WHERE`, pero si no se ponen todos los valores en la tabla se van a actualizar (de las columnas especificadas).
    - **IMPORTANTE**: Dependiendo de la clausula `WHERE` se actualizarán uno, varios o todos los registros.
- Cuando los registros se actualizaron de manera exitosa, el _statement_ `UPDATE` retorna: <br/> `UPDATE count`
    - _count_ es la cantidad de registros exitosamente actualizados.
- Se puede utilizar una clausula especial llamada `RETURNING` para que al momento de actualizar los registros, se retorne información sobre esos _records_. <br/> `UPDATE table_name SET col1 = new_val1, col2 = new_val2, ... [WHERE condicion] RETURNING *`
    - Si se quiere que se retorne los registros actualizados como tal, usar un asterisco.
    - Se puede poner el nombre de alguna columna en específico o varias, separadas por coma.

:::{warning}
Si la tabla es una _view_ es posible que no se pueda actualizar. Además la definición de la _view_ debe de respetar los siguientes puntos:
- `SELECT` no puede contener `DISTINCT`.
- `SELECT` no puede contener funciones de agregación.
- `SELECT` no puede contener funciones de _sets_.
- `SELECT` no puede contener operadores de _sets_.
- `FROM` no puede ser múltiples tablas.
- `WHERE` no puede contener _subqueries_.
- El _query_ no puede contener `GROUP BY` ni `HAVING`.
- Las columnas calculadas no deben de ser actualizadas.
- Todas las columnas `NOT NULL` deben de ser incluidas en la _view_ para que `INSERT` funcione.
:::

:::{tip}
Es posible verificar cuáles _views_ son actualizables utilizando el siguiente _query_: <br/>
<code> SELECT table_name, is_updatable
FROM INFORMATION_SCHEMA.VIEWS
WHERE table_schema NOT IN ('pg_catalog', 'information_schema')
</code>
- _is_updatable_ es una columna en la tabla _INFORMATION_SCHEMA.VIEWS_.
:::

<br/>

**Ejemplos**:

```sql
-- UPDATE simple
UPDATE cd.facilities
SET initialoutlay = 10000
WHERE facid = 1;

-- UPDATE múltiple
UPDATE cd.facilities
SET membercost = 6, guestcost = 30
WHERE facid IN (0, 1);
```
- [UPDATE simple](https://pgexercises.com/questions/updates/update.html): En este ejmeplo se actualiza el valor de una única columna en un registro específico.
- [UPDATE múltiple](https://pgexercises.com/questions/updates/updatemultiple.html): En este ejemplo se actualizan dos columnas en dos registros diferentes. Notar que ambos registros tendrán los mismos valores en las columnas que se modificaron.

<br/>

---
### UPDATE FROM

Se  utiliza para actualizar los registros de una tabla e igualarlos a los de una columna de otra tabla.
```sql
-- Plantilla básica de un UPDATE FROM
UPDATE table1 [AS t1]
SET t1.coli = t2.colj
FROM table2 [AS t2]
WHERE conditions;
```
- Se puede poner una o más condiciones en `WHERE`. Notar que en `WHERE` se establece cuáles registros de _table1_ se van a actualizar.
- `FROM` debe de ir inmediatamente después de `SET`.
- En lugar de usar _tabla2_ se puede usar un _subquery_.

<br/>

**Ejemplos**:

```sql
-- UPDATE FROM
UPDATE cd.facilities f
SET    membercost = f2.membercost * 1.1,
       guestcost = f2.guestcost * 1.1
FROM   (SELECT *
        FROM   cd.facilities
        WHERE  facid = 0) f2
WHERE  f.facid = 1;  
```
- [UPDATE FROM](https://pgexercises.com/questions/updates/updatecalculated.html): En este ejemplo se utiliza un _subquery_ para generar un tabla temporal cuyos valores se utilizarán para actualizar valores de la misma tabla pero en otro registro.

<br/>

---
### UPDATE JOIN

Se utiliza para actualizar los registros de una tabla con base a los valores de otra tabla usar:
```sql
-- Plantilla básicame de un UPDATE JOIN
UPDATE table1 [AS t1]
SET t1.col1 = val
FROM table2 [AS t2]
WHERE table1.col2 = table2.col2;
```
- La parte de `WHERE` equivaldría a `ON` en un `JOIN`. Es la condición del `JOIN`.
- `FROM` debe de ir inmediatamente después de `SET`.
- Para cada fila de _table1_, `UPDATE` examina cada fila de _table2_. Si el valor de _col2_ en _table1_ es igual al valor de _col2_ en _table2_, entonces el valor de _col1_ en _table1_se actualiza a _val_.

<br/>

---
## UPSERT

Se utiliza para ingresar nuevos registros a una tabla, pero en caso de que ya existan, actualizar los valores de los mismos.

```sql
INSERT INTO table_name (col_list) 
VALUES(val_list)
ON CONFLICT conflict_target 
action;
```
- _col_list_ son los nombres de las columnas donde se insertarán valores nuevos, separadas por comas.
- _val_list_ son lo valores que se insetarán, separados pos comas.
- _conflict_target_ puede ser:
    - El nombre de una columna.
    - `ON CONSTRAINT constraint_name`:_constraint_name_ es el nombre de una restricción.
    - `WHERE PREDICATE`: Una clausula `WHERE`.
- _action_ puede ser:
    - `DO NOTHING`: Para no hacer nada si el registro ya existe.
    - `DO UPDATE SET col1 = val1, ... WHERE condición`: Actualiza algunos campos en la _table_.