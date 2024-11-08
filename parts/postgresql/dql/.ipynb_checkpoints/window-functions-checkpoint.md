# Window Functions

Son funciones que realizan un cálculo en una "ventana" de datos. Algunos usos son:
- Extraer valores anteriores o posteriores a una fila en una misma columna (_fetching_).
- Crear rankings.
- Calcular promedio móviles, cálculos acumulados, diferencias y cambios porcentuales, entre otros.

```{important}
Las _window functions_ funcionan como si fueran un proceso iterativo que va aplicando cálculos con base a filas de manera "iterativa". En ese contexto la fila actual o `CURRENT ROW` es la fila en la que se está realizando el cálculo en cada "iteración".
```

## Cómo se usan

Se utilizan junto a la función `OVER()`, la cual permite pasar una _aggregate function_ o _window function_ a cada conjunto de filas. Cada función realizará un cálculo en cada ventana de datos especificados en `OVER()`:
```{code-block} sql
-- Plantilla básica del uso de window functions
window_function(arg1, arg2, ...) OVER(
    [PARTITION BY partition_expression]
    [ORDER BY sort_rule [ASC|DESC] [NULL {FIRST|LAST}]
    ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW)  
```
- _window_function_ es el nombre de una {ref}`funciones-sliding`, también pueden ser un _aggregate function_.
- `PARTITION BY` es similar a `GROUP BY`, permite aplicar la _window function_ para los valores distintos de una columna o la combinación de valores distintos de dos o más columnas de cada conjunto de ventanas.
    - Si no se especifica la _partition_expression_ se aplicará a todo el conjunto de filas como una única partición.
    - Para que sea con base a más de una columna solo separar con coma las columnas.
- `ORDER BY` es para especificar el orden de las filas de cada partición en la que la _window\_fuction_ será aplicada.
    - Dentro de `ORDER BY` se pueden usar _aggregate functions_.
    - Se puede indicar si los valores `NULL` se deben colocar al principio o al final. `NULL LAST` es la opción por default.
    - Se puede ordenar con base a más de una columna, solo separar por coma e indicar `ASC` o `DESC` según sea el caso.
- Por default se harán los cálculos desde la primer fila hasta la fila actual (`CURRENT ROW`) cuando se usa `ORDER BY`. En caso contrario los valores por default son `ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING`. Para otras opciones revisar {ref}`funciones-sliding`.
- Si no se especifica nada dentro de `OVER()` entonces la ventana de datos será todo el conjunto de resultados. 
- **IMPORTANTE**: La función `OVER()` se debe de poner siempre, no importa que no se especifiquen los elementos internos.
- **IMPORTANTE**: En lugar de poner `ROWS BETWEEN` se puede poner `RANGE BETWEEN` la diferencia, es que en la cláusula `ORDER BY` si hay valores repetidos, estos se considerarán como un rango y por lo tanto todos esos valores repetidos tendrán el mismo valor, pero al final se llega al mismo resultado. Por ejemplo en una suma acumulada:

| Year | Medals | Rows_RT | Range_RT |
|----|-----|----|----|
| 1992 | 10 | 10 | 10 |
| 1996 | 50 | 60 | 110 |
| 2000 | 50 | 110 |110 |
| 2004 | 60 | 170 | 230 |
| 2008 | 60 | 230 | 230 |
| 2012 | 70 | 300 | 300 |

```{attention}
Algunas _aggregate functions_ pueden ser usadas como _window function_, tal es el caso de `AVG()`, `SUM()`, `COUNT()`, `MAX()` Y `MIN()`. En este caso los cálculos, a menos de que se especifique otra cosa, serán acumulados.
```

<br/>

**Ejemplos**:

```sql
-- OVER sin argumentos
SELECT COUNT(*) OVER(), firstname, surname
FROM cd.members
ORDER BY joindate

-- Uso de ROW_NUMBER para enumeras filas
SELECT ROW_NUMBER() OVER(ORDER BY joindate), firstname, surname
FROM cd.members;

-- Uso de RANK
SELECT facid, total 
FROM (
	SELECT facid, SUM(slots) AS total, RANK() OVER(ORDER BY SUM(slots) desc) AS rank
	FROM cd.bookings
	GROUP BY facid) AS ranked
WHERE rank = 1  
```
- [OVER sin argumentos](https://pgexercises.com/questions/aggregates/countmembers.html): En este ejemplo se imprimen los miembros y el número total de miembros en cada fila.
- [Uso de ROW_NUMBER](https://pgexercises.com/questions/aggregates/nummembers.html): En este ejemplo se enumera a cada miembro.
- [Uso de RANK](https://pgexercises.com/questions/aggregates/nummembers.html): En este ejemplo se usa un _subquery_ para determinar el ranking de la suma de los _slots_ para cada _facid_ y se usa el resultado para determinar el _facid_ con el mayor número de _slots_.