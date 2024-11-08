# Cookbook

En esta sección se presentan algunas _recetas_ útiles en SQL.

## Crear bins de datos

Este es un ejemplo de referencia de cómo crear _bins_ para una columna numérica y calcular frecuencias, en este ejemplo los bins se calculan desde el valor mínimo hasta el máximo con _n_ bins en total, 20 en este ejemplo:
```sql
-- Definir el límite inferior de los bins
WITH bins AS (
    -- 20 bins desde el mínimo hasta el máximo
    SELECT generate_series(
               (SELECT MIN(values) FROM my_table),
               (SELECT MAX(values) FROM my_table),
               ((SELECT MAX(values) FROM my_table) - (SELECT MIN(values) FROM my_table)) / 20  -- Tamaño de cada bin
           ) AS lower
),
-- Definir los bins
upper_bounds AS (
    SELECT lower, lower + ((SELECT MAX(values) FROM my_table) - (SELECT MIN(values) FROM my_table)) / 20 AS upper
    FROM bins
)

-- Crear la tabla de frecuencias
SELECT b.lower, b.upper, COUNT(t.values) AS frequency
FROM upper_bounds b
LEFT JOIN my_table t
ON t.values >= b.lower AND t.values < b.upper
GROUP BY b.lower, b.upper
ORDER BY b.lower;
```
- Si se conoce previamente los valores mínimos, máximos y el tamaño de cada bin se puede crear solo un CTE similiar a: <br/> `WITH bins AS (SELECT generate_series(min_bin_1, min_bin_n, step) AS lower, generate_series(max_bin_1, max_bin_n, step) AS upper)`

<br/>

---
## Joins

Aquí se presentaran algunos patrones comúnes al hacer _joins_ entre tablas.

### JOIN de una tabla consigo misma

Para crear un _join_ de una tabla consigo misma simplemente otorgarle un nombre distinto a cada tabla y definir en cuáles columnas hacer el _join_.

En el siguiente ejemplo se realizará:
- Realizar un _Join_ de `populations` consigo mismo basado en `country_code`.
- Selecciona `country_code` de _p1_ y el campo `size`, de _p1_ y _p2_. SQL no acepta campos con el mismo nombre, por lo que será necesario renombrarlos. Se utilizará el alias _size2010_ para _p1.size_ y _size2015_ para _p2.size_.

```sql
-- Selecciona los campos con los nuevos nombres
SELECT p1.country_code,
p1.size AS size2010,
p2.size AS size2015
FROM populations AS p1
-- Unir consigo misma
INNER JOIN populations AS p2
-- Utiliza country_code para el join
USING(country_code);
```

<br/>

### Escribir un LEFT JOIN como un RIGHT JOIN:

Utilzar el siguiente ejemplo para conceptualizar cómo escribir un _join_ utilizando _right join_ en lugar de _left join_, que produzca el mismo resultado.

```sql
-- Convertir este query, usando RIGHT JOIN en lugar de LEFT JOIN.
/*
SELECT cities.name AS city, urbanarea_pop, countries.name AS country,
       indep_year, languages.name AS language, percent
FROM cities
  LEFT JOIN countries
    ON cities.country_code = countries.code
  LEFT JOIN languages
    ON countries.code = languages.code
ORDER BY city, language;
*/

SELECT cities.name AS city, 
    urbanarea_pop, 
    countries.name AS country,
    indep_year, 
    languages.name AS language, 
    percent
FROM languages
  RIGHT JOIN countries
    ON languages.code = countries.code
  RIGHT JOIN cities
    ON countries.code = cities.country_code
ORDER BY city, language;
```

<br/>

### Semi-Join

**Semi-Join**: Selecciona las observaciones en la primer tabla que cumple una condición en la segunda tabla. Usualmente para llevar a cabo esa tarea se usa un _subquerie_ en la clausula `WHERE`.

En el siguiente ejemplo se determina las lenguas únicas que se hablan en medio oriente. Usando las tablas _languages_ y _countries_

```sql
-- Seleccionar los nombres distintos
SELECT DISTINCT name
  -- De languages
  FROM languages
-- Sentencia WHERE.
WHERE code IN
  -- Código de países en Medio oriente.
  (SELECT code
   FROM countries
   WHERE region = 'Middle East')
-- Ordenar por nombre
ORDER BY name;
```

<br/>

### Anti-Join

**Anti-Join**: Selecciona las observaciones en la primer tabla las cuales no cumplen una condición en la segunda tabla. Usualmente para llevar a cabo esa tarea se usa un _subquerie_ en la clausula `WHERE`.

En el siguiente ejemplo el objetivo es identificar las monedas utilizadas en países de Oceanía.

```sql
SELECT code, name
  FROM countries
 WHERE continent = 'Oceania'
  AND code NOT IN
   (SELECT code
    FROM currencies);
```

<br/>

---
## Filtrado

Aquí se presentarán algunos patrones comúnes al filtrar tablas.

<br/>

### Filtrar con un CASE en WHERE

En este ejemplo se genera una lista de partidos ganados por el equipo de Bologna de Italia. En esencia se generá un valor TRUE o FALSE por cada fila y con base a eso se filtra el resultado final.

```sql
-- Seleccionar columnas
SELECT 
 season,
     date,
 home_goal,
 away_goal
FROM matches_italy
WHERE 
-- Excluir los partidos en los que no ganó Bologna
 CASE WHEN hometeam_id = 9857 AND home_goal > away_goal THEN 'Bologna Win'
  WHEN awayteam_id = 9857 AND away_goal > home_goal THEN 'Bologna Win' 
  END IS NOT NULL;
```
- En esencia solo muestra las filas que cumplen alguna de las condiciones en `WHERE`, es decir las que no son `NULL` (porque si una fila no cumple un `WHEN` automáticamente se le asigna `NULL`).

<br/>

---
## Información

Aquí se presentan algunos patrones comúnes de información sobre objetos en la base de datos o de la base de datos misma. Forma parte de SQL estándar aunque el nombre de las columnas puede diferir de un RDBMS a otro.

### Información de las views

Permite obtener información sobre todas las _views_ en la base de datos:

```sql
-- Retornar información sobre las views en la DB
SELECT 
   *
FROM 
   INFORMATION_SCHEMA.VIEWS
```
- En lugar de poner `*`, se puede poner atributos en específico que se quiera retornar. Los atributos son pueden ser (se se puede poner varias separadas por coma):
    - _table_name_: Nombre de la tabla (_view_).
    - _view_definition_: Definición de la _view_, es decir, el query que la produce.
    - _table_schema_: Esquema al que pertenece la tabla.
    - _table_catalog_: Base de datos a la que pertenece la tabla.
    - _is_updatable_: Indica si la _view_ se puede actualizar.
    - _is_insertable_into_: Indica si a la _view_ se le pueden agregar nuevos registros.
    - Entre otros, para revisar todos utiliza `*` en `SELECT`.
- `INFORMATION_SCHEMA.VIEWS` es la tabla que almacena la información.
- Se puede filtrar con base a valores de cualquier atributo con `WHERE`, para ello se tiene que conocer los valores de los atributos y el nombre del atributo. Es recomendado filtrar para la _table_schema_ usando: <br/> `WHERE table_schema NOT IN ('pg_catalog', 'information_schema')`.

<br/>

(postgresql-cookbook-information-columns)=
### Información de las columnas

Permite recuperar información sobre todas las columnas en todas las tablas y _views_ de la base de datos. Forma parte de SQL estándar aunque el nombre de las columnas puede diferir de un RDBMS a otro.

```sql
-- Retornar información sobre las columnas en la DB
SELECT 
*
FROM 
INFORMATION_SCHEMA.COLUMNS

-- Nombre de todas las columnas para cada tabla
SELECT table_name,
    STRING_AGG(column_name, ', ') AS columns
FROM INFORMATION_SCHEMA.COLUMNS
WHERE table_schema = 'public' -- No necesariamente tiene que ser public
GROUP BY table_name
```
- En lugar de poner `*`, se puede poner atributos en específico que se quiera retornar. Los atributos son pueden ser (se puede poner varias separadas por coma):
    - _column_name:_ Nombre de las columna
    - _column_default_: Valor por default de la columna.
    - _table_type_: Tipo de tabla (física (_BASE TABLE_) o _view_).
    - _table_name_: Tabla a la que pertenece la columna.
    - _table_schema_: Esquema al que pertenece la tabla.
    - _table_catalog_: Base de datos a la que pertenece la tabla.
    - _is_nullable_: Indica si la columna puede contener valores `NULL`.
    - _data_type_: Tipo de dato de la columna.
    - _character_maximum_length_: Longitud máxima de la cadena.
    - Otros: _ordinal_position_, _character_maximum_length_, _character_octet_length_, _numeric_precision_radix_, _numeric_scale_, _datetime_precision_, _interval_type_, _interval_precision_, etc.
    - Entre muchos otros, para revisar todos utiliza `*` en `SELECT`.
- `INFORMATION_SCHEMA.COLUMNS` es la tabla que almacena la información.
- Se puede filtrar con base a valores de cualquier atributo con `WHERE`, para ello se tiene que conocer los valores de los atributos y el nombre del atributo. Es recomendado filtrar para la _table\_schema_ o _table\_catalog_ usando `'public'` y el nombre de la base de datos respectivamente, para mostrar solo tablas que están en la base de datos. : <br/> `WHERE table_schema = 'public'` <br/> `WHERE table_catalog = 'DB_name'`.

<br/>

### Información de las tablas

Existen dos formas para ver información de tablas desde un query, la primera es:
```sql
-- Consultar información sobre tablas (es mulitplataforma)
SELECT *
FROM INFORMATION_SCHEMA.TABLES
```
- En lugar de poner `*`, se puede poner atributos en específico que se quiera retornar. Los atributos pueden ser (se puede poner varias separadas por coma):
    - _table_name_: Nombre de la tabla.
    - _table_schema_: Esquema al que pertenece la tabla.
    - _table_catalog_: Base de datos a la que pertenece la tabla.
    - _table_type_: Tipo de tabla (base, _view_, etc).
    - _is_insertable_into_: Indica si se pueden insertar valores a la tabla.
    - Otros: _self_referencing_column_name_, _reference_generation_, _user_defined_type_catalog_, _user_defined_type_schema_, _user_defined_type_name_, _is_typed_, _commit_action_.
- `INFORMATION_SCHEMA.TABLES` es la tabla que almacena la información.
- Se puede filtrar con base a valores de cualquier atributo con `WHERE`, para ello tiene que conocer los valores de los atributos y el nombre del atributo.

Otra forma que es exclusiva de PostgreSQL es con:
```sql
-- Consultar información sobre tablas (solo en PostgreSQL)
SELECT *
FROM pg_catalog.pg_tables
```
- En lugar de poner `*`, se puede poner atributos en específico que se quiera retornar. Los atributos son pueden ser (se se puede poner varias separadas por coma):
    - _schemaname_: Nombre del _schema_ donde están las tablas.
    - _table_name_: Nombre de la tabla.
    - _table_owner_: Dueño de la tabla.
    - _hasindexes_: Indica si la tabla tiene un _Index_.
    - _hasrules_: Indica si la tabla tiene reglas.
    - _hastriggers_: Indica si la tabla tiene _triggers_.
    - Otros: _tablespace_, _rowsecurity_.
    - Entre muchos otros, para revisar todos utiliza `*` en `SELECT`.
- `pg_catalog.pg_tables` es la tabla que almacena la información.
- Se puede filtrar con base a valores de cualquier atributo con `WHERE`, para ello tiene que conocer los valores de los atributos y el nombre del atributo. 

<br/>

(cookbook-informacion-enlistar-restricciones)=
### Información de las restricciones de una tabla

Permite conocer las restricciones e información de las tablas en un DB. Forma parte de SQL estándar aunque el nombre de las columnas puede diferir de un RDBMS a otro.

```sql
-- Retornar información sobre las restricciones de las tablas
SELECT *
FROM INFORMATION_SCHEMA.TABLE_CONSTRAINTS
```
- En lugar de poner `*`, se puede poner atributos en específico que se quiera retornar. Los atributos son pueden ser (se se puede poner varias separadas por coma):
    - _constraint_name_: Nombre de la restricción.
    - _constraint_type_: Tipo de restricción.
    - _table_name_: Nombre de la tabla.
    - _table_schema_: Esquema al que pertenece la tabla.
    - _table_catalog_: Base de datos a la que pertenece la tabla.
    - Entre muchos otros, para revisar todos utiliza `*` en `SELECT`.
- `INFORMATION_SCHEMA.TABLE_CONSTRAINTS` es la tabla que almacena la información.
- Se puede filtrar con base a valores de cualquier atributo con `WHERE`, para ello tiene que conocer los valores de los atributos y el nombre del atributo. Se recomienda filtrar para `table_name` y `table_schema = 'public'`.

<br/>

### Información sobre los tipos de datos

Permite conocer información sobre los tipos de datos en el sistema. Es exclusivo de PostgreSQL.

```sql
-- Retornar información sobre los tipos de datos
SELECT typname, typcategory
FROM pg_type
[WHERE typname = 'nom'] -- Opcional
```
- `pg_type_ es la tabla donde se almacena la información de los tipos de datos.
- Las columnas que se pueden seleccionar son:
    - _typename_: Nombre del tipo de dato.
    - _typecategory`: Categoría del tipo de dato.
    - Entre muchos otros, se puede revisarlos al usar `*` en `SELECT`.
- Se puede especificar un tipo de dato en `WHERE`
- **IMPORTANTE**: Para ver los tipos de datos de las columnas de una tabla en específico ver {ref}`postgresql-cookbook-information-columns`.

<br/>

---
## Bases de datos

Aquí se presentarán algunos patrones comúnes al trabajar con bases de datos.

### Crear una base de datos en PgAdmin:

Para crear una base de datos desde cero usando _PgAdmin_ se debe de seguir los siguientes pasos:

1. Crear la base de datos. Seleccionando el servidor donde se almacenará la base de datos dar click derecho y seguir `Create > DataBase`. Ponerle nombre y ajustar las opciones
2. Crear las tablas: En la base de datos que se acaba de crear, ubicar `Schemas > public > Tables` (en lugar de _public_ se puede elegir o crear otro _schema_) dar click derecho en _Tables_ y seguir `Create > Table`, en la ventana que se abra se crean las tablas indicando, las columnas, sus tipos de datos y sus restricciones (si tienen), así como otras configuraciones que se desea que tengan las tablas.
3. Agregar los datos manualmente o importarlos desde un archivo.

<br/>

### Crear una respaldo de una base de datos en pgAdmin

Para crear un respaldo de una base de datos y desde _pgAdmin_:

1. Seleccionar la base de datos y con el click derecho seleccionar _BackUp_.
2. Elegir una carpeta y un formato. El formato por default es _.sql_.
3. Es posible especificar de cuáles objetos crear el respaldo desde la pestaña _Objects_.
4. Después de configurar el respaldo, presionar _Backup_ en la parte inferior derecha.

<br/>

### Restaurar base de datos desde archivo .TAR en pgAming:

Si se tiene una base de datos en un archivo _.TAR_ entonces (en Windows) y desde _pgAdmin_:

1. Colocar el archivo _.TAR_ en la carpeta _'data'_ de PostgreSQL (Se debe de ubicar esa carpeta en el sistema y dentro de ella crear otra carpeta con cualquier nombre, aí debe de estar ubicado el archivo).
2. Crear la base de datos en _pgAdmin_.
3. Seleccionar la base de datos y con el click derecho seleccionar _Restore_.
4. En _Filename_ elegir el archivo con extensión _.TAR_ en la ruta donde está ubicado.
5. Presionar _Restore_ en la esquina inferior derecha.

<br/>

---
## Importación y Exportación.

### Importación CSV

Para importar unos datos a una tabla existente, desde un archivo _.csv_ se utiliza `COPY`:
```sql
-- Importar datos a una tabla desde un archivo csv
COPY table_name[(col_names)]
FROM 'path/filename.csv'
DELIMITER ','
[CSV HEADER] --Indica si la primer fila son encabezados
```
- _table_name_ es el nombre de la tabla.
- _col_names_ son las columnas de la tabla a las que se añadirá los datos del _csv_. Si se va insertar datos en todas las columnas no es necesario poner las columnas, solo el nombre de la tabla (tampoco los paréntesis).
- En el `FROM` se pone la ruta, el nombre del archivo y la extensión.
- En necesario especificar el delimitador del archivo.
- Opcionalmente, se puede indicar que la primer fila son los encabezados, para que se ignoren a la hora de leer el archivo, para eso se utiliza `CSV HEADER`. Solo aplica en archivos _csv_.
- **IMPORTANTE**: En Windows no se pueden importar archivos si éstos no están en una carpeta en específico.

<br/>

### Importación TXT

Para importar unos datos a una tabla existente, desde un archivo _.txt_ se utiliza `COPY`:
```sql
-- Importar datos a una tabla desde un archivo txt
COPY table_name[(col_names)]
FROM 'path/filename.txt'
DELIMITER ','
```
- _table_name_ es el nombre de la tabla.
- _col_names_ son las columnas de la tabla a las que se añadirá los datos del _txt_. Si se va insertar datos en todas las columnas no es necesario poner las columnas, solo el nombre de la tabla (tampoco los paréntesis).
- En el `FROM` se pone la ruta, el nombre del archivo y la extensión.
- En necesario especificar el delimitador del archivo.
- **IMPORTANTE**: El archivo no debe de tener los encabezados de las columnas, es decir, deben de ser solo el cuerpo de la tabla.
- **IMPORTANTE**: En Windows no se pueden importar archivos si éstos no están en una carpeta en específico.

<br/>

### Exportar CSV

Para exportar unos datos desde tabla existente, a un archivo _.csv_ se utiliza `COPY`:
```{code-block} sql
-- Exportar datos de una tabla a un archivo csv
COPY table_name[(col_names)]
TO 'path/filename.csv'
DELIMITER ','
[CSV HEADER]

-- Alternativa si no se cuenta con permisos para usar COPY
\copy (SELECT * FROM table_name) 
TO 'path/filename.csv' 
WITH csv
```
- _table_name_ es el nombre de la tabla.
- _col_names_ son las columnas de la tabla que se exportarán a _csv_. Si se va exportar todas las columnas no es necesario poner las columnas, solo el nombre de la tabla (tampoco los paréntesis).
- En el `TO` se pone la ruta, el nombre del archivo y la extensión.
- En necesario especificar el delimitador del archivo.
- Opcionalmente, se puede indicar que la primer fila sean los encabezados, para que se incluya a la hora de escribir el archivo, para ellos se usa `CSV HEADER`.

<br/>

---
## Valores perdidos

### Proporción de valores pérdidos en una columna de una tabla

Para calcular la proporción que representan los valores perdidos en una columna usar
```sql
SELECT
    SUM(CASE 
            WHEN col_name IS NULL THEN 1 ELSE 0 
        END)/SUM(1.00)
FROM table_name;
```
- _table_name_ es el nombre de la tabla.