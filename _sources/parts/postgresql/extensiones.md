# Extensiones

En esta sección se revisan cómo trabajar con extensiones y algunas extensiones populares.

## Ver extensiones en PostgreSQL

Para enlistar todas las extensiones que están disponibles para PostgreSQL usar:
```sql
-- Enlistar extensiones disponibles
SELECT name, comment
FROM pg_available_extensions;
```
- _name_ poner tal cual, es el nombre de una columna disponible en _pg\_available\_extensions_.
- _comment_ poner tal cual, es el nombre de una columna que muestra una pequeña descripción de la extensión.
- Hay más columnas, se pueden revisar con `*` en `SELECT`.

<br/>

---
## Ver extensiones instaladas

Para enlistar las extensiones que están instaladas en la sesión actual de PostgreSQL usar:
```sql
-- Enlistar extensiones instaladas
SELECT extname
FROM pg_extension;
```
- _extname_ poner tal cual, es el nombre de una columna disponible en _pg\_extension_.
- Hay más columnas, se pueden revisar con `*` en `SELECT`.

<br/>

---
## Instalar una extensión

Para instalar una extension usar:
```sql
-- Instalar una extensión
CREATE EXTENSION IF NOT EXISTS extname;
```
- _extname_ es el nombre de la extensión.

<br/>

---
## {tablefunc}

Es una extensión que incluye varias funciones que retornan tablas. 

### Agregar extensión

Para poder utilizar las funciones, primero es necesario importar la extensión a PostgreSQL:
```sql
-- Importar extensión a PostgreSQL
CREATE EXTENSION IF NOT EXISTS tablefunc;
```
- _tablefunc_ es el nombre de la extensión.

Esta extensión tiene varias funciones, entre las cuales se encuentran:
- `crosstab`.
- `normal_rand`.
- `crosstabN`.
- `connectBy`.

### CROSSTAB

`CROSSTAB()` - `table`: Sirve para convertir una tabla de _long format_ (_unpivot/stacked_) a _wide format_ (_pivot/unstacked_). Básicamente transforma una tabla, haciendo los valores únicos de una columna en varias columnas. La sintaxis es:
```{code-block} sql
-- Uso de CROSSTAB
 FROM CROSSTAB($$
     query
     ...
     ORDER BY row_name;
     $$) AS nombre
    (row_name DataType1, 
     category1 DataType2,
    ...
     categoryn DataTypen);
```
- Los `$$` son una forma segura de convertir lo que está entre ellos en una cadena y la función utiliza una cadena para funcionar. Por lo tanto básicamente se convierte el query a cadena.
- _query_ es el query que retorna una tabla que debe de tener la siguiente estructura:
    - Una columna _row\_name_ que contendrá los ids de las filas.
    - Una columna _categories_ que contendrá las categorías que se convertirán a columnas.
    - Una columna _values_ que contendrá los valores. Los valores no necesariamente tienen que ser numéricos, pueden ser de texto.
- _row\_name_ y _categories_ son nombres opcionales. de las columnas anteriormente mencionadas.
- **IMPORTANTE**: Las columnas se deben de ordenar, al menos por la columna row_name.
- Si el nombre de una columna es un número, se debe de ponerlo entre doble comillas simples ('').
- **IMPORTANTE**: Si dentro del _query_ se está creando una columna nueva con algún cálculo se debe declarar de qué tipo es el resultado con `::` seguido de `INTEGER`, `TEXT`, `VARCHAR`, etc. antes del `AS `nom (ejemplo con `INTEGER`): <br> ` SELECT FUNCTION( ) :: INTEGER AS nom ...`
- Si se van a utilizar _CTE's_, deben de ir adentro del `CROSSTAB`.

<br/>

---
## {fuzzystrmatch}

Provee de diversas funciones para determinar similitudes y distancias entre cadenas.

### Agregar extensión
Para poder utilzar las funciones, primer es necesario importar la extensión a PostgreSQL:
```sql
-- Importar extensión a PostgreSQL
CREATE EXTENSION IF NOT EXISTS fuzzystrmatch;
```
- _fuzzystrmatch_ es el nombre de la extensión.

Esta extesión tiene varias funciones entre las que se encuentran:
- `Levenshtein`.
- `Soundex`.
- `Metaphone`.
- `Double metaphone`.

<br/>

### LEVENSHTEIN

`LEVENSHTEIN(text_source, text_target)` - `Numeric`: Devuelve el número de caracteres que se tendrían que modificar para que las dos expresiones fueran idénticas.
- _text\_source_, _text\_target_  - `TEXT`: Son dos expresiones o columnas con un máximo de 255 caracteres.

<br/>

---
## {pg_trgm}

Provee de diversas funciones para determinar similitudes entre texto alfanumérico basado en _trigram matching_, así como operadores de índices que soportan búsquedas rápidas para cadenas similares .


### Agregar extensión

Para poder utilizar las funciones, primer es necesario importar la extensión a PostgreSQL:
```sql
-- Importar extensión a PostgreSQL
CREATE EXTENSION IF NOT EXISTS pg_trgm;
```
- _pg\_trgm_ es el nombre de la extensión.

Esta extensión tiene varias funciones entre las cuales se encuentran:
- `similarity`.
- `show_trgm`.
- `word_similarity`.
- `strict_word_similarity`.
- `show_limit`.
- `set_limit`.

<br/>

### SIMILARITY

`SIMILARITY(text, text)` - `Numeric`: Devuelve un número entre 0 y 1, indicando qué tan similares son dos expresiones, siendo 0 que no hay nada de similitud y 1 que las dos expresiones son idénticas.
- _text_ - `TEXT`: Son dos expresiones o columnas.

