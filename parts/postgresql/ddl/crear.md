# Crear

En esta sección se revisan las principales sentencias relacionadas con crear nuevos objetos en la base de datos.

## CREATE DATABASE

Se utiliza para crear una base de datos vacía.

:::{warning}
En esta sección no se cubren todos los detalles del comando `CREATE DATABASE`, para más información consultar la [documentación](https://www.postgresql.org/docs/current/sql-createdatabase.html) de PostgreSQL.
:::

```sql
-- Crear una base de datos
CREATE DATABASE DB_name
WITH
   [OWNER =  user_name]
   [TEMPLATE = template]
   [ENCODING = encoding]
   [LC_COLLATE = collate]
   [LC_CTYPE = ctype]
   [TABLESPACE = tablespace_name]
   [ALLOW_CONNECTIONS = true | false]
   [CONNECTION LIMIT = max_concurrent_connection]
   [IS_TEMPLATE = true | false ];
```
- _DB\_name_ es el nombre que tendrá el _database_. Debe de empezar con un carácter o `_` (guión bajo) y no debe ser mayor a 30 caracteres.
- `OWNER`: Indica el rol que será dueño de la DB.
- `TEMPLATE`: Específica alguna plantilla a usar para crear la DB.
- `ENCODING`: Determina la codificación de caracteres.
- `IS_TEMPLATE`: Si es `TRUE`, entonces cualquier usuario con el privilegio `CREATEDB` podrá clonar esta _DB_, si el `FALSE`, entonces solo un _superuser_ o el dueño podrán clonarla.

<br/>

---
## CREATE GROUP

Se utiliza para crear un rol de grupo. Se recomienda usar `CREATE ROLE`.

:::{warning}
En esta sección no se cubren todos los detalles del comando `CREATE GROUP`, para más información consultar la [documentación](https://www.postgresql.org/docs/current/sql-creategroup.html) de PostgreSQL.
:::

```sql
-- Crear un grupo
CREATE GROUP name [[WITH] option [...]]
```
- _name_ es el nombre que tendrá el rol.
- _privilege_: El privilegio que tendrá el rol desde un principio. Si son más de uno, se tiene que usar `WITH` y separar los privilegios con espacios en blanco:
    - `SUPERUSER` | `NOSUPERUSER`.
    - `CREATEDB ` | `NOCREATEDB`.
    - `CREATEROLE` | `NOCREATEROLE`.
    - `INHERIT` | `NOINHERIT`.
    - `LOGIN` | `NOLOGIN`.
    - `REPLICATION` | `NOREPLICATION`.
    - `BYPASSRLS` | `NOBYPASSRLS`.
    - `CONNECTION LIMIT` _connlimit_.
    - `[ENCRYPTED] PASSWORD` _'password'_ | `PASSWORD NULL`.
    - `VALID UNTIL` _'timestamp'_.
    - `IN ROLE` _role\_name \[, ...]_.
    - `IN GROUP` _role\_name \[, ...]_.
    - `ROLE` _role\_name \[, ...]_.
    - `ADMIN` _role\_name \[, ...]_.
    - `USER` _role\_name \[, ...]_.
    - `SYSID` _uid_.

<br/>

---
## CREATE INDEX

Se utiliza para crear un nuevo índice

:::{warning}
En esta sección no se cubren todos los detalles del comando `CREATE INDEX`, para más información consultar la [documentación](https://www.postgresql.org/docs/current/sql-createindex.html) de PostgreSQL.
:::

```sql
-- Crear un índice nuevo
CREATE [UNIQUE] INDEX index_name
ON table_name (col_name [ASC|DESC]);
```
- _index_name_ es el nombre que tendrá el índice.
- _table_name_ es el nombre de la tabla donde estará ubicado el índice.
- _col_name_ es la columna que se indexará. Puede ser más de una columna (_composite index_), separadas por coma, además se puede especificar si de manera ascendente o descendente.

<br/>

---
## CREATE ROLE

Se utiliza para crear un rol. Es útil para crear roles de grupos (por default, `NOLOGIN`) y roles de usuario (usar `LOGIN`). Es más vesátil y flexible que `CREATE USER` o `CREATE GROUP`.

:::{warning}
En esta sección no se cubren todos los detalles del comando `CREATE ROLE`, para más información consultar la [documentación](https://www.postgresql.org/docs/current/sql-createrole.html) de PostgreSQL.
:::


```sql
-- Crear rol nuevo
CREATE ROLE name [[WITH] option [...]];
```
- _name_ es el nombre que tendrá el rol.
- _privilege_: El privilegio que tendrá el rol desde un principio. Si son más de uno, se tiene que usar `WITH` y separar los privilegios con espacios en blanco:
    - `SUPERUSER` | `NOSUPERUSER`.
    - `CREATEDB ` | `NOCREATEDB`.
    - `CREATEROLE` | `NOCREATEROLE`.
    - `INHERIT` | `NOINHERIT`.
    - `LOGIN` | `NOLOGIN`.
    - `REPLICATION` | `NOREPLICATION`.
    - `BYPASSRLS` | `NOBYPASSRLS`.
    - `CONNECTION LIMIT` _connlimit_.
    - `[ENCRYPTED] PASSWORD` _'password'_ | `PASSWORD NULL`.
    - `VALID UNTIL` _'timestamp'_.
    - `IN ROLE` _role\_name \[, ...]_.
    - `IN GROUP` _role\_name \[, ...]_.
    - `ROLE` _role\_name \[, ...]_.
    - `ADMIN` _role\_name \[, ...]_.
    - `USER` _role\_name \[, ...]_.
    - `SYSID` _uid_.
- **Importante**: También se puede usar `CREATE USER` o `CREATE GROUP` (no recomendado), la diferencia es que cuando se usa `CREATE USER` se utiliza por default el privilegio `LOGIN`, mientras que cuando se usa `CREATE ROLE` se utiliza por default el privilegio `NOLOGIN` el cual es útil para definir un rol de grupo.

<br/>

### Con una contraseña:
Para crear un rol con una contraseña y una fecha de caducidad usar:
```sql
CREATE ROLE name 
WITH PASSWORD 'password'
VALID UNTIL 'yyyy-mm-dd';
```
- _name_ es el nombre que tendrá el rol.
- _'password'_ es la contraseña 
- _'yyyy-mm-dd'_ es una fecha en ISO 8601.

<br/>

---
## CREATE SCHEMA

Se utiliza para crear un _schema_ nuevo.

:::{caution}
En esta sección no se cubren todos los detalles del comando `CREATE SCHEMA`, para más información consultar la [documentación](https://www.postgresql.org/docs/current/sql-createschema.html) de PostgreSQL.
:::

```sql
-- Crear un esquema nuevo
CREATE SCHEMA IF NOT EXISTS schema_name [ AUTHORIZATION role_specification ]
```
- _schema_name_ será el nombre del _schema_, no puede empezar por '_pg', debe de tener máximo 32 caracteres y debe de empezar por una letra o guión bajo (`_`).
- _role_specification_ indica el usuario dueño del _schema_, las opciones son:
    - `CURRENT_ROLE`
    - `CURRENT_USER`
    - `SESSION_USER`

<br/>

---
## CREATE TABLE

Se utiliza para crear una nueva tabla en una _database_.

:::{warning}
En esta sección no se cubren todos los detalles del comando `CREATE TABLE`, para más información consultar la [documentación](https://www.postgresql.org/docs/current/sql-createtable.html) de PostgreSQL.
:::

```sql
-- Plantilla para crear una tabla
CREATE [TEMP] TABLE [IF NOT EXIST] table_name(
col1 type1 [rest1],
col2 type2 [rest2],
... 
```
- _table_name_ será el nombre de la tabla. Si se pone `IF NOT EXIST` entonces si una tabla con ese nombre ya existe, se arrojará una notificación y se omitirá la creación de la tabla.
- Se puede especificar que sea una tabla temporal con `TEMP` o `TEMPORARY`.
- _col1, col2, ..._ serán los nombres de cada columna en la tabla.
- _type1, type2, ..._ serán los tipos de datos que contendrá cada _coli_.
- _rest1, rest2, ..._ son cualquier tipo de {ref}`teoria-sql-restricciones` opcional que afecta a la columna _coli_.
    - Cada columna puede tener más de una restricción, para ello simplemente poner las restricciones separadas por espacios en blanco.
- Al final se ponen las restricciones que afectan toda la tabla, incluye restricciones de _primary keys_, _foreing keys_ y _check_. Son como las restricciones de columnas, pero afectan a dos o más columnas, aunque pueden estar definidas solo para una. Para ejemplos de cómo definir estas restricciones revisar las proximas secciones.

<br/>

### Con _PRIMARY KEY_

Al crear una tabla, para declarar una columna _primary key_ usar las palabras reservadas `PRIMARY KEY`:

```sql
-- Definir columna como PK
CREATE TABLE table_name(
col type PRIMARY KEY
...);

-- Alternativa
CREATE TABLE table(
col type
...  
PRIMARY KEY(col);
```
- _table_name_ es el nombre de la tabla.
- _col_ son los nombre de las columnas que serán las _primary keys_.
- _type_ son los tipos de datos de las columnas.
- Utilizar `PRIMARY KEY(...)` es conveniente cuando la _PK_ será la combinación de dos o más columnas, simplemente separar por coma cada columna.
- El nombre por default de la restricción será _table_name_pkey_, donde _table_name_ es el nombre de la tabla. Si se desea que la restricción tenga un nombre personalizado revisar {ref}`ddl-crear-create-con-restriccion`.

<br/>

(ddl-crear-create-con-foreing-key)=
### Con _FOREING KEY_

A la hora de crear una tabla se puede declarar una columna como _foreing key_, de la siguiente manera (como restricción de columna):

```SQL
-- Definir columnas como FK
CREATE TABLE table_name(
col type REFERENCES another_table (colPk));

-- Alternativa
CREATE TABLE table_name(
col type 
...
CONSTRAINT rest_name
FOREING KEY (col) REFERENCES another_table (colPk))
[ON DELETE delete_action]
[ON UPDATE update_action];
```
- _table_name_ es el nombre de la tabla hija (donde están las _FK_)
- _col_ es el nombre de la columna en la tabla hija.
- _type_ es el tipo de dato de la columna.
- _another_table_ es el nombre de la tabla padre (donde está la _PK_).
- _colPk_ es la columna _primary key_ de _another_table_.
- _rest_name_ es el nombre que tendrá la restricción.
- Es posible además especificar que acción realizar cuando la _PK_ se modifica o elimina (se viola la _referential integrity_), indicando que acción realizar en la _FK_. Las acciones posibles son:
    - `SET NULL`: Establece `NULL` las observaciones afectadas en la tabla hija.
    - `SET DEFAULT`: Establece el valor por default de las observaciones afectadas en la tabla hija.
    - `RESTRICT`: Similar a `NO ACTION`.
    - `NO ACTION`: Se arroja una violación de restricción.
    - `CASCADE`: Elimina las observaciones afectadas en la tabla hija.

<br/>

### Con _NOT-NULL_

Al momento de crear una tabla se puede indicar cuáles columnas no podrán contener valores `NULL`, esto es útil cuando una columna es indispensable que sea `NOT-NULL`.

```sql
-- Definir una columna como NOT NULL
CREATE TABLE table_name(
col type NOT NULL,
...);
```
- _table_name_ es el nombre de la tabla
- _type_ es el tipo de dato de la columna.
- _col_ es el nombre de la columna.

<br/>

### Con _UNIQUE_

Al momento de crear una tabla, se puede indicar que una columna no debe de tener valores repetidos:
```sql
-- Definir una columna como que no puede tener valores repetidos
CREATE TABLE table_name(
col type UNIQUE,
...);

-- Alternativa
CREATE TABLE table_name(
col type,
...
UNIQUE(col);
```
- _table_name_ es el nombre de la tabla.
- _type_ es el tipo de dato de la columna.
- _col_ es el nombre de la columna.
- El nombre por default de la restricción será _table_name_col_name_key_. Para nombres personalizados revisar {ref}`ddl-crear-create-con-restriccion`.
- Se puede crear una restricción de unicidad de múltiples columnas, simplemente separando con comas el nombre de las columnas.

<br/>

### Con _CHECK_

Al momento de crear una tabla, se puede indicar que los valores de una columna cumplan determinada condición utilizando `CHECK`:
```sql
-- Definir condicionales en una columna
CREATE TABLE table_name(
col type CHECK(condition)
...);
```
- _table_name_ es el nombre de la tabla.
- _col_ es el nombre de la columna.
- _condition_ normalmente es una comparación que involucra a esa misma columna, utilizando los {ref}`operadores-comparacion`, por ejemplo: `col > 0`.
- El nombre por default de la restricción será _table_name_col_key_. Para nombres personalizados revisar {ref}`ddl-crear-create-con-restriccion`.

<br/>

(ddl-create-partition-by-range)=
### Con PARTITION BY RANGE

Se utiliza para crear particiones horizontales en tablas nuevas con base a rangos de una columna. Para particiones con base a columnas categóricas revisar {ref}`ddl-create-partition-by-list`.
```sql
-- Especificar la columna con la que se hará la partición
CREATE TABLE table_name(
 ...
 col_name TYPE CONSTRAINTS
 ...
)
PARTITION BY RANGE(col_name)

-- Posteriormente se tiene que crear las particiones (cada tabla) indicando los rangos con base a los valores de la columna col_name
CREATE TABLE partition_name_1 PARTITION OF table_name
FOR VALUE FROM (value_i) TO (value_j);
CREATE TABLE partition_name_2 OF table_name
FOR VALUE FROM (value_j) TO (value_k);
...
```
- _col_name_ es el nombre de la columna con la que se basará la partición.
- _partition_name_i_ serán los nombres de las particiones.
- _table_name_ es la tabla sobre la cual se está haciendo la partición.
- _value_ son valores de _col_name_, con ellos se definen los rangos para hacer las particiones. Tiene que ser un tipo de dato que permita crear rangos únicos, por ejemplo de tipo fecha, numéricos, etc.

<br/>

(ddl-create-partition-by-list)=
### Con PARTITION BY LIST

Se utiliza para crear particiones horizontales en tablas nuevas con base a valores categóricos de una columna. Para particiones con base a rangos de valores ver {ref}`ddl-create-partition-by-range`.
```sql
-- Especificar la columna con la que se hará la partición
CREATE TABLE table_name(
  ...
  col_name TYPE CONSTRAINTS
  ...
)
PARTITION BY LIST(col_name)
    
-- Posteriormente se tiene que crear las particiones (cada tabla) con base a los valores de la columna col_name
CREATE TABLE partition_name_1 PARTITION OF table_name
FOR VALUES IN (value_i);

CREATE TABLE partition_name_2 PARTITION OF table_name
FOR VALUES IN (value_j);

...
```
- _col_name_ es el nombre de la columna con la que se basará la partición.
- _partition_name_i_ serán los nombres de las particiones.
- _table_name_ es la tabla sobre la cual se está haciendo la partición.
- _value_ son valores de _col_, debe de ser una columna con valores categóricos.

<br/>

### Con valor por default

Al momento de crear una tabla, se puede indicar el valor por default de una columna utilizando `DEFAULT`:
```sql
-- Definit valor por default de una columna
CREATE TABLE table(
col type DEFAULT val;
...);
```
- _table_ es el nombre de la tabla
- _col_ es el nombre de la columna.
- _type_ es el tipo de dato de la columna.
- _val_ es el valor por default que tendrá la columna y que debe de ser del mismo tipo que _type_.

<br/>

### Con Serial

`SERIAL` es un tipo de dato especial que genera una secuencia de número enteros automáticamente conforme se van agregando valores a una tabla, por lo que se puede omitir las columnas de tipo `SERIAL` a la hora de insertar valores. La forma de crear columnas de este tipo de datos es con `SERIAL`:
```sql
CREATE TABLE table(
col SERIAL,
...);
```
- `SERIAL` es el _type_, no es una restricción.
- Por default a _col_ se le agregará un restricción `NOT NULL`.
- Existen alternativas, dependiendo de las necesidades de la serie: `SMALLSERIAL`, `SERIAL` y `BIGSERIAL`. Ver {ref}`tipos-numericos-enteros` y {ref}`theory-sql-tablas-serial`.
- Es común que a los tipos de datos `SERIAL` se les agregué la restricción `PRIMARY KEY`, aunque no es obligatorio.

<br/>

### Con _identity column_

Una columna identidad es una columna numérica que genera números enteros automáticamente cada vez que se insertan valores nuevos a una tabla. Es una alternativa más flexible a `SERIAL`. Para más información ver {ref}`teoria-sql-tablas-identity-column`.
```{code-block} sql
-- Definir una columna como identity column
CREATE TABLE table(
col type GENERATED {ALWAYS | BY DEFAULT} AS IDENTITY[(sequence_option)],
...);
```
- _type_ puede ser `SMALLINT`, `INT` o `BIGINT`.
- Se puede especificar cómo se debe de comportar la columna al ejecutar otras sentencias como `INSERT` o `UPDATE`:
    - `ALWAYS`: Si se intenta insertar (`INSERT`) o actualizar (`UPDATE`) valores en una columna se generará un error (aunque sí es posible hacerlo, revisar {ref}`teoria-sql-tablas-identity-column`). 
    - `BY DEFAULT`: Se puede insertar (`INSERT`) o actualizar (`UPDATE`) otro valor manualmente.
- _sequence_option_ son opciones del objeto `SEQUENCE` para alterar el comportamiento de la secuencia, ver {ref}`teoria-sql-tablas-identity-column`.

<br/>

(ddl-crear-create-con-restriccion)=
### Con restricción

Para definir una restricción en una columna al momento de crear una tabla:
```sql
-- Definir restricción en una columna
CREATE TABLE table(
col type CONSTRAINT name_rest REST(col) 
...);
```
- _REST_ es el nombre de algún tipo de restricción como `UNIQUE`, `CHECK`, `PRIMARY KEY`, etc.
- _col_ es el nombre de la columna que tendrá la restricción. Pueden ser más de una columna separada por comas.
- _name_rest_ es el nombre que tendrá la restricción, debe de empezar con una letra o guion bajo (_) y tener máximo 31 caracteres.
- Si la restricción es `FOREIGN KEY` le debe de seguir `REFERENCES another_table(colPk)` (ver {ref}`ddl-crear-create-con-foreing-key`).

<br/>

### En un _schema_ específico

Para indicar un _schema_ específico al momento de crear la tabla, en el nombre se debe de agregar el nombre del _schema_, seguido de un punto (`.`), seguido del nombre de la tabla:

```sql
-- Crear tabla en un schema específico
CREATE TABLE schema_schema.table_name( 
col type,
...); 
```
- _name_schema_ es el nombre del _schema_ donde estará la tabla.
- _name_tabla_ es el nombre que tendrá la tabla.

<br/>

---
## CREATE TABLE AS

Crea una tabla nueva con base a los resultados de un _query_.

:::{warning}
En esta sección no se cubren todos los detalles del comando `CREATE TABLE AS`, para más información consultar la [documentación](https://www.postgresql.org/docs/current/sql-createtableas.html) de PostgreSQL.
:::

```sql
-- Crear tabla desde un query
CREATE [TEMP] TABLE [IF NOT EXIST] table_name [(col1, col2, ...)]
AS -- query definition
```
- _table_name_ será el nombre de la tabla.
- _query_ es un _query_ que debe de arrojar una tabla.
- Se puede especificar que sea una tabla temporal con `TEMP` o `TEMPORARY`.
- Las columnas de la tabla nueva tendrán el mismo nombre que las del `SELECT`, si se quieren poner otro nombre, poner una lista con los nombres, separadas por coma, entre paréntesis, después del nombre de la nueva tabla (_col1, col2, ..._).

<br/>

---
## CREATE USER

Se utiliza para crear un rol de usuario .

:::{warning}
En esta sección no se cubren todos los detalles del comando `CREATE USER`, para más información consultar la [documentación](https://www.postgresql.org/docs/current/sql-createuser.html) de PostgreSQL.
:::


```sql
-- Crear rol nuevo
CREATE USER name [[WITH] option [...]];
```
- _name_ es el nombre que tendrá el rol.
- _privilege_: El privilegio que tendrá el rol desde un principio. Si son más de uno, se tiene que usar `WITH` y separar los privilegios con espacios en blanco:
    - `SUPERUSER` | `NOSUPERUSER`.
    - `CREATEDB ` | `NOCREATEDB`.
    - `CREATEROLE` | `NOCREATEROLE`.
    - `INHERIT` | `NOINHERIT`.
    - `LOGIN` | `NOLOGIN`.
    - `REPLICATION` | `NOREPLICATION`.
    - `BYPASSRLS` | `NOBYPASSRLS`.
    - `CONNECTION LIMIT` _connlimit_.
    - `[ENCRYPTED] PASSWORD` _'password'_ | `PASSWORD NULL`.
    - `VALID UNTIL` _'timestamp'_.
    - `IN ROLE` _role\_name \[, ...]_.
    - `IN GROUP` _role\_name \[, ...]_.
    - `ROLE` _role\_name \[, ...]_.
    - `ADMIN` _role\_name \[, ...]_.
    - `USER` _role\_name \[, ...]_.
    - `SYSID` _uid_.
- **Importante**: De esta manera se puede crear tanto un rol de usuario como un rol de grupo, es lo mismo, aunque a los roles de usuarios se tiene que definirle atributos mientras que en un rol de grupo no.
- **Importante**: También se puede usar `CREATE USER`, que básicamente es un alias de `CREATE ROLE`, la única diferencia es que cuando se usa `CREATE USER` se utiliza por default el privilegio `LOGIN`, mientras que cuando se usa `CREATE ROLE` se utiliza por default el privilegio `NOLOGIN`.

<br/>

---
## CREATE VIEW AS

Se utiliza para crear una nueva _view_ en una _database_.

:::{warning}
En esta sección no se cubren todos los detalles del comando `CREATE VIEW AS`, para más información consultar la [documentación](https://www.postgresql.org/docs/current/sql-createview.html) de PostgreSQL.
:::

```sql
-- Crear una view
CREATE [OR REPLACE] VIEW view_name AS
-- query definition
```
- _query_ es un _query_ que debe de arrojar una tabla.
- Si se va a reemplazar una _view_ existente entonces el nuevo _query_ debe de producir el mismo número de columnas con los mismos nombres, los mismos tipos de datos y en el mismo orden. Es posible agregar columnas nuevas al final.
- Una vez creada la tabla virtual se podrá hacer referencia a ella como a cualquier otra tabla.
- Para añadir valores se puede usar {ref}`dml-actualizar-update` como con cualquier otra tabla.

<br/>

---
### CREATE MATERIALIZED VIEW AS

Para "materializar" una _view_ y almacenarla localmente se puede utilizar la sentencia `CREATE MATERIALIZED VIEW ... AS ...`
```sql
-- Crear una view materializada
CREATE MATERIALIZED VIEW view_name AS
-- query definition
```
- _query_ es un _query_ que debe de arrojar una tabla.
- Una vez creada la tabla virtual se podrá hacer referencia a ella como a cualquier otra tabla.
- Para añadir valores se puede usar {ref}`dml-actualizar-update` como con cualquier otra tabla.


<br/>

---
## SELECT INTO TABLE

Crea una tabla nueva con base a los resultados de un _query_. Para ello simplemente se utiliza un _query_ normal, pero se pone `INTO` junto con el nombre de la nueva tabla, después de `SELECT` y antes de `FROM`. Las columnas de la nueva tabla tendrán los mismos nombres que los de las columnas de `SELECT`. Es preferible utilizar `CREATE TABLE AS` en lugar de `SELECT INTO`.

:::{warning}
En esta sección no se cubren todos los detalles del comando `SELECT INTO AS`, para más información consultar la [documentación](https://www.postgresql.org/docs/current/sql-selectinto.html) de PostgreSQL.
:::

```sql
-- Crear tabla desde un query
SELECT *
INTO [TEMP] TABLE new_table_name [IN database]
FROM table_name
...
```
- _new_table_name_ será el nombre de la tabla nueva.
- _table_name_ es la tabla de donde se obtendrán los datos.
- Es posible especificar en cuál _database_ insertar la tabla con el operador `IN`.
- Se puede especificar que sea una tabla temporal con `TEMP` o `TEMPORARY`.
