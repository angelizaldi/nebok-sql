## Tipos de Datos

En esta sección se explica brevenmente los tipos de datos más comunes en PostgreSQL. Los tipos de datos definen lo que se denomina _el dominio_ de una columna, así como las operaciones posibles de los datos. 

<br/>

---
### Bool

Almacena valores booleanos.
-	Su nombre en SQL es: `BOOLEAN` | `BOOL`.
-	Puede tomar tres posibles valores: `TRUE`, `FALSE` o `NULL`.
-	Si se inserta datos a una columna `BOOL` se hacen las siguientes conversiones:
    -	`TRUE`: '1', 'yes', 'y', 't', true.
    -	`FALSE`: '0', 'no', 'n', 'f', false.
    -	Cualquiera de los valores anteriores se pueden utilizar en lugar de `TRUE` o `FALSE`.
-	Cuando se hacen comparaciones con columnas `BOOL`, no es necesario poner como tal la comparación, ya que los valores ya son `FALSE` o `TRUE`.
-	Si se selecciona datos de una columna `BOOL` se hacen las siguientes conversiones:
    -	`TRUE`: 't'
    -	`FALSE`: 'f'
    -	`NULL`: _espacio_.

<br/>

--- 
### Carácter

```{important}
SQL es sensible a mayúsculas y minúsculas. Además al ordenar cadenas se priorizará primero los espacios en blanco, después las mayúsculas y finalmente las minúsculas.
```

```{note}
Para usar cadenas en el _query_ se deben de poner entre comillas simples: `'texto'`.
```

Existen tres tipos de datos de carácter/cadenas:
-	`CHAR(N)` | `CHARACTER(N)`: Cadena de longitud fija de _n_ caracteres. Si se agrega una cadena de longitud menor a n, PostgreSQL añadirá espacios al final para que su longitud sea de _n_. Si se agrega una cadena de longitud mayor a _n_, se arrojará un error. Los espacios extras se ignorarán al hacer comparaciones entre este tipo de cadenas. Si no se especifica _n_, equivale a `CHAR(1)`.
-	`VARCHAR(N)` | `CHARACTER VARYING(N)`: Cadena que almacena hasta un máximo de _n_ caracteres, puede ser menor, pero no mayor.
-	`TEXT` | `VARCHAR`: Cadena de longitud variable.

<br/>

#### Concatenación

Para concatenar cadenas se puede hacer uso de los operadores `||`. Ejemplo:
```sql
col1 || ' ' || col2
``` 
-	Se concatenará el contenido de _col1_ y _col2_, separados por un espacio.
-	**IMPORTANTE**: No se puede concatenar valores `NULL`, si se utiliza un `NULL` convertirá toda la cadena en `NULL`.
-	**IMPORTANTE**: También se puede usar la función `CONCAT()`.

<br/>

---
### Numéricos

Existen tres tipos de datos numéricos para número enteros y para números décimales que se explican a continuación.
- Enteros.
- Flotantes.
- `NaN`.

#### Enteros

Existen los siguientes tipos de datos numéricos enteros:
-	`SMALLINT` | `INT2`: Rango de -32,768 a 32,767.
-	`INT` | `INTEGER` | `INT4`: Rango de -2,147,483,648 a 2,147,483,647.
-	`BIGINT` | `INT8`: Rango de -9,223,372,036,854,775,808 a +9,223,372,036,854,775,807.
-	`SERIAL`: Igual que `INT`, pero PostgreSQL generará y llenará de manera automática las columnas que sean de este tipo.
-	`SMALLSERIAL` | `INT8`: 1 a 32,767.
-	`SERIAL` | `SERIAL2`: 1 to 2,147,483,647.
-	`BIGSERIAL` | `SERIAL8`: 1 to 9,223,372,036,854,775,807.

<br/>

#### Flotantes

Existen tres tipos de datos numéricos flotantes:
-	`REAL` | `FLOAT4`: Número décimal de 4 bytes. 6 décimales de precisión.
-	`DOUBLE PRECISION` | `FLOAT8`: Número decimal de 8 bytes. 15 décimales de precisión.
-	`NUMERIC` |  `NUMERIC(P, S)`: Número de _p_ dígitos, con _s_ décimales. Puede soportar 131,072 dígitos y 16,383 décimales. 

#### NaN

Es un valor especial que significa _'Not a Number'_, se puede definir usando `'NaN'` (entre comillas simples).

<br/>

---
### Fechas y Tiempo

Existen 4 tipos principales para almacenar fechas y tiempo, todas ella respetan _ISO 8601_ y para hacer referencia a una fecha en el _query_ es necesario ponerla como una cadena (entre comillas simples), pero **no** es necesario convertir la cadena a una fecha por medio de algún método para convertir datos explícitamente. Por ejemplo, para hacer referencia a una fecha en un _query_ usar: <br> `'YYYY-MM-DD'`

:::{note}
_ISO 8601_ se define una fecha y tiempo siguiendo el siguiente formato:

`YYYY-MM-DDTHH:MM:SS`
- _YYYY_: Año de cuatro dígitos, desde 0000 a 9999.
- _MM_: Mes de dos dígitos, del 01 a 12.
- _DD_: Día de dos dígitos, del 01 al 31.
- _HH_: Horas de dos dígitos, desde 00 a 23.
- _MM_: Minutos de dos dígitos, desde 00 a 59.
- _SS_: Segundos de dos dígitos, desde 00 a 59.
- **Importante**: Los campos de fechas son obligatorios, mientras que los campos de tiempo son opcionales.
:::

<br/>

#### Creación de un valor de tipo fecha.

Para crear una fecha o intervalo en el _query_ se pueden utilizar tres opciones (ejemplos para el 31 de agosto del 2012 a la 1 en SELECT):
```sql
-- Usando TIMESTAMP o algún tipo de dato de fecha:
SELECT TIMESTAMP '2012-08-31 01:00:00';

-- Convirtiendo una cadena a una fecha:
SELECT '2012-08-31 01:00:00'::TIMESTAMP;

-- Convirtiendo una cadena a una fecha con la función CAST:
SELECT CAST('2012-08-31 01:00:00' AS TIMESTAMP);
```
- No necesariamente tiene que ser `TIMESTAMP` puede ser cualquier otro tipo de dato de fecha o tiempo.

<br/>

#### DATE

`DATE` Almacena fechas y se deben de ingresar como cadena (entre comillas simples). El formato válido es el siguiente:
```sql
-- Formato válidos para crear un tipo DATE
'YYYY-MM-DD'
```
- _YYYY_: Año de cuatro dígitos, desde 0000 a 9999.
- _MM_: Mes de dos dígitos, del 01 a 12.
- _DD_: Día de dos dígitos, del 01 al 31.

:::{tip}
Para obtener la fecha actual usar el comando: <br/> `CURRENT_DATE`
:::

<br/>

#### Time

`TIME(PRECISION)`: Almacena tiempo. Algunos formatos admisibles son:
```sql
-- Formatos válidos para crear un tipo TIME
'HH:MM:SS'
'HHMMSS'
```
- _HH_: Horas de dos dígitos, desde 00 a 23.
- _MM_: Minutos de dos dígitos, desde 00 a 59.
- _SS_: Segundos de dos dígitos, desde 00 a 59.

El tiempo puede almacenar la precisión, para ello solo agregar hasta 6 dígitos:
```sql
-- Time con precisión de hasta 6 dígitos
'HH:MM:SS.pppppp'
```
- _HH_: Horas de dos dígitos, desde 00 a 23.
- _MM_: Minutos de dos dígitos, desde 00 a 59.
- _SS_: Segundos de dos dígitos, desde 00 a 59.
- _p_ son dígitos.

:::{tip}
Para obtener la hora actual con zona horaria usar el comando: <br/> `CURRENT_TIME`
:::


:::{note}
Para revisar como crear `TIME` con zona horaria revisar {ref}`tipos-zona-horaria`.
:::

#### Timestamp

`TIMESTAMP` almacena fechas y tiempo, se deben de ingresar como cadenas (entre comillas simples). 

Existen dos tipos:
-	**timestamp**: No almacena la zona horaria, por lo que modificaciones en la zona horaria del sistema no actualizará las horas almacenadas en la base de datos.
-	**timestamptz**: Almacena la zona horaria, por lo que modificaciones en la zona horaria del sistema actualizará las horas almacenadas en la base de datos. Éstas se almacenan en UTC.

Los formatos válidos son los siguiente:
```sql
-- TIMESTAMP sin zona horaria
'YYYY-MM-DD HH:MM:SS'

-- TIMESTAMP con zona horaria
'YYYY-MM-DD HH:MM:SS' AT TIME ZONE 'time_zone'
```
- _YYYY_: Año de cuatro dígitos, desde 0000 a 9999.
- _MM_: Mes de dos dígitos, del 01 a 12.
- _DD_: Día de dos dígitos, del 01 al 31.
- _HH_: Horas de dos dígitos, desde 00 a 23.
- _MM_: Minutos de dos dígitos, desde 00 a 59.
- _SS_: Segundos de dos dígitos, desde 00 a 59.
- _'time\_zone'_ es una abreviación en cadena de la zona horaria o una cadena de tipo `'Continent/City'`. Para consultar los códigos visitar [esta página](http://www.healthstream.com/hlchelp/Administrator/Classes/HLC_Time_Zone_Abbreviations.htm).

:::{tip}
Para obtener la fecha y hora actual con zona horaria usar el comando: <br/> `CURRENT_TIMESTAMP`
:::

:::{note}
Para revisar como crear `TIMESTAMP` con zona horaria revisar {ref}`tipos-zona-horaria`.
:::

(tipos-zona-horaria)=
#### Zonas horarias

El tiempo puede tener una zona horaria, cuando se declare una columna del tipo `TIME` es necesario indicar que debe incluir zona horaria:
```sql
-- Agregar zona horaria
expression AT TIME ZONE 'time_zone'
expression AT TIMESTAMP ZONE 'time_zone'
```
-	_expression_ puede ser una columna o un valor de tipo `TIME`.
-	_'time\_zone'_ es una abreviación en cadena de la zona horaria o una cadena de tipo `'Continent/City'`. Para consultar los códigos visitar [esta página](http://www.healthstream.com/hlchelp/Administrator/Classes/HLC_Time_Zone_Abbreviations.htm).t

Si un tiempo tiene zona horaria, aparecerá al final con un _+N_, que significará que está _N_ horas por delante (+) o detrás (-) de _UTC_.
```sql
-- Time con zona horaria
'HH:MM:SS.pppppp+N'
```

Para convertir a otra zona horaria usar:
```sql
-- Modificar zona horaria
expression AT TIME ZONE 'time_zone'
expression AT TIMESTAMP ZONE 'time_zone'
```
-	_expression_ puede ser una columna o un valor de tipo `TIME` con zona horaria.
-	_'time\_zone'_ es una abreviación en cadena de la zona horaria o una cadena de tipo `'Continent/City'`. Para consultar los códigos visitar [esta página](http://www.healthstream.com/hlchelp/Administrator/Classes/HLC_Time_Zone_Abbreviations.htm).

Existen unos comandos especiales para establecer o mostrar la zona horaria actual del servidor (no se usa dentro de `SELECT`):
```sql
-- Para establecer la zona horaria del servidor.
SET timezone = 'time_zone'

-- Para mostrar la zona horaria del servidor.
SHOW timezone
```
-	_'time\_zone'_ es una abreviación en cadena de la zona horaria o una cadena de tipo `'Continent/City'`. Para consultar los códigos visitar [esta página](http://www.healthstream.com/hlchelp/Administrator/Classes/HLC_Time_Zone_Abbreviations.htm).

<br/>

#### Interval

`INTERVAL` Permite almacenar y manipular periodos de tiempo en años, meses, días, horas, minutos, segundos, etc. 

Se definen como cadenas, especificando el número y el campo, pueden ser más de uno y estos se se puede estar en singular o plural, además se puede definir una dirección, la sintaxis general es:
```sql
-- Definir un intervalo
INTERVAL 'quantity unit [quantity2 unit2] ... [AGO]'
```
- _quantity_ es un número positivo o negativo.
- _unit_ es la unidad de tiempo, puede ser:
    - `millennium`
    - `century`
    - `decade`
    - `year`
    - `month`
    - `week`
    - `day`
    - `hour`
    - `minute`
    - `second`
    - `millisecond`
    - `microsecond`
    - o abreviaciones de los anteriores (`y`, `m`, `d`, etc.) o formas plurales (`months`, `days`, etc.).
    - `AGO` convierte el intervalo en un intervalo negativo.

Algunos ejemplos de intervalos:
```sql
-- Intervalo de 2 meses
INTERVAL '2 months'

-- Intervalo de 3 horas y 20 minutos
INTERVAL '3 hours 20 minutes'

-- Intervalo de 1 año, 2 meses y 3 días
INTERVAL '1 year 2 months 3 days';

-- Intervalo para representar 2 semanas atrás, es decir -14 días
INTERVAL '2 weeks AGO';
```

Existe otra forma de definir los intervalos respetando _ISO 8601_. El formato ISO es de la forma:
```sql
-- Formato ISO 8601 para intervalos
P quantity unit [ quantity unit ...] [ T [ quantity unit ...]]
```
-	Empieza por la letra _P_.
-	_T_ es para indicar el comienzo de las unidades de tiempo.
-	Se escribe todo junto con las abreviaciones de las unidades.
-	Las abreviaciones de las unidades son:
    -	_Y_: Years
    -	_M_: Months (en la parte de fecha)
    -	_W_: Weeks
    -	_D_: Days
    -	_H_: Hours
    -	_M_: Minutes (en la parte de tiempo)
    -	_S_: Seconds
-	Por ejemplo, el intervalo "6 years 5 months 4 days 3 hours 2 minutes 1 second", se escribe: <br> `P6Y5M4DT3H2M1S`
-	Otra alternativa sería:  <br> `P [YYYY-MM-DD][T HH:MM:SS]` <br> entonces el intervalo anterior se escribiría como: <br>  `P0006-05-04T03:02:01`

Los mismo ejemplos dados anteriormente, pero ahora como intervalos usando ISO 8601:
```sql
-- Intervalo de 2 meses
INTERVAL 'P2M'
INTERVAL 'P0000-02-00'

-- Intervalo de 3 horas y 20 minutos
INTERVAL 'PT3H20M'
INTERVAL 'PT03:20:00'

-- Intervalo de 1 año, 2 meses y 3 días
INTERVAL 'P1Y2M3D';
INTERVAL 'P0001-02-03';

-- Intervalo para representar 2 semanas atrás, es decir -14 días
- INTERVAL 'P2W';
```

<br/>

#### Operaciones y comparaciones con fechas

Algunas características de realizar operaciones con fechas:
- Se pueden hacer comparaciones entre fechas con los operadores de {ref}`operadores-comparacion` y retornar valores `BOOL`. Ejemplo con 'mayor que' y `DATE`: <br> `DATE 'YYYY-MM-DD' > DATE 'YYYY-MM-DD'`
- Se pueden extraer dos fechas dando como resultado un entero o un intervalo. Si una de las fechas no es una columna se debe de poner _DATE/TIMESTAMP_ antes de la fecha. Ejemplo de restarle a una columna de fecha una fecha específica: <br> `col - DATE|TIMESTAMP 'YYYY-MM-DD'`
- A una `DATE`/`TIMESTAMP` se le puede sumar/extraer un entero que representa días, dando como resultado `DATE`/`TIMESTAMP`. Ejemplo de agregar _N_ días a una fecha. No es estrictamente necesario poner `INT`. **IMPORTANTE**: si se quiere agregar una unidad diferente a días usar `INTERVAL`. <BR> `DATE 'YYYY-MM-DD' + INT N`
- Si se extraen dos `TIMESTAMPS` dará como resultado `INTERVAL`.
- Se puede usar `INTERVAL` para hacer operaciones más complejas con fechas y tiempos.

<br/>

---
### Arrays

Cada tipo de dato tiene su contraparte en array, por ejemplo, la contraparte de `INTEGER` es `INTEGER[]`. 

#### Crear columnas de tipo array

Cualquier columna puede ser del tipo _array_, para ello solo agregar los corchetes al momento de definir la columna (ejemplo con CHAR):
```sql
-- Crear tabla con columnas de tipo array
CREATE TABLE table(
    - En este ejempo son arreglos de cadenas
    col1 CHAR[] 	-- Vector
    col2 CHAR[][]	-- Matriz
    ...
);
```
-	_col1_, _col2_ será los nombres de la columnas.

<br/>

#### Insertar valores a un array

Para insertar valores a una columna de tipo _array_ en una tabla usar:
```SQL
-- Insertar valores a columnas de tipo _array_
INSERT INTO TABLE (col1, col2, ...)
VALUES(val1, ARRAY[vali, vali+1 ], ...)
```
-	En este caso _col2_ es un _array_, al momento de insertar los valores se ponen entre corchetes junto a la sentencia `ARRAY` y cada uno de los valores del _array_ separados por coma.
-	En lugar de usar `ARRAY` se puede usar llaves, debe de ir entre comillas simples. Si los elementos son de tipo carácter, cada elemento debe de ir entre comillas dobles y todo entre comillas simples: <br> `VALUES(val1, '{"vali", "valj", ...}', ...)`
-	Para array de dos dimensiones usar:
```SQL
-- Con la sentencia ARRAY
ARRAY[[val1,1, val1,2, ...], [val2,1, val2,2, ...]]
	
-- Con la notación entre llaves:
{{val1, val2, ...}, {val1, val2, ...}, ...}
```

<br/>

#### Seleccionar elementos de un array

Para seleccionar elementos de un _array_ se hace uso de corchetes y los índices de los elementos:
```sql
-- Seleccionar elemento en el índice 'i'
col[i]

-- Seleccionar elemento la coordenada '(i,j)'
col[i][j]
```
-	_col_ es el nombre de una columna de tipo _array_.
-	_i_, _j_ son los índices del elemento, empiezan por uno.

<br/>

#### Modificar un elementos

Para modificar un elemento en concreto de una columna de tipo _array_, se debe seleccionar y asignar un nuevo valor:

```sql
-- Modificar elemento en el índice 'i'
UPDATE table
SET col[i] = val
[WHERE condición];
```
-	_table_ y _col_ son el nombre de la tabla y columna respectivamente. La columna tiene que ser del tipo _array_.
-	Se puede especificar una condición con respecto a las columnas y valores de la tabla, no del _array_.
-	**IMPORTANTE**: Si no se especifica el índice, todo el _array_ será reemplazado por _val_.
-	**IMPORTANTE**: Si el _array_ es de dos dimensiones indicar ambos índices.

<br/>

#### Verificar que un elemento esté en un array


Se puede utilizar la función `ANY()` para verificar que un elemento esté en un _array_:
```sql
-- Usando la función ANY()
val = ANY(col);

-- Otra formar sería:
nomArray @> ARRAY[val] 
```
- si `val` es una cadena, se debe de poner entre comillas simples.
- Las expresiones anteriores retornan valores `TRUE` o `FALSE`.

<br/>
 
### NULL

Es un campo sin valor. Al insertar una nueva fila, si no se especifica el valor de determinada columna, entonces un valor `NULL` será puesto en esa columna (siempre y cuando la columna pueda contener valores `NULL`). `NULL` puede significar muchas cosas, como desconocido, que no existe, que no aplica, etc.

#### Verificar que un campo sea nulo

No se puede verificar que un campo sea `NULL` con operadores de comparación (`=` o `<>`), es necesario usar `IS` o `IS NOT`.
```sql
-- Verificar que los valores se una columnas sean o no sean NULL
col IS NULL
col IS NOT NULL
```
- _col_ es el nombre de una columna.
- Normalmente esta comparación se hará en la sentencia `WHERE`.

<br/>

### Tipos personalizados

Es posible crear tipos de datos personalizados para ello usar:
```sql
-- Crear un tipo personalizado
CREATE TYPE nom AS 
ENUM('exp1', 'exp2', ...);
```
-	No estrictamente se tiene que usar `ENUM()`.
-	Usar `ENUM()` solo es recomendado para establecer una lista de valores personalizados que rara vez cambia, como los días de las semanas, ciudades o estados, etc.

<br/>
 
### Otros

Otros tipos de datos existentes en PostgreSQL son:
- `JSON`
- `UUID`.
- Epeciales:
    - box`
    - `line`.
    - `point`.
    - `lseg`.
    - `polygon`.
    - `inet`.
    - `macaddr`.

<br/>
 
### Conversión

Para convertir un tipo de dato a otro usar:
```sql
-- Converter 'expression' a 'type'
expression :: type
```
- _expression_ es un valor o columna de algún tipo de dato.
- _type_ es el tipo de dato al que se desea convertir.

:::{caution}
No todas las conversiones entre tipos de datos son válidas.
:::

:::{tip}
También es posible usar la función `CAST()`.
:::