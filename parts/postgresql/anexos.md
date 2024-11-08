# Anexos

## 1. WildCards

Al momentos de usar `LIKE` o `ILIKE` se pueden usar los siguientes _wildcards_ para afinar el filtrado.

```{list-table} Wildcards
:header-rows: 1
:name: anexos-wildcards

* - Símbolo
  - Descripción
  - Ejemplo
* - `%`
  - Cero o más caracteres.
  - `a%`: Cualquier cadena que empiece con _'a'_.
* - `_`
  - Un caracter.
  - `h_t`: Cualquier cadena de tres caracteres que empiece con _'h'_ y termine con _'t'_.
* - `[]`
  - Cualquier carácter dentro de los corchetes.
  - `h[oa]t`: Cualquier cadena que empiece con _'h'_, le sigue una _'o'_ o una _'a'_ y termine con _'t'_.
* - `^`
  - Cualquier carácter que no esté dentro de los corchetes.
  - `h^[oa]t`: Cualquier cadena que empiece con _'h'_, no le sigue una _'o'_ o una _'a'_ y termine con _'t'_.
* - `-`
  - Un rango de caracteres. Los rangos se pueden definir como: `[0-9]`, `[a-Z]`, `[a-z]`, `[A-Z]`.
  - `c[a-e]`: Cualquier cadena que empiece con _'c'_ y le sigua una _'a'_, _'b'_, _'c'_, _'d'_ o una _'e'_.
```

<br/>

## 2. Regular Expressions

A continuación se enlistan los caracteres especiales para usarlos con expresiones regulares (ver {ref}`dql-filtrado-posix`). 

```{list-table} Caracteres Especiales
:header-rows: 1
:name: table-caracteres-especiales

* - Carácter
  - Descripción
  - Ejemplo
* - `~`
  - Indica una búsqueda sensible a minúsculas y mayúsculas.
  - `~ 'Apple'`: La palabra 'Apple', con la primer letra en mayúsculas.
* - `~*`
  - Indica una búsqueda no sensible a minúsculas y mayúsculas.
  - `~* 'apple'`: La palabra 'apple', independientemente de la combinación de mayúsculas y minúsculas de las letras.
* - `[]`
  - Un conjunto de caracteres. Se busca cualquier caracter contenido en el conjunto. Para representar rangos de letras y números usar a-z, A-Z y 0-9.
  - `[a-m]`: De la "a" a la "m". <br>
    `[#$&]`: Cualquier carácter "#", "$" o "&".
* - `^`
  - La cadena empiece con.
  - `^he`: La cadena mpiece con "he".
* - `$`
  - La cadena termine con.
  - `llo$`: La cadena ermine con "llo".
* - `*`
  - Cero o más ocurrencias del caracter a la izquierda.
  - `aix*`: El patrón "ai" seguido por cero o más "x".
* - `+`
  - Una o más ocurrencias del caracter a la izquierda.
  - `aix+`: El patrón "ai" seguido por una o más "x".
* - `?`
  - Cero o una ocurrencia del caracter a la izquierda.
  - `aix?`: El patrón "ai" seguido por cero o una "x".
* - `{}`
  - Número específico de ocurrencias del caracter a la izquierda.
  - `al{N}`: Buscar el patrón "a" seguido por _N_ "l". <br>
    `al{N,M}`: Buscar el patrón "a" seguido por mínimo _N_ "l" y máximo _M_ "l". Si se omite _M_ sería infinto y si se omite _N_ sería cero.
* - `|`
  - O
  - `falls|stays`: Buscar cualquiera de esos dos patrones.
```

<br/>

(anexos-field)=
## 3. Parámetro _Field_

El argumento _field_ de funciones como `EXTRACT()` o `DATE_PART()` y su significado/uso depende del tipo de dato de _source_ (`TIMESTAMP` o `INTERVAL`, ver tabla).

| Field Value | TIMESTAMP | INTERVAL |
| --- | --- | --- |
| `CENTURY` | El siglo | Número de siglos. |
| `DAY` | Día del mes (1-31) | Número de días. |
| `DECADE` | La decada | La decada |
| `DOW` | Día de la semana: Domingo (0) a Sábado (6) | N/A |
| `DOY` | Día del año, desde 1 a 366 | N/A |
| `EPOCH` | Número de segundos desde 1970-01-01 00:00:00 UTC | Número total de segundos en el intervalo. |
| `HOUR` | La hora (0-23) | Número de horas |
| `ISODOW` | Día de la semana basado en ISO 8601 Lunes (1) a Domingo (7) | N/A |
| `ISOYEAR` | ISO 8601 week number of year | N/A |
| `MICROSECONDS` | Segundos, incluyendo decimales, multiplicado por 1000000 | Segundos, incluyendo decimales, multiplicado por 1000000 |
| `MILLENNIUM` | El milenio | Número de milenios |
| `MILLISECONDS` | Segundos, incluyendo decimales, multiplicado por 1000 | Segundos, incluyendo decimales, multiplicado por 1000 |
| `MINUTE` | Minutos (0-59) | Número de minutos |
| `MONTH` | Mes, 1-12 | Número de meses 
| `QUARTER` | Trimestres | Número de trimestres |
| `SECOND` | Segundos | Número de segundos. |
| `TIMEZONE` | El desfase en segundos entre la zona horaria y UTC. | N/A |
| `TIMEZONE_HOUR` | The hour component of the time zone offset | N/A |
| `TIMEZONE_MINUTE` | The minute component of the time zone offset | N/A |
| `WEEK` | Número de la semana en el año, de acuero a ISO 8601 | N/A |
| `YEAR` | Año | Año |

(anexos-literales-fecha)=
## 4. Literales de fecha

### Patrones

Estos patrones se utilizan en funciones como `TO_CHAR()`, `TO_DATE()` y `TO_TIMESTAMP()` para darle un formato específico a una fecha y tiempo, o indicar el formato de una cadena que representa una fecha y tiempo.

| **Patrón**         | **Descripción**                                                                                      |
|--------------------|------------------------------------------------------------------------------------------------------|
| `HH`               | Hora del día (01–12).                                                                                 |
| `HH12`             | Hora del día (01–12).                                                                                 |
| `HH24`             | Hora del día (00–23).                                                                                 |
| `MI`               | Minuto (00–59).                                                                                       |
| `SS`               | Segundo (00–59).                                                                                      |
| `MS`               | Milisegundo (000–999).                                                                                |
| `US`               | Microsegundo (000000–999999).                                                                         |
| `FF1`              | Décima de segundo (0–9).                                                                              |
| `FF2`              | Centésima de segundo (00–99).                                                                         |
| `FF3`              | Milisegundo (000–999).                                                                                |
| `FF4`              | Décima de milisegundo (0000–9999).                                                                    |
| `FF5`              | Centésima de milisegundo (00000–99999).                                                               |
| `FF6`              | Microsegundo (000000–999999).                                                                         |
| `SSSS, SSSSS`      | Segundos después de la medianoche (0–86399).                                                          |
| `AM, am, PM, pm`   | Indicador de meridiano (sin puntos).                                                                  |
| `A.M., a.m., P.M., p.m.` | Indicador de meridiano (con puntos).                                                            |
| `Y,YYY`            | Año (4 o más dígitos) con coma.                                                                       |
| `YYYY`             | Año (4 dígitos).                                                                                      |
| `YYY`              | Últimos 3 dígitos del año.                                                                            |
| `YY`               | Últimos 2 dígitos del año.                                                                            |
| `Y`                | Último dígito del año.                                                                                |
| `IYYY`             | Año en numeración de semanas ISO 8601 (4 o más dígitos).                                              |
| `IYY`              | Últimos 3 dígitos del año en numeración ISO 8601.                                                     |
| `IY`               | Últimos 2 dígitos del año en numeración ISO 8601.                                                     |
| `I`                | Último dígito del año en numeración ISO 8601.                                                         |
| `BC, bc, AD, ad`   | Indicador de era (sin puntos).                                                                        |
| `B.C., b.c., A.D., a.d.` | Indicador de era (con puntos).                                                                  |
| `MONTH`            | Nombre completo del mes en mayúsculas (rellenado con espacios a 9 caracteres).                        |
| `Month`            | Nombre completo del mes capitalizado (rellenado con espacios a 9 caracteres).                         |
| `month`            | Nombre completo del mes en minúsculas (rellenado con espacios a 9 caracteres).                        |
| `MON`              | Nombre abreviado del mes en mayúsculas (3 caracteres en inglés, la longitud varía según la localización).|
| `Mon`              | Nombre abreviado del mes capitalizado (3 caracteres en inglés, la longitud varía según la localización).|
| `mon`              | Nombre abreviado del mes en minúsculas (3 caracteres en inglés, la longitud varía según la localización).|
| `MM`               | Número del mes (01–12).                                                                               |
| `DAY`              | Nombre completo del día en mayúsculas (rellenado con espacios a 9 caracteres).                        |
| `Day`              | Nombre completo del día capitalizado (rellenado con espacios a 9 caracteres).                         |
| `day`              | Nombre completo del día en minúsculas (rellenado con espacios a 9 caracteres).                        |
| `DY`               | Nombre abreviado del día en mayúsculas (3 caracteres en inglés, la longitud varía según la localización).|
| `Dy`               | Nombre abreviado del día capitalizado (3 caracteres en inglés, la longitud varía según la localización).|
| `dy`               | Nombre abreviado del día en minúsculas (3 caracteres en inglés, la longitud varía según la localización).|
| `DDD`              | Día del año (001–366).                                                                                |
| `IDDD`             | Día del año en numeración ISO 8601 (001–371; el día 1 del año es el lunes de la primera semana ISO).   |
| `DD`               | Día del mes (01–31).                                                                                  |
| `D`                | Día de la semana, de domingo (1) a sábado (7).                                                        |
| `ID`               | Día de la semana en numeración ISO 8601, de lunes (1) a domingo (7).                                   |
| `W`                | Semana del mes (1–5) (la primera semana comienza el primer día del mes).                               |
| `WW`               | Número de semana del año (1–53) (la primera semana comienza el primer día del año).                    |
| `IW`               | Número de semana del año en numeración ISO 8601 (01–53; la primera semana incluye el primer jueves del año).|
| `CC`               | Siglo (2 dígitos) (el siglo XXI comienza el 01-01-2001).                                              |
| `J`                | Fecha juliana (días enteros desde el 24 de noviembre de 4714 a.C. a medianoche local).                 |
| `Q`                | Trimestre.                                                                                            |
| `RM`               | Mes en números romanos en mayúsculas (I–XII; I = enero).                                               |
| `rm`               | Mes en números romanos en minúsculas (i–xii; i = enero).                                               |
| `TZ`               | Abreviatura de la zona horaria en mayúsculas.                                                          |
| `tz`               | Abreviatura de la zona horaria en minúsculas.                                                          |
| `TZH`              | Horas de la zona horaria.                                                                             |
| `TZM`              | Minutos de la zona horaria.                                                                           |
| `OF`               | Desplazamiento de la zona horaria desde UTC (HH o HH:MM).                                              |

### Prefijos y sufijos

Estos modificadores se utilizan junto con los patrones de fechas y tiempo para añadir información extra.


| **Modificador** | **Descripción**                                                                               | **Ejemplo**                          |
|-----------------|-----------------------------------------------------------------------------------------------|--------------------------------------|
| Prefijo **FM**   | Modo de relleno (suprime ceros a la izquierda y espacios en blanco de relleno).               | `FMMonth`                            |
| Sufijo **TH**    | Sufijo de número ordinal en mayúsculas.                                                       | `DDTH`, por ejemplo, `12TH`          |
| Sufijo **th**    | Sufijo de número ordinal en minúsculas.                                                       | `DDth`, por ejemplo, `12th`          |
| Prefijo **FX**   | Opción global de formato fijo (ver notas de uso).                                             | `FX Month DD Day`                    |
| Prefijo **TM**   | Modo de traducción (utiliza nombres de días y meses localizados según `lc_time`).             | `TMMonth`                            |
| Sufijo **SP**    | Modo de escritura (no implementado).                                                          | `DDSP`                               |


(anexos-literales-numero)=
## 5. Literales de número

### Patrones

Estos patrones se utilizan en la función `TO_CHAR()` para indicar el formato de una cadena que representa un número.

| **Patrón**       | **Descripción**                                                                                      | **Ejemplo** |
|------------------|------------------------------------------------------------------------------------------------------| ----------  |
| `9`              | Posición del dígito (puede omitirse si es insignificante).                                            | `to_char(485, '999')` -> '485' |
| `0`              | Posición del dígito (no se omite, incluso si es insignificante).                                      | `to_char(0.1, '0.9')` -> '0.1' |
| `.` (punto)      | Punto decimal.                                                                                        | `to_char(-0.1, '99.99')` -> '-.10' |
| `,` (coma)       | Separador de miles.                                                                                   | `to_char(1485, '9,999')` -> '1,485' |
| `PR`             | Valor negativo entre corchetes angulares.                                                             | `to_char(-485, '999PR')` -> '<485>' |
| `S`              | Signo anclado al número (usa configuración regional).                                                 | `to_char(-485, '999S')` -> '485-' |
| `L`              | Símbolo de moneda (usa configuración regional).                                                       | `to_char(485, 'L999')` -> 'DM 485' |
| `D`              | Punto decimal (usa configuración regional).                                                           | `to_char(148.5, '999D999')` -> '148,500' |
| `G`              | Separador de grupo (usa configuración regional).                                                      | `to_char(1485, '9G999')` -> '1 485' |
| `MI`             | Signo negativo en la posición especificada (si el número es < 0).                                     | `to_char(-485, '999MI')` -> '485-' |
| `PL`             | Signo positivo en la posición especificada (si el número es > 0).                                     | `to_char(485, 'PL999')` -> '+485' |
| `SG`             | Signo de más/menos en la posición especificada.                                                       | `to_char(-485, 'SG999')` -> '-485' |
| `RN`             | Número romano (entrada entre 1 y 3999).                                                               | `to_char(485, 'FMRN')` -> 'CDLXXXV' |
| `TH` o `th`      | Sufijo de número ordinal.                                                                             | `to_char(482, '999th')` -> ' 482nd' |
| `V`              | Desplaza el número especificado de dígitos (ver notas).                                               | `to_char(12.45, '99V9')` -> ' 125' |
| `EEEE`           | Exponente para notación científica.                                                                   | `to_char(0.0004859, '9.99EEEE')` -> ' 4.86e-04' |

### Prefijos y sufijos

Estos modificadores se utilizan junto con los patrones de número para añadir información extra.

| **Modificador**   | **Descripción**                                                                                      | **Ejemplo**   |
|-------------------|------------------------------------------------------------------------------------------------------|---------------|
| **Prefijo FM**    | Modo de relleno (suprime ceros a la derecha y espacios de relleno).                                   | `FM99.99`     |
| **Sufijo TH**     | Sufijo de número ordinal en mayúsculas.                                                              | `999TH`       |
| **Sufijo th**     | Sufijo de número ordinal en minúsculas.                                                              | `999th`       |
