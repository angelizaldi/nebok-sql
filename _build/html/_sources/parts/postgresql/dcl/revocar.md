# Revocar

En esta sección se revisan las sentencias relaciones con revocar privilegios.

## REVOKE

Se utiliza para revocar privilegios en objetos específicos a uno o más usuarios.

:::{warning}
En esta sección no se cubren todos los detalles de la sentencia `REVOKE`, para más información consultar la [documentación](https://www.postgresql.org/docs/current/sql-revoke.html) de PostgreSQL.
:::

```sql
-- Revocar privilegios
REVOKE privilage(s)
ON object
FROM role
```
- _privilege_: El privilegio que se revocará. Si son más de uno, separar por comas. Si se quiere revocar todos los privilegios se puede usar `ALL PRIVILAGES`. Los privilegios son:
    - `SELECT` (hacer queries)
    - `INSERT` (agregar registros)
    - `UPDATE `(actualizar registros)
    - `DELETE`.
    - `TRUNCATE`.
    - `REFERENCES`.
    - `TRIGGER`.
    - `CREATE`.
    - `CONNECT`.
    - `TEMPORARY`.
    - `EXECUTE`.
    - `USAGE`. 
- _object_name_: El nombre de una _view_, _table_, _schema_, etc.
- _role_name_: Un usuario o un grupo de usuarios. Para decir todos los usuarios usar `PUBLIC`.