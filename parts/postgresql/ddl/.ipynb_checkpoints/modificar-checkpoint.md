# Modificar

En esta sección se presentan algunas acciones comunes relacionadas con modificar objetos en la base de datos.

## ALTER DATABASE

Modifica una base de datos.

```sql
ALTER DATABASE name 
[[WITH] option [...]]
```
- _name_ es nombre de la base de datos.
- _option_: Las posibles opciones son:
    - `ALLOW_CONNECTIONS allowconn`
    - `CONNECTION LIMIT connlimit`
    - `IS_TEMPLATE istemplate`

Existen otras sintaxis para realizar otro tipo de modificaciones, aquí solo se revisará la relacionada con renombrar la base de datos. Para más información visitar la [documentación](https://www.postgresqltutorial.com/postgresql-alter-database/) de PostgreSQL.

### Renombrar

Para renombrar una base de datos usar:

```sql
ALTER DATABASE DB_name
RENAME TO new_name;
```
-	_DB_name_ es el nombre de la base de datos actual.
-	_new_name_ es el nuevo nombre. Debe de empezar con un carácter o `_` (guión bajo) y no debe ser mayor a 30 caracteres.


## ALTER GROUP

```sql
ALTER GROUP name_grupo 
ADD USER name_user1, nom_user2, ...;
```
-	_name\_group_ es el nombre del grupo.
-	_useri_ es el nombre de cada usuario.

## ALTER INDEX

Modifica la definición de un índice.

:::{warning}
En esta sección no se cubren todos los detalles de la sentencia `ALTER INDEX`, únicamente cómo modificar el nombre, para más información consultar la [documentación](https://www.postgresql.org/docs/current/sql-altertable.html) de PostgreSQL.
:::

```sql
-- Modificar nombre de un índice
ALTER INDEX [ IF EXISTS ] index_name RENAME TO new_name
```
- _index_name_ es el nombre actual del índice.
- _new_name_ será el nuevo nombre del índice

## ALTER ROLE

Es un alias de `ALTER USER`. Se utiliza para modificar la configuración de un rol.

### Modificar un rol:
Para modificar un rol existente, como agregar algún privilegio general (no en un objeto en específico) usar:
```sql
ALTER ROLE name
[privilages];
```
-	_name_ es el nombre que tendrá el rol.
-	_privilege_: Los privilegios nuevos. Si son más de uno, separar por comas. 

### Asignar una contraseña a un rol existente:
```sql
ALTER ROLE name
WITH PASSWORD 'password';
```
-	_name_ es el nombre que tiene el rol.
-	_'password'_ es la contraseña.


<br/>

---
## ALTER TABLE

Se usa para agregar, eliminar o modificar columnas en una tabla existente o la configuración de la tabla misma.

:::{warning}
En esta sección no se cubren todos los detalles de la sentencia `ALTER TABLE`, para más información consultar la [documentación](https://www.postgresql.org/docs/current/sql-altertable.html) de PostgreSQL.
:::

### ADD COLUMN

Para agregar una columna a una tabla usar `ADD COLUMN`.

```sql
-- Agregar una columna a una tabla
ALTER TABLE  table_name
ADD COLUMN col type [rest];

-- Agregar múltiples columnas a una tabla
ALTER  table_name 
ADD COLUMN col1 type1 [rest1],
ADD COLUMN col2 type2 [rest2],
...

-- Agregar columna serial
ALTER TABLE table_name
ADD COLUMN id SERIAL [PRIMARY KEY]; 
```
- _table_name_ es el nombre de la tabla.
- _col_ será el nombre de la columna.
- _type_ es el tipo de dato que contendrá la columna.
- _rest_ son las restricciones opcionales que tendrá la nueva columna.
- Se pueden agregar más de una columna al mismo tiempo, repitiendo la sentencia las veces que sea necesario (agregar una coma después de cada sentencia).

<br/>

### ADD CONSTRAINT

`ADD CONSTRAINT` es útil para añadir restricciones a columna existentes en una tabla. Algunas de las restricciones que se pueden añadir incluyen:
- `UNIQUE`.
- `PRIMARY KEY`.
- `FOREIGN KEY`.
- `CHECK`.

:::{caution}
Para la restricción _not-null_ revisar {ref}`ddl-modificar-alter-table-set-not-null`
:::

```sql
-- Añadir restricción UNIQUE
ALTER TABLE table_name
ADD CONSTRAINT rest_name
UNIQUE (col[, ...]);

-- Añadir restricción PRIMARY KEY
ALTER TABLE table_name
ADD CONSTRAINT rest_name
PRIMARY KEY (col[, ...]);

-- Añadir restricción FOREIGN KEY
ALTER TABLE table_name
ADD CONSTRAINT rest_name --Existe la convención de usar other_table_name_id
FOREIGN KEY (col) REFERENCES other_table (colPK);

-- Añadir restricción CHECK
ALTER TABLE table_name
ADD CONSTRAINT rest_name
CHECK (condiditon) 
```
- _table_name_ es el nombre de la tabla.
- _col_ es la columna que se modificará.
- _rest_name_ será el nombre de la restricción.
- _other_table_ es el nombre de la tabla donde está la _primary key_.
- _condition_ normalmente es una comparación que involucra a esa misma columna, utilizando los {ref}`operadores-comparacion`, por ejemplo: `col > 0`.



<br/>

### ADD PRIMARY KEY

`ADD PRIMARY KEY` es útil para modificar una columna como la _primary key_ de la tabla.

```sql
-- Modificar primary key
ALTER TABLE table_name
ADD PRIMARY KEY(col);
```
- _table_name_ es el nombre de la tabla.
- _col_ es la columna que se modificará.
- Pueden ser más de una columna, solo separar los nombres de las columnas con comas.

<br/>

### ALTER COLUMN

#### DROP DEFAULT

`DROP DEFAULT` es útil para eliminar el valor por default de una columna.

```sql
-- Eliminar el valor por default de una columna
ALTER TABLE table_name
ALTER COLUMN  col
DROP DEFAULT 
```
- _table_name_ es el nombre de la tabla.
- _col_ es la columna que se modificará.

<br/>

#### DROP NOT NULL

`SET NOT NULL` se utilizar para eliminar la restricción _not-null_ de una columna.

```sql
-- Eliminar not-null de una columna
ALTER TABLE table_name
ALTER COLUMN  col
DROP NOT NULL;
```
- _table_name_ es el nombre de la tabla.
- _col_ es la columna que se modificará.

<br/>

#### SET DEFAULT

`SET DEFAULT` es útil para modificar el valor por default de una columna.

```sql
-- Modificar el valor por default de una columna
ALTER TABLE table_name
ALTER COLUMN  col
SET DEFAULT default_value
```
- _table_name_ es el nombre de la tabla.
- _col_ es la columna que se modificará.
- _default_value_ es el nuevo valor por default. Debe de coincidir con el tipo de dato de la columna.

<br/>

(ddl-modificar-alter-table-set-not-null)=
#### SET NOT NULL

`SET NOT NULL` se utilizar para establecer una columna con la restricción _not-null_.

```sql
-- Establecer columna como not-null
ALTER TABLE table_name
ALTER COLUMN  col
SET NOT NULL;
```
- _table_name_ es el nombre de la tabla.
- _col_ es la columna que se modificará

<br/>

#### TYPE

`TYPE` se puede utilizar para modificar el tipo de dato de una columna.

```sql
-- Modificar el tipo de dato de una columna
ALTER TABLE table_name
ALTER COLUMN  col
TYPE new_type;

-- Modificar mútiples columnas al mismo tiempo
ALTER TABLE table_name
ALTER COLUMN  col TYPE new_type, 
ALTER COLUMN  col TYPE new_type,
...

-- Modificar tipo y los valores al mismo tiempo
ALTER TABLE table_name
ALTER COLUMN  col
TYPE new_type;
USING func_name()
```
- En lugar de `TYPE` se puede utilizar `SET DATA`.
- _table_name_ es el nombre de la tabla.
- _new_type_ es el nuevo tipo de dato, ver {doc}`../tipos_de_datos.md`.
- Para modificar más de una columna a la vez usar la clausula las veces necesarias, usando una coma después de cada una.
- Se puede modificar el tipo y los valores de una columna usando alguna función en específico (solo en PostgreSQL):
    - _func_name_ normalmente será una función de conversión. También es posible utilizar `col_name::new_type`.
    - Por ejemplo, si antes _col_name_ era `NUMERIC` y ahora se quiere que sea entero, entonces en _new\_type_ se pondría `INTEGER` y en _func_name()_ se podría utiizar `ROUND()`. Otro ejemplo sería un usar `SUBSTRING(col, 1, N)` para un `VARCHAR(N)` de _N_ caracteres.

<br/>

### DROP COLUMN

Para eliminar una columna de una tabla usar `DROP COLUMN`.

```sql
-- Eliminar una columna de una tabla
ALTER TABLE  table_name
DROP COLUMN  col;

-- Eliminar múltiples columnas de una tabla
ALTER TABLE  table_name
DROP COLUMN  col1,
DROP COLUMN  col2,
```
- _table_name_ es el nombre de la tabla.
- _col_ será el nombre de la columna.
- Se pueden eliminar más de una columna al mismo tiempo, repitiendo la sentencia las veces que sea necesario (agregar una coma después de cada sentencia).
- Si la columna a eliminar es usada en objetos como _views_, _triggers_, etc. No se podrá eliminar la columna de la manera anterior. Para efectivamente eliminarla será necesario agregar la palabra `CASCADE`: <br/> `ALTER TABLE table_name DROP COLUMN col CASCADE;`

<br/>

### DROP CONSTRAINT

`DROP CONSTRAINT` es útil para eliminar restricciones de columnas por su nombre.

```sql
-- Eliminar restriccion
ALTER TABLE table_name
DROP CONSTRAINT rest_name;  
```
- _table_name_ es el nombre de la tabla.
- _rest\_name_ es el nombre que se le asignó a esa restricción al momento de crearla. Para conocer los nombres de las restricciones consulta el {doc}`../cookbook.md`.

<br/>

### OWNER TO

`OWNER TO` es útil para traspasar el _ownership_ de una tabla a un usuario.
```sql
-- Transpasar la propiedad de una tabla
ALTER TABLE table_name 
OWNER TO user_name;
```
- _table_name_ es el nombre de la tabla.
- _user_name_ es el nombre del usuario.

<br/>

### RENAME COLUMN

Para renombrar una columna de una tabla usar `RENAME COLUMN`.

```sql
-- Modificare el nombre de una columna
ALTER TABLE  table_name
RENAME COLUMN old_name TO new_name;

-- Modificar el nombre de múltiples columnas
ALTER TABLE  table_name
RENAME COLUMN old_name1 TO new_name1,
RENAME COLUMN old_name2 TO new_name2;
```
- _table_name_ es el nombre de la tabla.
- _old_name_ y _new_name_ son los nombres anterior y nuevo, respectivamente, de la columna.
    - **Nota**: Si se quiere poner un nombre con caracteres especiales o espacios se debe poner entre comillas dobles, aunque no es recomendado utilizar espacios o caracteres especiales: <br/> `RENAME COLUMN "old_name" TO "new view_name"`.
- Si se quiere renombrar más de una columna al mismo tiempo es necesario ejecutar toda la sentencia `ALTER TABLE` la cantidad de veces que sea necesaria o separar con una coma cada clausula `RENAME COLUMN`.

<br/>

### RENAME TO

`RENAME TO` es útil para renombrar tablas, _views_, secuencias e índices.
```sql
-- Renombrar una tabla
ALTER TABLE old_table_name
RENAME TO new_table_name;
```
- _old_table_name_ es el nombre antiguo de la tabla.
- _new_table_name_ es el nombre antiguo de la tabla.

<br/>

---
## ALTER VIEW

:::{warning}
En esta sección no se cubren todos los detalles de la sentencia `ALTER VIEW`, para más información consultar la [documentación](https://www.postgresql.org/docs/current/sql-alterview.html) de PostgreSQL.
:::

De manera similar a `ALTER TABLE`, esta sentencia se usa para agregar, eliminar o modificar columnas en una _view_ existente. A continuación se presentará un resumen de algunas sentencias válidas_

```sql
-- Modificar valor por default de una columna
ALTER VIEW [IF EXISTS] view_name ALTER COLUMN col_name SET DEFAULT expression 

-- Eliminar valor por default de una columna
ALTER VIEW [IF EXISTS] view_name ALTER COLUMN col_name DROP DEFAULT

-- Modificar el ownership de una view
ALTER VIEW [IF EXISTS] view_name OWNER TO new owner_name

-- Renombrar la view    
ALTER VIEW [IF EXISTS] view_name RENAME TO new_name

-- Cambiar el schema de la view    
ALTER VIEW [IF EXISTS] view_name SET SCHEMA new_schema

-- Modificar la configuración de la view    
ALTER VIEW [IF EXISTS] view_name SET (view_option_name [= view_option_value] [,...])

-- Resetear la configuración de la view    
ALTER VIEW [IF EXISTS] view_name RESET (view_option_name [,...])
```
- _view_name_ es el nombre de la _view_.
- _col_name_ es el nombre de alguna columna.
- _owner_name_ es el nombre de algún usuario.
- _view_option_name_ es el nombre de alguna propiedad de la _view_. La propiedad más común es `check_option` y sus posibles valores son:
    - `LOCAL`.
    - `CASCADED`. 