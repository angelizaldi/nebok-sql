# Seleccionar

En esta sección se explica brevemente las principales sentencias relacionadas con selección de datos en una base de datos que son `SELECT` y `FROM` y algunas otras cláusulas útiles al seleccionar columnas de tablas.

## SELECT

Se utiliza para seleccionar información de la base de datos, la información que se retorna se almacena en una tabla temporal llamada _result-set_. 
```sql
-- Seleccionar columnas de una tabla
SELECT col1 [, col2, ...]
FROM table_name;

-- Seleccionar todas las columnas de una tabla
SELECT *
FROM table_name;
    
-- Seleccionar escalares
SELECT 'Hola' || ' ' || 'Mundo'; -- Retorna 'Hola Mundo'
SELECT POWER(2, 5); -- Retorna 32
SELECT DATE '2023-12-25' -- Retorna 2023-12-25
```
- _col1, col2, ..._ son nombres de las columnas.
- Las columnas se separan por comas.
- `FROM` solo se utiliza cuando se van a seleccionar datos de una tabla.
- _table_name_ es el nombre de la tabla.
- No estrictamente tienen que ser columnas, pueden operaciones, cadenas, fechas y tiempos, etc.

<br/>

**Ejemplos**:

```sql
-- Seleccionar columnas específicas de un tabla
SELECT name, membercost
FROM cd.facilities;
```
- [Selección simple](https://pgexercises.com/questions/basic/selectspecific.html): En este ejemplo se seleccionan dos columnas de la tabla _cd.facilities_.

<br/>

---
## FROM

Se utiliza para indicar la tabla que se utilizará para extraer los datos con `SELECT`. 
```sql
-- Indicar la tabla de donde se obtendrán las columnas
SELECT col1 [, ...]
FROM table_name;

-- Indicar el esquema y tabla de donde se obtendrán las columnas
SELECT col1 [, ...]
FROM schema_name.table_name;
```
- _col1, ..._ son los nombres de las columnas de la tabla.
- _table_name_ es el nombre de la tabla de donde se obtendrá la información.
- Es posible indicar también el _schema_ donde está alojada la tabla, si no se indica se toma del _schema_ default que es _public_.


<br/>

---
## DISTINCT

Junto con `SELECT` se utiliza para seleccionar valores únicos de una columna (incluyendo `NULL`).
```sql
-- Seleccionar valores únicos en una columna
SELECT DISTINCT col 
FROM table_name;

-- Seleccionar filas únicas con combinaciones de columnas
SELECT DISTINCT col1, col2, ... 
FROM table_name;
```
- _col1, col2, ..._ son nombres de las columnas.
- _table_name_ es el nombre de la tabla.
- Es posible usar más de una columna, en ese caso se seleccionarán las filas únicas (las combinaciones únicas de los valores de las columnas).


<br/>

---
## AS

Sirve para asignar un alias (nombre temporal), ya sea a una columna, una tabla, etc. Se utilizan para que la lectura sea más sencilla. Ejemplo en `SELECT` y `FROM`:

:::{note}
Los alias suelen ser nombres cortos, pero que logren identificar con relativa facilidad la tabla y/o columna en la que se está aplicando.
:::

```sql
-- Asignar alias a columnas y tablas
SELECT col [AS] colAlias, ...
FROM table_name [AS] tableAlias;

-- Si el alias contiene espacios es necesario ponerlo entre comillas
SELECT col AS "nombre temporal"
```
- _colAlias_ y _tableAlias_ son cualquier nombre que se le quiera dar a las columnas y/o tablas.
- _col_ es el nombre de una columna.
- _table_name_ es el nombre de la tabla.
- Estrictamente no es necesario usar `AS`, se puede omitir. <br/>: `FROM cd.facilities AS f -- equivale a: FROM cd.facilities f`
