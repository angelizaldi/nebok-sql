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
  - Cero o más caracteres
  - `a%`: Cualquier cadena que empiece con 'a'
* - `_`
  - Un caracter
  - `h_t`: Cualquier cadena de tres caracteres que empiece con 'h' y termine con 't'
* - `[]`
  - Cualquier carácter dentro de los corchetes
  - `h[oa]t`: Cualquier cadena que empiece con h, le sigue una 'o' o una 'a' y termine con 't'
* - `^`
  - Cualquier carácter que no esté dentro de los corchetes
  - `h^[oa]t`: Cualquier cadena que empiece con 'h', no le sigue una 'o' o una 'a' y termine con 't'
* - `-`
  - Un rango de caracteres. Los rangos se pueden definir como: `[0-9]`, `[a-Z]`, `[a-z]`, `[A-Z]`.
  - `c[a-e]`: Cualquier cadena que empiece con 'c' y le sigua una 'a', 'b', 'c', 'd' o una 'e'
```

<br/>

## 2. Regular Expressions

A continuación se enlistan los caracteres especiales para usarlos con expresiones regulares.

```{list-table}
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
  - `~ 'apple'`: La palabra 'apple', independientemente de la combinación de mayúsculas y minúsculas de las letras.
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