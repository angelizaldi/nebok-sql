# Funciones

En esta sección se enlistas las principales funciones que ya forman parte de PostgreSQL.

(funciones-aggregate)=
## Aggregate functions

Son funciones que resumen una cantidad de datos en un solo número, algunas de sus características son:
-	Sirven para hacer cálculos con los datos en el _database_. 
-	Devuelven un solo valor.
-	Para cada columna se debe de volver a usar la función. 
-	Es muy recomendable usar `AS` para darle nombre al resultado.
-	Estas funciones son iguales en todas las plataformas de SQL. 
-	Se pueden usar únicamente en las sentencias `SELECT` y `HAVING`.

```{note}
Los valores `NULL` son ignorados en este tipo de funciones.
```

```{caution}
Esta no es una lista completa de las _aggregate functions_ disponibles. Para una lista completa y más información visitar la [documentación](https://www.postgresql.org/docs/current/functions-aggregate.html) de PostgreSQL.
```

- `AVG(col)` - `NUMERIC` <br/> Calcula el promedio de una columna.
    - _col_ - `NUMERIC`: Es la expresión o columna.
    - Es posible promediar solo valores distintos si se agrega la palabra `DISTINCT`.
<br/>

- `ARRAY_AGG(anyarray [ORDER BY [sort_expression {ASC | DESC}], [...])` - `ARRAY` <br/> Recibe un conjunto de valores y retorna un _array_, en el cual cada elemento en el conjunto es asignado a un elemento del _array_. Es posible ordenar los valores antes de conformar el _array_.
    - _anyarray_: Es una expresión que retorna un valor de cualquier tipo aceptado por un _array_, por ejemplo la concatenación de dos columnas.
    - _sort\_expression_ - `{ASC | DESC}`: Es para indicar como ordenar los valores de _expression_.

<br/>

- `COUNT(col)` - `NUMERIC` <br/> Devuelve el número de filas en una columna/s específica de una tabla.
    - _col_ - `VARIANT`: Es la expresión o columna.
    - Casos especiales:
      - `COUNT(*)`: El número total de filas que tiene una tabla.
      - `COUNT(col)`: El número de valores no `NULL` de una columna.
      - `COUNT(DISTINCT col)`: El número de valores únicos que tienen una columna. Sin incluir `NULL`.

<br/>

- `SUM(col)` - `NUMERIC` <br/> Calcula la suma de una columna.
    - _col_ - `NUMERIC`: Es la expresión o columna.
    - Es posible sumar solo valores distintos si se agrega la palabra `DISTINCT`.

<br/>

- `MAX(col)` - `NUMERIC`, `TEXT`, `TIMESTAMP` <br/> Calcula el valor máximo de una columna. Si son caracteres será por orden alfabético.
    - _col_ - `NUMERIC`, `TEXT`, `TIMESTAMP`: Es la expresión o columna.

<br/>

- `MIN(col)` - `NUMERIC`, `TEXT`, `TIMESTAMP` <br/> Calcula el valor mínimo de una columna. Si son caracteres será por orden alfabético.
    - _col_ - `NUMERIC`, `TEXT`, `TIMESTAMP`: Es la expresión o columna.

<br/>

**Ejemplos**:

```sql
-- Uso de COUNT para contar filas
SELECT COUNT(*)
FROM cd.facilities;

-- Uso de MAX
SELECT MAX(joindate) AS latest
FROM cd.members;
```
- [Uso de COUNT](https://pgexercises.com/questions/aggregates/count.html): En este ejemplo se cuenta el número de filas en la tabla _cd.facilities_.
- [Uso de MAX](https://pgexercises.com/questions/basic/agg.html): En este ejemplo se determina el valor máximo de la columna _joindate_.

<br/><br/>

---
(funciones-cadenas)=
## Cadenas

En esta sección se enlistan funciones útiles para valores y columnas de tipo texto.

```{caution}
Esta no es una lista completa de las funciones de cadenas disponibles. Para una lista completa y más información visitar la [documentación](https://www.postgresql.org/docs/current/functions-string.html) de PostgreSQL.
```

### ASCII

Funciones relacionadas con códigos ASCII.

- `ASCII(text)` - `NUMERIC` <br/> Retorna el código ASCII de un carácter.
    - _text_ - `TEXT`: El carácter del cual se quiere conocer el código ASCII.

<br/>

- `CHR(integer)` - `TEXT` <br/> Retorna el carácter asociado a un código ASCII.
    - _integer_ - `NUMERIC`: El código ASCII.

:::{note}
Para consultar los códigos ASCII visitar [esta página](https://www.ascii-code.com/).
:::

<br/>

### Concatenar

Funciones útiles para concatenar cadenas.

- `CONCAT(str1, str2, ...)` - `TEXT` <br/> Concatena cadenas, símbolos y valores. Ignora valores `NULL`. Para concatenar con un separador en común revisar `CONCAT_WS()`. También se pueden usar los operadores de concatenación `||`.
    - _str1, str2, ..._ - `TEXT`: Cadenas a concatenar, separar cada cadena con una coma. Es posible poner valores que no sean `TEXT` pero se convertirán a `TEXT`. Pueden ser columnas.

<br/>

- `CONCAT_WS(sep, str1, str2, ...)` - `TEXT` <br/> Concatena cadenas, símbolos y valores con un separador entre cada elemento. Ignora valores `NULL`.
    - _sep_ - `TEXT`: Carácter que se utilizará para separar cada elemento.
    - _str1, str2, ..._ - `TEXT`: Cadenas a concatenar, separar cada cadena con una coma. Es posible poner valores que no sean `TEXT` pero se convertirán a `TEXT`. Pueden ser columnas.

<br/>

- `STRING_AGG(str, sep [ORDER BY exp])` - `TEXT` <br/> Concatena una lista de caracteres y las separa con un separados específico.
    - _str_ - `TEXT`: Es cualquier expresión que resulte en una cadena, como por ejemplo la concatenación de dos columnas o una sola columna. Si el resultado no es una cadena, se debe convertir a una cadena explícitamente.
    - _sep_ - `TEXT`: es el separador que se utilizara para concatenar cada elemento, debe ser de tipo `TEXT` o ir entre comillas simples. En ese mismo argumento se puede especificar cómo ordenar las cadenas. También se puede hacer con: <br/> `STRING_AGG(STR, sep) WITHIN GROUP (ORDER BY ...)`

<br/>

### Expresiones regulares

Funciones que permiten usar expresiones regulares.

```{caution}
Esta no es una lista completa de las funciones compatibles con expresiones regulares. Para una lista completa y más información visitar la [documentación](https://www.postgresql.org/docs/current/functions-string.html) de PostgreSQL, particulamente las funciones que inician con _regex_*_.
```

- `REGEXP_COUNT(str, pattern, [start, flags])` - `INTEGER` <br/> Retorna el recuento de coincidencias de un patrón en un cadena.
    - _str_ - `TEXT`: Cadena donde se hará el recuento.
    - _pattern_ - `TEXT`: Expresión regular (POSIX) para determinar lo que se va a buscar.
    - _start_ - `INTEGER`: Indica desde cuál caracter hacer la búsqueda.
    - _flags_ - `TEXT`: _Flags_ a aplicar a la expresión regular.

- `REGEXP_INSTR(str, pattern, [start, N, endpoint, flags, subexpr])` - `INTEGER` <br/> Retorna la posición en la cadena donde ocurre la coincidencia número _N_ del patrón. Retorna 0 si no hay coincidencia.
    - _str_ - `TEXT`: Cadena donde se harán los reemplazos.
    - _pattern_ - `TEXT`: Expresión regular (POSIX) para determinar lo que se va a reemplazar.
    - _start_ - `INTEGER`: Indica desde cuál caracter hacer la búsqueda.
    - _N_ - `INTEGER`: Número de coincidencia.
    - _flags_ - `TEXT`: _Flags_ a aplicar a la expresión regular.

- `REGEXP_LIKE(str, pattern, [flags])` - `BOOLEAN` <br/> Indica si existe una coincidencia de un patrón en una cadena.
    - _str_ - `TEXT`: Cadena donde se hará la búsqueda.
    - _pattern_ - `TEXT`: Expresión regular (POSIX) para determinar lo que se va a buscar.
    - _flags_ - `TEXT`: _Flags_ a aplicar a la expresión regular.
 
- `REGEXP_MATCH(str, pattern, [flags])` - `TEXT[]` <br/> Retorna la primer coincidencia de un patrón en una cadena. Para retornar todos los _matches_ revisar la función `REGEXP_MATCHES` en la [documentación](https://www.postgresql.org/docs/current/functions-string.html).
    - _str_ - `TEXT`: Cadena donde se hará la búsqueda.
    - _pattern_ - `TEXT`: Expresión regular (POSIX) para determinar lo que se va a buscar.
    - _flags_ - `TEXT`: _Flags_ a aplicar a la expresión regular.

- `REGEXP_REPLACE(str, pattern, replacement, [flags])` - `TEXT` <br/> Reemplazar los _match_ de una expresión regular por una cadena. Por default solo se reemplazará la primer coincidencia, para reemplazar todas las coincidencias usar la _flag_ 'g'.
    - _str_ - `TEXT`: Cadena donde se harán los reemplazos.
    - _pattern_ - `TEXT`: Expresión regular (POSIX) para determinar lo que se va a reemplazar.
    - _replacement_ - `TEXT`: Cadena que reemplazará los _matches_ de _pattern_.
    - _flags_ - `TEXT`: _Flags_ a aplicar a la expresión regular.

**Ejemplos**:

```sql
-- Uso de REGEXP_REPLACE()
SELECT memid, REGEXP_REPLACE(telephone, '[-() ]', '', 'g')
FROM cd.members
ORDER BY memid;
```
- [Uso de REGEXP_REPLACE](https://pgexercises.com/questions/string/translate.html): En este ejemplo los caracteres '-', '(', ')' y ' ', se reemplazan por ''. Alternativamente se pudo haber usado la expresión regular _'\[^0-9]'_.

<br/>

### Formato

Funciones que modifican el formato de una cadena.

- `FORMAT(str, format)` - `TEXT` <br/> Modifica el formato de una cadena de acuerdo a una cadena de formato.
    - _str_ - `TEXT`: Cadena que se modificará. Pueden ser columnas.
    - _format_ - `TEXT`: Cadena de formato que indica el formato a aplicar. Ver la [documentación](https://www.postgresql.org/docs/current/functions-string.html#FUNCTIONS-STRING-FORMAT).

<br/>

- `INITCAP(str)` - `TEXT` <br/> Convierte en mayúscula la primer letra de cada palabra de una cadena.
    - _str_ - `TEXT`: Cadena que se modificará. Pueden ser columnas.

<br/>

- `LOWER(str)` - `TEXT` <br/> Convierte la cadena a minúsculas.
    - _str_ - `TEXT`: Cadena que se convertirá en minúsculas. Pueden ser columnas.

<br/>

- `UPPER(str)` - `TEXT` <br/> Convierte la cadena a mayúsculas.
    - _str_ - `TEXT`: Cadena que se convertirá en mayúsculas. Pueden ser columnas.

<br/>

### Información

Funciones que retornan información sobre una cadena.

- `CHAR_LENGTH(str)` - `INTEGER` <br/> Devuelve la longitud de una cadena. Es igual que `LENGTH()`.
    - _str_ - `TEXT`: Cadena que se quiere saber su longitud. Pueden ser columnas.

<br/>


- `LENGTH(str)` - `INTEGER` <br/> Devuelve la longitud de una cadena. Es igual que `CHAR_LENGTH()`.
    - _str_ - `TEXT`: Cadena que se quiere saber su longitud. Pueden ser columnas.

<br/>


- `POSITION(substr IN text)` - `INTEGER` <br/> Devuelve la posición, de izquierda a derecha, en la que está determinada subcadena en una cadena. Similar a `STRPOS()`.
    - _substr_ - `TEXT`: Subcadena a buscar en la cadena.
    - _text_ - `TEXT`: La cadena que contiene el carácter. También puede ser una columna.

<br/>


- `STRPOS(text, substr)` - `INTEGER` <br/> Devuelve la posición, de izquierda a derecha, en la que está determinada determinada en una cadena. Es similar a `POSITION()`.
    - _text_ - `TEXT`: La cadena que contiene el carácter. También puede ser una columna.
    - _substr_ - `TEXT`: Subcadena a buscar en la cadena.
    

<br/>

### Subcadenas y splits

Funciones que recuperan partes de una cadena o dividen la cadena en partes más que pequeñas.

- `LEFT(text, N)` - `TEXT` <br/> Devuelve los _N_ primeros caracteres de una cadena.
    - _text_ - `TEXT`: Cadena que se modificará. Pueden ser columnas.
    - _N_ - `INTEGER`: Número de caracteres a retornar.

<br/>

- `RIGHT(text, N)` - `TEXT` <br/> Devuelve los _N_ últimos caracteres de una cadena.
    - _text_ - `TEXT`: Cadena que se modificará. Pueden ser columnas.
    - _N_ - `INTEGER`: Número de caracteres a retornar.

<br/>

- `SPLIT_PART(text, delimiter, position)` - `TEXT` <br/> Divide un cadena de acuerdo a un delimitador y retorna el elemento _N_ (del split).
    - _text_ - `TEXT`: Cadena que se dividirá. Pueden ser columnas.
    - _delimiter_ - `TEXT`: pattern a utilizar para separar la cadena.
    - _position_ - `INTEGER`: Posición a retornar. Comienza en uno. Si es mayor al número de partes, entonces retorna una cadena vacía.

<br/>

- `SUBSTRING(text, start_position, length)` - `TEXT` <br/> Extrae una cadena de otra. Otra sintaxis alternativa sería `SUBSTRING(string FROM start_position FOR length)`. Es similar a `SUBSRT()`.
    - _text_ - `TEXT`: Cadena que se dividirá. Pueden ser columnas.
    - _start_position_ - `INTEGER`: Posición de carácter en la cual empezar la sub cadena. Empieza en cero. No acepta número negativos.
    - _length_ - `INTEGER`: Indica el número de caracteres de la sub cadena. Si `start_position + length > text` (en longitudes) entonces se retorna toda la cadena.

<br/>

- `SUBSTR(text, start_position, length)` - `TEXT` <br/> Extrae una cadena de otra. Similar a `SUBSTRING()`.
    - _text_ - `TEXT`: Cadena que se dividirá. Pueden ser columnas.
    - _start_position_ - `INTEGER`: Posición de carácter en la cual empezar la sub cadena. Empieza en cero. No acepta número negativos.
    - _length_ - `INTEGER`: Indica el número de caracteres de la sub cadena. Si `start_position + length > text` (en longitudes) entonces se retorna toda la cadena.

<br/>

**Ejemplos**:

```sql
-- Uso de LEFT()
SELECT LEFT(surname, 1) AS letter, COUNT(*)
FROM cd.members
GROUP BY letter
ORDER BY letter;
```
- [Uso de LEFT](https://pgexercises.com/questions/string/substr.html): En este ejemplo se cálcula el número de registros en los que la columna _surname_ comienza con cada letra del abecedario. Alternativamente se pudo haber usado `SUBSTR()`.

<br/>

### Repetir, Reemplazar y Revertir

Funciones que permiten multiplicar la cadenan, reemplazar partes de la misma por otra cadena o modificar el orden de la cadena de manera inversa.

- `REPEAT(text, N)` - `TEXT` <br/> Repite una cadena/carácter un número de veces en específico.
    - _text_ - `TEXT`: Cadena que se repetirá. Pueden ser columnas.
    - _N_ - `INTEGER`: Número de ves que se repetirá _text_.

<br/>

- `REPLACE(text, from, to)` - `TEXT` <br/> Remplaza de la cadena, _from_ por el _to_. Remplaza todos los patrones. Por ejemplo si se utiliza 'from=a' y  'to=b', todas las "_a_" las convertirá en "_b_".
    - _text_ - `TEXT`: Cadena que se modificará. Pueden ser columnas.
    - _from_, _to_ - `TEXT`: Los patrones a sustituir y por el cual se sustituirá respectivamente.

<br/>

- `REVERSE(text)` - `TEXT` <br/> Convierte una cadena en su equivalente al revés.
    - _text_ - `TEXT`: Cadena que se modificará. Pueden ser columnas.

<br/>

- `TRANSLATE(text, from, to)` - `TEXT` <br/> Remplaza un conjunto de caracteres por otro conjunto de caracteres en una cadena. Los conjuntos de caracteres se empatan por posición, en caso de que el conjunto de caracteres de _from_ sea mayor a _to_, entonces los caracteres extras se eliminarán de la cadena original 
    - _text_ - `TEXT`: Cadena que se modificará. Pueden ser columnas.
    - _from_, _to_ - `TEXT`: Los conjuntos de caracteres a sustituir y por el cual se sustituirá respectivamente.

<br/>

**Ejemplos**

```sql
-- Uso de TRANSLATE
SELECT memid, TRANSLATE(telephone, '-() ', '') AS telephone
FROM cd.members
ORDER BY memid;  
```
- [Uso de TRANSLATE](https://pgexercises.com/questions/string/translate.html): En este ejemplo los caracteres '-', '(', ')' y ' ', se reemplazan por ''. En realidad solo '-' es efectivamente reemplazado por '', pero al ser mayor el número de caracteres a reemplazar que el número de caracteres pos los cuales se reemplazaran el resto de los caracteres simplemente se eliminan de la cadena original.

<br/>

### TRIM y PAD

- `TRIM([LEADING|TRAILING|BOTH] [characters] FROM TEXT)` - `TEXT` <br/> Elimina todos los caracteres que están al principio, al final o en ambos de una cadena. Por default es _both_ y espacio en blanco como carácter.
    - `LEADING`, `TRAILING`, `BOTH`: Son para especificar en qué parte de la cadena eliminar los caracteres, si al principio, al final o en ambos respectivamente. Solo se pone uno.
    - _text_ - `TEXT`: Cadena que se modificará. También pueden ser columnas.
    - _characters_ - `TEXT`: El carácter que se quiere eliminar de la cadena. Pueden ser más de uno. No es un pattern se eliminarán todos los caracteres aquí puestos. Es sensible a mayúsculas y minúsculas.

<br/>

- `BTRIM(text, characters)` - `TEXT` <br/> Elimina todos los caracteres que están al principio y al final de una cadena.
    - _text_ - `TEXT`: Cadena que se modificará. También pueden ser columnas.
    - _characters_ - `TEXT`: El carácter o los caracteres que se quiere eliminar de la cadena. Pueden ser más de uno. No es un pattern se eliminarán todos los caracteres aquí puestos. Es sensible a mayúsculas y minúsculas.

<br/>

- `LTRIM(text, characters)` - `TEXT` <br/> Elimina todos los caracteres que están al principio de una cadena.
    - _text_ - `TEXT`: Cadena que se modificará. También pueden ser columnas.
    - _characters_ - `TEXT`: El carácter o los caracteres que se quiere eliminar de la cadena. Pueden ser más de uno. No es un pattern se eliminarán todos los caracteres aquí puestos. Es sensible a mayúsculas y minúsculas.

<br/>

- `RTRIM(text, characters)` - `TEXT` <br/> Elimina todos los caracteres que están al final de una cadena.
    - _text_ - `TEXT`: Cadena que se modificará. También pueden ser columnas.
    - _characters_ - `TEXT`: El carácter o los caracteres que se quiere eliminar de la cadena. Pueden ser más de uno. No es un pattern se eliminarán todos los caracteres aquí puestos. Es sensible a mayúsculas y minúsculas.

<br/>

- `LPAD(text, N, fill)` - `TEXT` <br/> Agrega los símbolos necesarios al principio de la cadena para que ésta tenga una longitud de _N_. Por default _fill_ son espacios en blanco.
    - _text_ - `TEXT`: Cadena que se modificará. También pueden ser columnas.
    - _N_ - `INTEGER`: El tamaño que se desea que tenga la cadena.
    - _fill_ - `TEXT`: Caracteres que se agregarán a la izquierda de la cadena para cumplir con la longitud deseada.

<br/>

- `RPAD(text, N, fill)` - `TEXT` <br/> Agrega los símbolos necesarios al final de la cadena para que ésta tenga una longitud de N. Por default _fill_ son espacios en blanco. Si _N_ es menor al tamaño de la cadena entonces se truncará la cadena.
    - _text_ - `TEXT`: Cadena que se modificará. También pueden ser columnas.
    - _N_ - `INTEGER`: El tamaño que se desea que tenga la cadena.
    - _fill_ - `TEXT`: Caracteres que se agregarán a la derecha de la cadena para cumplir con la longitud deseada.

<br/>

**Ejemplos**:

```sql
-- Uso de LPAD()
SELECT LPAD(zipcode :: TEXT, 5, '0')
FROM cd.members;
```
- [Uso de LPAD](https://pgexercises.com/questions/string/pad.html): En este ejemplo se añaden '0's al inicio de una cadena (convertido a `TEXT`) para que tengan una longitud determinanda.

<br/><br/>

---
(funciones-conversion)=
## Conversión

- `CAST(expression AS type)` - `VALUE` <br/> Convierte un valor a otro tipo de dato.
    - _expression_ - `VALUE`: Valor o columna.
    - _type_ - `Data type`: Tipo de dato que se desea.

<br/>

- `TO_CHAR(expression, format)` - `TEXT` <br/> Convierte un `TIMESTAMP`, `INTERVAL`, `INTEGER` o valores numéricos o de doble precisión a `TEXT` en formatos concretos.
    - _expression_ - `NUMERIC`, `DATETIME`: Cualquier valor de tipo `TIMESTAMP`, `INTERVAL`, `INTEGER` o valores numéricos o de doble precisión.
    - _format_ - `TEXT`: Indica el formato de la fecha/numérico de _text_. Revisar {ref}`anexos-literales-fecha` y {ref}`anexos-literales-numero`.
    - **IMPORTANTE**: Se debe de indicar carácter por carácter el formato en el que está expresión y lo que representa cada carácter.

- `TO_NUMBER(expression, format)` - `NUMERIC` <br/> Convierte una cadena a un número.
    - `expression` - `TEXT`: Cualquier valor de cadena.
    - _format_ - `TEXT`: Indica el formato del número en _expression_. Ejemplo: <br/> `to_number('12,454.8-', '99G999D9S')`.

<br/>

- `TO_DATE(text, format)` - `DATE` <br/> Convierte una cadena en una fecha, indicando el formato en el que está la cadena.
    - _text_ - `TEXT`: Es la cadena que contiene la fecha.
    - _format_ - `TEXT`: Indica el formato de la fecha de _text_. Revisar {ref}`anexos-literales-fecha`.

<br/>

- `TO_TIMESTAMP(text, format)` - `TIMESTAMP` <br/> Convierte una cadena en una fecha y hora, indicando el formato en el que está la cadena.
    - _text_ - `TEXT`: Es la cadena que contiene la fecha y tiempo.
    - _format_ - `TEXT`: Indica el formato de la fecha de _text_. Revisar {ref}`anexos-literales-fecha`.

<br/>

**Ejemplos**:

```sql
-- Ejemplos de TO_CHAR con "DATETIME"
to_char(interval '15h 2m 12s', 'HH24:MI:SS') -- > '15:02:12'

-- Ejemplos de TO_CHAR con "NUMERIC"    
to_char(0485, '9999') -- > '485'
to_char(0485, '0000') -- > '0485'
to_char(125.8, '999D99') -- > '125.80'
to_char(125.8, '999D00') -- > '125.8'
```

<br/><br/>

---
(funciones-estadisticas)=
## Estadísticas

### Varianzas y desviaciones

- `VAR_POP(exp)` - `NUMERIC` <br/> Calcula la varianza poblacional.
    - _exp_ - `NUMERIC`: Es la expresión o columna.

<br/>

- `VAR_SAMP(exp)` - `NUMERIC` <br/>  Calcula la varianza muestral.
    - _exp_ - `NUMERIC`: Es la expresión o columna.

<br/>

- `VARIANCE(exp)` - `NUMERIC` <br/> Calcula la varianza muestral.
    - _exp_ - `NUMERIC`: Es la expresión o columna.

<br/>

- `STDDEV_POP(exp)` - `NUMERIC` <br/> Calcula la desviación estándar poblacional.
    - _exp_ - `NUMERIC`: Es la expresión o columna.

<br/>

- `STDDEV_SAMP(exp)` - `NUMERIC` <br/> Calcula la desviación estándar poblacional.
    - _exp_ - `NUMERIC`: Es la expresión o columna.

<br/>

- `STDDEV(exp)` - `NUMERIC` <br/> Calcula la desviación estándar muestral.
    - _exp_ - `NUMERIC`: Es la expresión o columna.

<br/>

### Correlaciones

- `CORR(X, Y)` - `NUMERIC` <br/> Calcula la correlación entre dos columnas numéricas.
    - _X_ - `NUMERIC`: Son los valores dependientes.
    - _Y_ - `NUMERIC`: Son los valores independientes.
    - Se ignoran los `NULL` cuando ambos valores por posición son `NULL`. Si hay un `NULL` que esté en par entonces se retorna `NULL`.

<br/>

### Percentiles

```{attention}
Con cualquiera las dos funciones se deben usar de la siguiente manera: <br/> `PERCENTILE_CONT(percentil) WITHIN GROUP(ORDER BY col_name ASC)`
```

- `PERCENTILE_DISC(percentil)` - `NUMERIC` <br/> Calcula el percentil de unos datos numéricos ordenados. Siempre devolverá un valor que esté en la columna.
    - _percentil_ - `NUMERIC`: Es un valor entre 0 y 1.

<br/>

- `PERCENTILE_CONT(percentil)` - `NUMERIC` <br/> Calcula el percentil de unos datos numéricos ordenados. Es posible que devuelva un valor que no esté en la columna, ya que interpola valores, cuando el percentil no tiene un valor exacto.
    - _percentil_ - `NUMERIC`: Es un valor entre 0 y 1.

<br/>


<br/><br/>

---
(funciones-fechas)=
## Fechas y Tiempos

### Fecha y tiempos actuales

- `NOW()` - `TIMESTAMP` <br/> Devuelve la fecha y tiempo actual con zona horaria (_timestamp_ con zona horaria). Equivale a `CURRENT_TIMESTAMP()`.
    - No tiene argumentos.
    - Si se modifica manualmente la zona horaria, entonces `NOW()` se ajusta a esa zona horaria.

<br/>

- `CURRENT_DATE` - `DATE` <br/> Devuelve la fecha actual sin tiempo _'YYYY-MM-DD'_.
    - No tiene argumentos, ni siquiera lleva paréntesis.

<br/>

- `CURRENT_TIME(precision)` - `TIME` <br/> Devuelve la hora actual con zona horaria _'HH:MM:SS.GMT+TZ'_.
    - _precisión_ - `NUMERIC`: Especifica la precisión de los segundos fraccionales (número de decimales en los segundos).

<br/>

- `CURRENT_TIMESTAMP(precision)` - `TIMESTAMP` <br/> Devuelve la fecha y hora actual con zona horaria _'YYYY-MM-DD HH:MM:SS.GMT+TZ'_.
    - _precision_ - `NUMERIC`: Especifica la precisión de los segundos fraccionales (número de decimales en los segundos).

<br/>

- `LOCALTIME(precision)` - `TIMESTAMP` <br/> Devuelve la fecha y hora actual sin zona horaria.
    - _precision_ - `NUMERIC`: Especifica la precisión de los segundos fraccionales (número de decimales en los segundos).

<br/>

- `LOCALTIMESTAMP(precision)` - `TIMESTAMP` <br/> Devuelve la fecha y hora actual sin zona horaria.
    - _precision_ - `NUMERIC`: Especifica la precisión de los segundos fraccionales (número de decimales en los segundos).

<br/>

### Operaciones

- `AGE(timestamp1, timestamp2 = CURRENT_TIMESTAMP)` - `NUMERIC` <br/> Retorna la diferencia entre dos `TIMESTAMP`. Retorna la diferencia en años, meses y días, horas, etc.
    - _timestamp1, timestamp2_ - `TIMESTAMP`: Son las fechas, donde `timestamp2 > timestamp1`. Si no se especifica el segundo, se utiliza la fecha y tiempo actual.

### Partes de fechas y truncar

- `EXTRACT(field FROM source)` - `NUMERIC` <br/> Retorna una parte específica de la fecha en formato de número.
    - _field_ - `DATE FIELD`: La parte de la fecha, puede ser _DAY_, _QUARTER_, _WEEK_, _MONTH_, _YEAR_, _EPOCH_, etc. Revisar el anexo {ref}`anexos-field`. No es una cadena.
    - _source_ - `TIMESTAMP`: Es la fecha, ya sea una columna o un valor, cualquier dato de tipo fecha se convertirá a `TIMESTAMP`.

<br/>

- `DATE_PART(field, source)` - `NUMERIC` <br/> Retorna una parte específica de la fecha en formato de número.
    - _field_ - `TEXT`: la parte de la fecha, puede ser _'day'_, _'quarter'_, _'week'_, _'month'_, _'year'_, _'EPOCH'_, etc. Revisar el anexo {ref}`anexos-field`.
    - _source_ - `TIMESTAMP`: Es la fecha, ya sea una columna o un valor, cualquier dato de tipo fecha se convertirá a `TIMESTAMP`.

<br/>

- `DATE_TRUNC(datepart, source)` - `TIMESTAMP` <br/> Truncará la fecha de acuerdo al _field_ que se haya especificado. El resto de los campos los pondrá en su unidad mínima.
    - _datepart_ - `TEXT`: Es el nivel de precisión a truncar la fecha, puede ser: _millennium, century, decade, year, quarter, month, week, day, hour, minute, second, milliseconds, microseconds_.
    - _source_ - `TIMESTAMP`, `INTERVAL`: Es la fecha, ya sea una columna o un valor, cualquier dato de tipo fecha se convertirá a `TIMESTAMP`.
 
**Ejemplos**:

```sql
-- Ejemplo de uso de DATE_PART
SELECT DATE_PART('day', TIMESTAMP '2012-08-31');

-- Ejemplo de uso de EXTRACT
SELECT EXTRACT(DAY FROM TIMESTAMP '2012-08-31');

-- Ejemplo de uso de DATE_TRUNC
SELECT DATE_TRUNC('month' ,my_date + INTERVAL '1 month') - DATE_TRUNC('day' ,my_date) AS remaining
FROM (SELECT TIMESTAMP '2012-02-11 01:00:00' AS my_date) AS sub;
```
- [Uso de DATE_PART y EXTRACT](https://pgexercises.com/questions/date/extract.html): En estos ejemplos se extra el día de las fechas.
- [Uso de DATE_TRUNC](https://pgexercises.com/questions/date/daysremaining.html): En este ejemplo se utiliza la función para determinar el número de días restantes en el mes de una fecha dada. Para ello se utiliza la función para determinar el primer día del siguiente mes y para truncar al día de la fecha, excluyendo el tiempo.

<br/><br/>

---
(funciones-numericas)=
## Numéricas

### Miscelaneas

- `ABS(exp)` - `NUMERIC` <br/> Calcula es valor absoluto de una columna.
    - _exp_ - `NUMERIC`: Es la expresión o columna.

<br/>

- `GREATEST(col1, ...)` - `NUMERIC` <br/> Calcula el valor más grande de un conjunto de columnas para cada fila. Es decir aplica el cálculo sobre las filas.
    - _col1, ..._ - `NUMERIC`: Son los nombres de las columnas.

<br/>

- `LEAST(col1, ...)` - `NUMERIC` <br/> Calcula el valor más pequeño de un conjunto de columnas para cada fila. Es decir aplica el cálculo sobre las filas.
    - _col1, ..._ - `NUMERIC`: Son los nombres de las columnas.

<br/>

- `POWER(m, n)` - `NUMERIC` <br/> Eleva un número _m_ a una potencia _n_.
    - _m_ - `NUMERIC`: Es la base.
    - _n_ - `NUMERIC`: Es el exponente.

<br/>

### Números aleatorios

- `RANDOM()` - `NUMERIC` <br/> Retorna un número pseudo aleatorio entre cero (inclusivo) y uno (exclusivo).
    - No tiene argumentos.
    - **Tip**: Para generar un número decimal entre un rango _[A, B)_ usar: <br/> `RANDOM()*(B - A) + A`.
    - **Tip**: Para generar un número entero entre un rango _[A, B)_ usar: <br/> `FLOOR(RANDOM()*(B - A + 1)) + A`.

<br/>

- `SETSEED(seed)` - `NULL` <br/> Establece la semilla para el generador de número aleatorios. Permite reproducibilidad.
    - _seed_ - `NUMERIC`: Es un valor entre -1 a 1.

<br/>

### Redondeo y Truncar

- `ROUND(source, n)` - `NUMERIC` <br/> Redondea expresiones numéricas.
    - _source_ - `NUMERIC`: Es el número o la columna a redondear.
    - _n_ - `NUMERIC`: Es para indicar cuántos decimales usar. Si se pone números negativos entonces se redondearán las unidades (-1), decenas (-2), centenas (-3), etc.

<br/>

- `CEIL(source)` - `NUMERIC` <br/> Redondea un número al siguiente entero.
    - _source_ - `NUMERIC`: Es el número o la columna a redondear.

<br/>

- `FLOOR(source)` - `NUMERIC` <br/> Redondea un número al entero anterior.
    - _source_ - `NUMERIC`: Es el número o la columna a redondear.

<br/>

- `TRUNC(source, precision)` - `NUMERIC` <br/> Redondea un número truncado a un entero o a un número específico de decimales.
    - _source_ - `NUMERIC`: Es el número o la columna a redondear.
    - _precision_ - `NUMERIC`: Entero que indica el número de decimales. Si se ponen cero o números negativos Se redondea a la unidad por ejemplo 0 a unidades, -1 a decenas, -2 a centenas, etc.

<br/>

### Series

- `GENERATE_SERIES(start, end, step)` - `NUMERIC`, `TIMESTAMP` <br/> Genera una secuencia de números con un paso determinado.
    - _start_, _end_, _step_ - `NUMERIC`: Indica el inicio, fin y el paso de la serie. Es posible generar fechas para ello _start_ y _end_ deben ser `TEXT` conteniendo una fecha que respete ISO 8601 y _step_ debe ser `INTERVAL`.

<br/>

**Ejemplos**

```sql
-- Generar una series de fechas 1
SELECT GENERATE_SERIES('2012-10-01', '2012-10-31', INTERVAL 'P1D');

-- Generar una series de fechas 2
SELECT EXTRACT(month FROM cal.month) AS month,
       (cal.month + interval '1 month') - cal.month AS length
FROM (
        SELECT GENERATE_SERIES(TIMESTAMP '2012-01-01', TIMESTAMP '2012-12-01', INTERVAL '1 month') AS month ) cal
ORDER BY month; 
```
- [Generar series de fechas 1](https://pgexercises.com/questions/date/series.html): En este ejemplo en lugar de usar _'P1D'_ se pudo haber usado _'1 day'_.
- [Generar series de fechas 2](https://pgexercises.com/questions/date/daysinmonth.html): En este ejemplo, se genera una serie de fechas para el primer día de cada mes durante 2012. Notar que el uso de `TIMESTAMP` es opcional.



<br/><br/>

---
(funciones-null)=
## Valores `NULL`

Funciones útiles para trabajalos con valores con valores `NULL`.

- `COALESCE(arg1, arg2, ...)` - `VALUE`, `NULL` <br/> Devuelve el primer valor no `NULL` de las columnas seleccionadas de izquierda a derecha, en caso de que todos sean `NULL`, entonces devolverá un `NULL`. Opera fila por fila.
    - _arg1, arg2, ..._ - `VALUE`: Son los argumentos, normalmente serán nombres de columnas, pero puede ser valores directamente.
    - **Tip**: Una aplicación útil sería para convertir valores `NULL` en alguna otra expresión usando: <br/> `COALESCE(arg, exp);`.

<br/>

- `NULLIF(arg1, arg2)` - `NULL`, `VALUE` <br/> Retorna `NULL` si ambos elementos son iguales, sino retorna el primer elemento _arg1_.
    - _arg1, arg2_ - `VALUE`: Son los argumentos, normalmente serán nombres de columnas, pero puede ser valores directamente.

<br/><br/>

---
(funciones-sliding)=
## Window functions

Realizan cálculos de acuerdo a una ventana relativa a la fila actual. Puede ser particionada en una o más columnas. Útil para cálculos acumulados, promedios móviles, etc. Sintaxis:
```sql
-- Plantilla de uso básico de sliding windows
agg_function OVER(... ROWS BETWEEN start AND end)
```
- Es posible usar `ORDER BY` y `PARTITION BY` dentro de `OVER()`.
- _start_ y _end_ pueden ser:
    - `n PRECEDING` y `n FOLLOWING`: Se usa para especificar el número de filas antes o después de la fila actual para incluir en los cálculos. Se debe de poner el número de filas antes de la sentencia.
    - `UNBOUNDED PRECEDING` y `UNBOUNDED FOLLOWING`: Se usa para especificar que se quiere usar cada fila desde el principio o el final en el data set para incluir en los cálculos.
    - `CURRENT ROW`: Se usa para especificar que se quiere parar el cálculo en la fila actual.

---

<br/>

- `CUME_DIST() OVER([PARTITION BY EXPR] ORDER BY EXPR)` - `NUMERIC` <br/> Retorna la distribución acumulada de un valor, es decir el % de valores superiores o inferiores de un _dataset_. En otras palabras retorna la posición relativa de un valor con respecto a otros valores ordenados. Es necesario utilizar `ORDER BY` dentro de `OVER()`.
    - No tiene argumentos, para indicar la columna con los valores utilizar `OVER()`.

<br/>

- `DENSE_RANK() OVER([PARTITION BY EXPR] ORDER BY EXPR)` - `INTEGER` <br/> Asigna un ranking a cada valor en relación con los demás, si más de un fila tienen un mismo valor se les asignará el mismo ranking. A diferencia de `RANK()`, `DENSE_RANK()` siempre retorna rankings consecutivos, es decir si un valor se repite, no se saltará el ranking. Es necesario utilizar `ORDER BY` dentro de `OVER()`.
    - No tiene argumentos, para indicar la columna con los valores utilizar `OVER()`.

<br/>

- `FIRST_VALUE(col) OVER([PARTITION BY EXPR] ORDER BY EXPR)` - `VALUE` <br/> Devuelve el primer valor de una columna o partición ordenada.
    - _col_ - `VALUE`: Es el nombre de un columna, una expresión o un _subquery_ evaluado contra la primer fila de una partición ordenada de un _dataset_. Debe retorna un único valor.
    - Para indicar la columna con los valores utilizar `OVER()`.

<br/>

- `LAST_VALUE(col) OVER([PARTITION BY EXPR] ORDER BY EXPR)` - `VALUE` <br/> Devuelve el último valor de una columna o partición ordenada. Tener en cuenta que por default una tabla termina en el "valor actual", por lo tanto se debe de especificar el rango real de la tabla si se quiere encontrar el último valor de toda la tabla.
    - _col_ - `VALUE`: Es el nombre de un columna, una expresión o un _subquery_ evaluado contra la primer fila de una partición ordenada de un _dataset_. Debe retorna un único valor.
    - Para indicar la columna con los valores utilizar `OVER()`.

<br/>

- `LAG(col, [offset], [default]) OVER([PARTITION BY EXPR] ORDER BY EXPR)` - `VALUE` <br/> Regresa el valor de la columna, que está _N_ filas antes de la fila actual. En otras palabras, se utiliza para acceder a filas anteriores a la actual.
    - _col_ - `VALUE`: Es el nombre de un columna, una expresión o un _subquery_ evaluado contra la fila _N_ posiciones atrás de la actual. Debe retorna un único valor.
    - _offset_ - `INTEGER`: Es un entero positivo que indica cuantas filas antes se quiere acceder. Su valor por default es uno.
    - _default_ - `VALUE`: Es para indicar el valor que se debe de retornar en caso de que _offset_ sea mayor al número de filas anteriores. Su valor por default es `NULL`.
    - Para indicar la columna con los valores utilizar `OVER()`.

<br/>

- `LEAD(col, [offset], [default]) OVER([PARTITION BY EXPR] ORDER BY EXPR)` - `VALUE` <br/> Regresa el valor de la columna, que está _N_ filas después de la fila actual. En otras palabras, se utiliza para acceder a filas posteriores a la actual.
    - _col_ - `VALUE`: es el nombre de un columna, una expresión o un _subquery_ evaluado contra la fila _N_ después de la actual. Debe retorna un único valor.
    - _offset_ - `INTEGER`: es un entero positivo que indica cuantas filas después se quiere acceder. Su valor por default es uno.
    - _default_ - `VALUE`: es para indicar el valor que se debe de retornar en caso de que _offset_ sea mayor al número de filas posteriores. Su valor por default es `NULL`.
    - Para indicar la columna con los valores utilizar `OVER()`.

<br/>

- `NTILE(buckets) OVER([PARTITION BY EXPR] ORDER BY EXPR)` - `INTEGER` <br/> Divide el data set ordenado en _N_ partes aproximadamente iguales, a cada una de esas partes se les llama _bucket_, en cada fila se indicará a cuál _bucket_ pertenece.
    - _buckets_ - `INTEGER`: represeta el número de partes iguales en las que se quiere dividir el _dataset_.
    - Para indicar la columna con los valores utilizar `OVER()`.

<br/>

- `NTH_VALUE(col, offset) OVER([PARTITION BY EXPR] ORDER BY EXPR)` - `VALUE` <br/> Retorna el valor de la _n-ésima_ fila de un _dataset_ ordenado.
    - _col_ - `VALUE`: es el nombre de un columna, una expresión o un _subquery_.
    - _offset_ - `INTEGER`: Es un entero positivo, el número de fila relativa a la primera en la ventana en la que la expresión en evaluada.
    - Para indicar la columna con los valores utilizar `OVER()`.

<br/>

- `PERCENT_RANK() OVER([PARTITION BY EXPR] ORDER BY EXPR)` - `INTEGER` <br/> Similar a `CUME_DIST()`, evalúa La posición relativa de un valor dentro del conjunto de valores de un _dataset_ ordenado y asigna su percentil a cada valor.
    - No tiene argumentos, para indicar la columna con los valores utilizar `OVER()`.

<br/>

- `RANK() OVER([PARTITION BY EXPR] ORDER BY EXPR)` - `INTEGER` <br/> Rankea cada fila ordenada de una _dataset_, por default lo hace de manera ascendente. Si dos o más filas tienen el mismo valor, se les dará el mismo ranking, pero se saltará el número de valores repetidos en el ranking, es decir, habrá un ranking no secuencial.
    - No tiene argumentos, para indicar la columna con los valores utilizar `OVER()`.

<br/>

- `ROW_NUMBER() OVER([PARTITION BY EXPR] ORDER BY EXPR)` - `INTEGER` <br/> Sirve para enumerar las filas de un data set. Siempre va enumerar valores distintos, es decir, valores secuenciales.
    - No tiene argumentos, para indicar la columna con los valores utilizar `OVER()`.

<br/>

**Ejemplos**
```sql
-- Uso de ROW_NUMBER para enumerar filas
SELECT ROW_NUMBER() OVER(ORDER BY joindate), firstname, surname
FROM cd.members;

-- USO de RANK()
SELECT firstname, surname, hours AS hours, RANK() OVER(ORDER BY hours DESC) AS rank
FROM (
  SELECT firstname, surname, ROUND(SUM(slots)/2, -1) AS hours
  FROM cd.bookings AS b
  JOIN cd.members AS m
  USING(memid)
  GROUP BY firstname, surname) AS data
ORDER BY rank, surname, firstname;

-- USO de NTILE()
SELECT name, CASE WHEN bucket = 1 THEN 'high' WHEN bucket = 2 THEN 'average' ELSE 'low' END AS revenue
FROM(
  SELECT name, NTILE(3) OVER(ORDER BY SUM(revenue) DESC) AS bucket
  FROM (
	SELECT name, CASE WHEN memid = 0 THEN guestcost * slots ELSE membercost * slots END AS revenue
	FROM cd.bookings AS b
	JOIN cd.facilities AS f
	USING(facid)) AS subsub
  GROUP BY name
  ORDER BY bucket) AS sub
ORDER BY bucket, name;

-- USO de LEAD()
SELECT 
    DATE_PART('month', start_month) AS month, 
    (LEAD(start_month, 1, '2013-01-01') OVER () - start_month) AS length
FROM (
    SELECT GENERATE_SERIES('2012-01-01', '2012-12-01', INTERVAL '1 month') AS start_month
) AS sub;
```
- [Uso de ROW_NUMBER](https://pgexercises.com/questions/aggregates/nummembers.html): En este ejemplo se enlistan los miembros por la fecha en la que se unieron.
- [Uso de RANK](https://pgexercises.com/questions/aggregates/rankmembers.html): En este ejemplo se rankea a los miembros por el número de horas reservadas.
- [Uso de NTILE](https://pgexercises.com/questions/aggregates/classify.html): En este ejemplo se clasifica los _facilities_ por el ingreso en partes iguales.
- [Uso de LEAD](https://pgexercises.com/questions/date/daysinmonth.html): En este ejemplo, primero se crea un subquery para determinar el primer día de cada mes durante 2012, posteriormente se utiliza una _window function_ para calcular la diferencia entre el mes siguiente y el mes actual. En la función `LEAD()` se usa el parámetros `default='2013-01-01'` para que diciembre se pueda calcular correctamente.

