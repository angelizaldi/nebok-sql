# Eliminar

En esta sección se revisa la sentencia relacionada con la eliminación de filas en una tabla existente en una base de datos.

## DELETE

:::{warning}
En esta sección no se cubren todos los detalles de la sentencia `DELETE`, para más información consultar la [documentación](https://www.postgresql.org/docs/current/sql-delete.html) de PostgreSQL.
:::

Se utiliza para eliminar registros existentes de una tabla.

```sql
-- Plantilla básica del uso de DELETE
DELETE FROM table_name
[WHERE condición;]
```
- _table_name_ es el nombre de la tabla o tabla temporal.
- Si no se específica `WHERE` se eliminarán todos registros de _table_name_.
    - **IMPORTANTE**: Dependiendo de la clausula `WHERE` se eliminarán uno, varios o todos los registros.
- Cuando los registros se eliminaron de manera exitosa, el _statement_ `DELETE` retorna: <br/> `DELETE count`
    - _count_ es la cantidad de registros exitosamente eliminados.
- Se puede utilizar una clausula especial llamada `RETURNING` para que al momento de eliminar los registros, se retorne información sobre esos registros: <br/> `DELETE FROM table_name WHERE condition RETURNING *`
    - Si se quiere que se retorne los registros eliminados como tal, usar un asterisco.
    - Se puede poner el nombre de alguna columna en específico o varias, separadas por coma.

:::{tip}
Alternativamente se podría usar la sentencia {ref}`ddl-eliminar-truncate-table` para eliminar todas las filas de tabla de manera más rápida.
:::
 
<br/>

**Ejemplos**:

```sql
-- DELETE ALL
DELETE FROM cd.bookings;

-- DELETE simple
DELETE FROM cd.members
WHERE memid = 37;

-- DELETE con subquery
DELETE FROM cd.members
WHERE memid NOT IN (SELECT DISTINCT memid FROM cd.bookings);
```
- [DELETE ALL](https://pgexercises.com/questions/updates/delete.html): En este ejemplo se eliminan todos los registros de la tabla _cd.bookings_.
- [DELETE simple](https://pgexercises.com/questions/updates/deletewh.html): En este ejemplo se elimina un registro específico de la tabla _cd.members_.
- [DELETE con subquery](https://pgexercises.com/questions/updates/deletewh2.html): En este ejemplo se eliminan registros con base al resultado de un _subquery_.