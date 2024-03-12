# Operadores

(operadores-comparacion)=
## Comparación

```{list-table}
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

```{list-table}
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

```{list-table}
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
\* Si los números son enteros, devolverá un entero. Para hacer divisiones de enteros y que devuelva decimales se necesita agregar un _.0_ al menos a un número.

<br/>

## Membresía

```{list-table}
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
Para más información revisa:
- {ref}`IN y BETWEEN`.
- {ref}`LIKE`.

<br/>

## Lógicos:
```{list-table}
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
  - `TRUE` si todos los valores de un subquery cumple una condición.
* - `ANY`
  - `TRUE` si al menos uno de los valores de un subquery cumple una condición.
* - `EXISTS`
  - `TRUE` si el subquery retorna al menos un registro.
* - `SOME`
  - `TRUE` si cualquiera de los valores de un subquery cumple una condición.
```
Para más información consulta:
- {ref}`ANY y ALL`
- {ref}`EXISTS`

<br/>

## Concatenar
Para concatenar cadenas se puede hacer uso de los operadores `||`, por ejemplo: 
```sql
col1 || ' ' || col2
``` 
-	Se concatenará el contenido de _col1_ y _col2_, separados por un espacio. 
-	**IMPORTANTE**: No se puede concatenar valores `NULL`, si se utiliza un `NULL` convertirá toda la cadena en `NULL`.
-	**IMPORTANTE**: También se puede usar la función `CONCAT()`.
