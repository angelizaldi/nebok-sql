# Otorgar

En esta sección se revisan las sentencias relaciones con otorgar privilegios.

## GRANT

Se utiliza para conceder privilegios en objetos específicos a uno o más usuarios.

:::{warning}
En esta sección no se cubren todos los detalles de la sentencia `GRANT`, para más información consultar la [documentación](https://www.postgresql.org/docs/current/sql-grant.html) de PostgreSQL.
:::

```sql
-- Otorgar privilegios
GRANT privilage(s)
ON object_name
TO role_name
```
- _privilege_: El privilegio que se otorgará. Si son más de uno, separar por comas. Si se quiere conceder todos los privilegios se puede usar `ALL PRIVILAGES`. Los privilegios son:
    - `SELECT`.
    - `INSERT`.
    - `UPDATE `.
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

<br/>

### Conceder acceso a un schema

Para conceder acceso a un _schema_ a un usuario o grupo.

```sql
-- Otorgar acceso a un schema
GRANT USAGE 
ON SCHEMA schema_name
TO role_name;
```
- _schema_name_ es el nombre del _schema_.
- _role_name_: Un usuario o un grupo de usuarios. Para decir todos los usuarios usar `PUBLIC`.

<br/>

### Conceder privilegios en un schema

Para conceder privilegios a un grupo o usuario en un schema.
```sql
GRANT privilage1, privilage2, ... 
ON ALL TABLES IN SCHEMA schema_name 
TO role_name;
```
- _schema_name_ es el nombre del _schema_.
- _role_name_: Un usuario o un grupo de usuarios. Para decir todos los usuarios usar `PUBLIC`.
- _privilege_: El privilegio que se otorgará. Si son más de uno, separar por comas. Si se quiere conceder todos los privilegios se puede usar `ALL PRIVILAGES`. Los privilegios son:
    - `SELECT`.
    - `INSERT`.
    - `UPDATE `.
    - `DELETE`.
    - `TRUNCATE`.
    - `REFERENCES`.
    - `TRIGGER`.
    - `CREATE`.
    - `CONNECT`.
    - `TEMPORARY`.
    - `EXECUTE`.
    - `USAGE`. 
