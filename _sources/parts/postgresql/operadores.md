# Operadores

En esta sección se presentan los operadores en SQL.

## Comparación

Operadores útiles para comparar valores.

```{list-table} Operadores de Comparación
:header-rows: 1
:name: operadores-comparacion
* - Operador
  - Descripción
* - `=`
  - Igualdad
* - `<>` o `!=`
  - No igual*
* - `<`
  - Menor que
* - `>`
  - Mayor que
* - `<=`
  - Menor o igual a
* - `>=`
  - Mayor o igual a
```

<br/>

## Operadores Bitwise

```{list-table} Operadores Bitwise
:header-rows: 1
:name: operadores-bitwise

* - Operador
  - Descripción
* - `&`
  - _Bitwise And_
* - `|`
  - _Bitwise Or_
* - `^`
  - _Bitwise exclusive Or_
```

<br/>

## Aritméticos

Operadores útiles para realizar operaciones aritméticas con valores.

```{list-table} Operadores Aritméticos
:header-rows: 1
:name: operadores-aritmeticos

* - Operador
  - Descripción
* - `*`
  - Multiplicación
* - `+`
  - Suma
* - `-`
  - Resta
* - `/`
  - División*
* - `%`
  - Módulo
```

:::{warning}
En una división, si los números son enteros, el resultado será un entero. Para hacer divisiones de enteros y que devuelva decimales se necesita agregar un _.0_ al menos a un número.
:::

<br/>

## Membresía

Operadores útiles para verificar si los valores están dentro de un conjunto de valores, rangos o cumplen un patrón.

```{list-table} Operadores de Membresía
:header-rows: 1
:name: operadores-membresia

* - Operador
  - Descripción
* - `IN`
  - Especificar múltiples posibles valores
* - `BETWEEN`
  - Que el valor esté entre un rango.
* - `LIKE`
  - Para buscar patrones en cadenas.
```
Para más información revisar:
- {ref}`dql-filtrado-in`.
- {ref}`dql-filtrado-between`.
- {ref}`dql-filtrado-like`.

<br/>

## Lógicos

Operadores útiles para manipular valores _booleanos_.

```{list-table} Operadores Lógicos
:header-rows: 1
:name: operadores-logicos

* - Operador
  - Descripción
* - `AND`
  - Retorna `TRUE` si todos los valores son `TRUE`, si al menos uno es `FALSE` retorna `FALSE`.
* - `OR`
  - Retorna `TRUE` si al menos uno de los valores es `TRUE`, retorna `FALSE` si todos son `FALSE`.
* - `NOT`
  - Niega el valor/rango.
* - `ALL`
  - `TRUE` si todos los valores de un _subquery_ cumple una condición.
* - `ANY`
  - `TRUE` si al menos uno de los valores de un _subquery_ cumple una condición.
* - `EXISTS`
  - `TRUE` si el _subquery_ retorna al menos un registro.
* - `SOME`
  - `TRUE` si cualquiera de los valores de un _subquery_ cumple una condición.
```
Para más información consultar:
- {ref}`subquery-all` y {ref}`subquery-any`
- {ref}`subquery-exists`

<br/>

## Concatenar

Para concatenar cadenas se puede hacer uso de los operadores `||`.

```sql
-- Concatenar los valores de dos columnas
col1 || ' ' || col2
``` 
- En este ejemplo se concatena el contenido de _col1_ y _col2_, separados por un espacio.
- En lugar de columnas se pueden concatenar otras cadenas.

:::{warning} No se puede concatenar valores `NULL`, si se utiliza un `NULL` convertirá toda la cadena en `NULL`.
:::

:::{tip} También se puede usar la función `CONCAT()`.
:::

<br/>

**Ejemplo**:

```sql
-- Concatenación de columnas y cadenas
SELECT surname || ', ' || firstname
FROM cd.members;
```
- [Concatenación de columnas y cadenas](https://pgexercises.com/questions/string/concat.html): En este ejemplo se concatenan los valores de dos columnas, junto con una cadena que representa una coma y un espacio en blanco.
