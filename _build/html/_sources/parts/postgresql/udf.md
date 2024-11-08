# Funciones (UDF)

Para crear una función en PostgreSQL usar:
```{code-block} sql
-- Sintaxis basica para crear una función
CREATE FUNCTION function_name(param1 AS type1, ...) RETURN type
LANGUAGE plpgsql AS
$$
	BEGIN
    	-- function body
    	RETURN expression
	END;
$$;
```
-	_function_name_ será el nombre de la función. Con este nombre se puede hacer referencia a la función en los queries.
-	_param1, ..._ serán los nombres de los parámetros de la función.
-	_type1, ..._ serán los tipos de datos de los parámetros o del valor retornado por la función.
-	Los símbolos `$$` son una forma segura de convertir a texto todo lo que esté dentro de ellos. Se podría poner como cadena, pero es mejor utilizar esos símbolos.
-	`BEGIN` y `END` indican el inicio y fin de la función respectivamente.
-	`RETURN` es una palabra reservada para retonar un valor.
-	`plpgsql` es sintaxis.
-	Para llamar a una función se hace desde `SELECT`.

:::{note}
Las funciones pueden recibir como argumento columnas y los calculos se harán sobre todas las filas de las columnas, retornando tablas.
:::

**Ejemplo**: A continuación se muestra un ejemplo sencillo de una función que recibe dos argumentos y retorna la suma de ambos:

```{code-block} sql
-- Definir la función
CREATE FUNCTION sumar(a INT, b INT) RETURNS INT AS
LANGUAGE plpgsql AS 
$$
    BEGIN
        RETURN a + b;
    END;
$$

SELECT add_two_numbers(5, 7); -- Retorna 12
```