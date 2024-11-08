# Eliminar y Truncar

En esta sección se presentan las sentencias relacionadas con eliminar columnas, tablas o la base de datos como tal.

:::{warning}
Las acciones de las sentencias aquí presentadas (excepto por `DELETE`) no se pueden deshacer una vez realizadas.
:::

---
## DELETE TABLE

:::{note}
Esta no es una sentencia _DDL_ pero se presenta aquí como alternativa a truncar una tabla. Tener en cuenta que esta sentencia sí se puede deshacer una vez realizada.
:::

Para eliminar todos los registros de una tabla usar `DELETE TABLE`. Esta sentencia no elimina la tabla como tal, solo su contenido. Para más información de esta sentencia ver {doc}`../dml/eliminar`.

```sql
-- Plantilla básica para eliminar contenido de una tabla
DELATE TABLE table_name;
```
- _table_name_ es el nombre de la tabla que se va a eliminar.

<br/>

---
## DROP DATABASE

Se utiliza para eliminar todo una base de datos.

:::{caution}
Para más información de la sentencia `DROP DATABASE` consultar la [documentación](https://www.postgresql.org/docs/current/sql-dropdatabase.html) de PostgreSQL.
:::

```sql
-- Eliminar una base de datos
DROP DATABASE [IF EXIST] db_name;
```
-	_db\_name_ es el nombre de la base de datos.
-	Se debe de tener cuidado porque una vez eliminado no se puede recuperar la información.
-	Para poder eliminar la base de datos, ya no se debe de tener conexiones activas.

<br/>

---
## DROP INDEX

Se utiliza para eliminar un índice actual en la base de datos. Es necesario ser dueño del index para poder eliminarlo.

:::{caution}
Para más información de la sentencia `DROP INDEX` consultar la [documentación](https://www.postgresql.org/docs/current/sql-dropindex.html) de PostgreSQL.
:::

```sql
-- Eliminar un index
DROP INDEX [IF EXISTS] index_name
[CASCADE | RESTRICT]
```
-	_name_ es el nombre del índice.
- Es posible indicar que hacer en caso de que la tabla sea usada en otros objetos.
    - `CASCADE`: Remover el índice y los objetos dependientes.
    - `RESTRICT`: No elimina el índice si hay objetos dependientes, esta es la opción por default.

<br/>

---
## DROP TABLE

Para eliminar una tabla existente de la base de datos usar `DROP TABLE`.

:::{caution}
Para más información de la sentencia `DROP TABLE` consultar la [documentación](https://www.postgresql.org/docs/current/sql-droptable.html) de PostgreSQL.
:::

```sql
-- Plantilla básica para eliminar una tabla
DROP TABLE [IF EXIST] table_name
[CASCADE | RESTRICT];

-- Eliminar múltiples tablas
DROP TABLE [IF EXIST] table_name1, table_name2, ...
[CASCADE | RESTRICT];
```
- _table_name_ es el nombre de la tabla o tabla temporal que se va a eliminar.
- `IF EXIST` es para evitar que surja un error en caso de que la tabla no exista.
- Es posible indicar que hacer en caso de que la tabla sea usada en otros objetos como _views_ o _triggers_.
    - `CASCADE`: Remover la tabla y los objetos dependientes.
    - `RESTRICT`: No elimina tabla si hay objetos dependientes, esta es la opción por default.

<br/>

---
## DROP VIEW

Se utiliza para eliminar una _view_ existente en la base de datos.

:::{caution}
Para más información de la sentencia `DROP VIEW` consultar la [documentación](https://www.postgresql.org/docs/current/sql-dropview.html) de PostgreSQL.
:::

```sql
-- Plantilla básica para eliminar una view
DROP VIEW [IF EXIST] view_name
[CASCADE | RESTRICT];
```
- _view_name_ es el nombre de la _view_ que se va a eliminar.
- `IF EXIST` es para evitar que surja un error en caso de que la _view_ no exista.
- Es posible indicar que hacer en caso de que la _view_ sea usada en otros objetos como otras _views_ o _triggers_.
    - `CASCADE`: Remover la _view_ y los objetos dependientes.
    - `RESTRICT`: No elimina _view_ si hay objetos dependientes, esta es la opción por default.

<br/>

---
(ddl-eliminar-truncate-table)=
## TRUNCATE

:::{caution}
Para más información de la sentencia `TRUNCATE` consultar la [documentación](https://www.postgresql.org/docs/current/sql-truncate.html) de PostgreSQL.
:::

Para eliminar todos los registros de una tabla, pero conservar la estructura de la tabla usar `TRUNCATE`. A diferencia de `DELETE` esta sentencias siempre eliminará todos los registros, no es posible especificar qué registros eliminar y no se puede deshacer esta acción.

```sql
-- Plantilla básica para truncar una tabla
TRUNCATE TABLE table_name;
[CASCADE | RESTRICT];

-- Truncar múltiples tablas
TRUNCATE TABLE table_name1, table_name2, ...;

-- Indicar acción con la secuencia asociada a una columna
TRUNCATE TABLE table_name
[RESTART IDENTITY | CONTINUE IDENTITY];
```
- _table_name_ es el nombre de la tabla a la cual se va a eliminar las filas.
- Es posible indicar que hacer en caso de que la tabla sea usada en otros objetos como _views_ o _triggers_.
    - `CASCADE`: Remover contenido de la tabla y de los objetos dependientes.
    - `RESTRICT`: No elimina el contenido de la tabla si hay objetos dependientes y se arroja un error, esta es la opción por default.
