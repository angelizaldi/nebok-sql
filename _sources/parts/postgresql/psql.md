# psql

_psql_ permite de manera interactiva y por medio de comandos acceder y manipular bases de datos de PostgreSQL desde la terminal. Existen dos formas de utilizar _psql_:
- Se puede conectar con una base de datos y poder ejecutar queries desde la terminal o desde archivos de manera interactiva, permite además ejecutar comandos de _psql_. Se puede identificar que se está conectado a una base de datos desde _psql_ porque en la terminal se mostrará el nombre de la base de datos a la que se está conectado de una de las siguientes maneras: <br/> `dbname=#` <br/> `dbname=>`
- Se pueden correr queries y comandos sin necesidad de conectarse de manera interactiva, para ello se deben especificar opciones como `-c` o `-f` al momento de llamar a _psql_.

```shell
# Uso de psql
psql [OPTION]... [DBNAME [USERNAME]]
```
- **Opciones de conexión**: Especifican cómo realizar la conexión con la base de datos.
    - _-h, --host=HOSTNAME_: Directorio de _socket_ o _host_ del servidor de la base de datos (default: "local socket").
    - _-p, --port=PORT_: Puerto del servidor de la base de datos.
    - _-U, --username=USERNAME_: Nombre de usuario con el cual conectarse a la base de datos. No es estrictamente necesario especificar esta opción. Tanto la base de datos como el nombre de usuario se pueden especificar directamente después de las opciones.
    - _-w, --no-password_: Indica que nunca se debe solicitar la contraseña.
    - _-W, --password_: Indica que se debe de solicitar la contraseña.
- **Opciones generales**:
    - _-c, --command=COMMAND_ : Ejecuta un único commando (interno o de SQL) y se sale. El comando o _query_ se debe de poner entre comillas simples: <br/> `psql -c '\x' -c 'SELECT * FROM foo;'`
    - _-d, --dbname=DBNAME_: Nombre de la base de datos a conectar. No es estrictamente necesario especificar esta opción. Tanto la base de datos como el nombre de usuario se pueden especificar directamente después de las opciones.
    - _-f, --file=FILENAME_: Ejecuta los comandos en el archivo y se sale.
    - _-l, --list_: Enlista las base datos y se sale.
    - _-v, --set=, --variable=NAME=VALUE_: Estable la variable psql _NAME_ a _VALUE_, ejm: <br/> `-v ON_ERROR_STOP=1`
    - _-V, --version_: Imprime la version de psql y se sale.
    - -?, --help: Muesta ayuda sobre psql y las opciones del comando. Equivale a usar `--help=options`.
        - `--help=commands`: Enlista los comandos del tipo _/command_ (meta comandos) y se sale.
        - `--help=variables`: Enlista las variables especiales y se sale.
- **Opciones de _IO_**: Opciones para _input_/_output_.
    - _-a, --echo-all_: Imprime todas las entradas del _script_.
    - _-b, --echo-errors_: Imprime todos los comandos de SQL fallidos.
    - _-e, --echo-queries_: Imprime los comandos mandados al servidor.
    - _-E, --echo-hidden_: Muestra los queries que los comandos internos generan.
    - _-L, --log-file=FILENAME_: Almacena el _log_ de la sesión en un archivo.
    - _-n, --no-readline_: Deshábilita la edición de línea mejorada (readline).
    - _-o, --output=FILENAME_: Almacena los resultados de un _query_ en un archivo.
    - _-q, --quiet_: Ejecuta los comandos de manera silencionsa, es decir, no muestra ningún mensaje salvo los resultados del _query_.
    - _-s, --single-step_: Modo paso por paso, es necesario confirmar cada _query_.
    - _-S, --single-line_: Modo de una sola línea. Un salto de línea finaliza el comando de SQL.
- **Opciones de formato para _outputs_**:
    - _-A, --no-align_: Modo de no alineación del resultado.
    - _--csv_: Modo de resultado como _csv_.
    - _-F, --field-separator=STRINGfield_: Indica el separador de valores para resultados no alineados (default: "|").
    - _-H, --html_: Modo de resultado como HTML.
    - _-P, --pset=VAR\[=ARG]_: Establece la opción de impression _VAR_ al valor _ARG_ (ver el comando `\pset`).
    - _-R, --record-separator=STRING_: Indica el separador de líneas para resultados no alineados (default: _newline_).
    - _-t, --tuples-only_: Imprime únicame las filas.
    - _-T, --table-attr=TEXT_: Establece valores de los atributos del _tag_ `<table/>` de HTML.
    - _-x, --expanded_: Indica que el resultado debe ser de tipo expandido.
    - _-z, --field-separator-zero_: Establece el separador de valores para resultados no alineados a cero bytes.
    - _-0, --record-separator-zero_: Establece el separador de filas para resultados no alineados a cero bytes.

**Ejemplos**

```shell
# Conexión a una base de datos de manera interactiva
psql -d database_name -U username # Equivale a: psql database_name username

# Ejecutar un query desde un archivo, almacenar el resultado en un csv y sale de psql
psql -U username -d database_name -f query.sql --csv -o output.csv
```
- Considerar que el directorio que se toma es desde el cual se ejecutó _psql_ desde la terminal.

```{note}
Mientras estés en la terminal para poder volver a escribir en ella presiona la tecla _Q_.
```

<br/>

---
## Comandos

Los comandos permiten administrar y hacer _scripting_ desde _psql_, para poder ejecutarlos es necesario utilizarlos adentro de _psql_ de manera interactiva o con la opción `--command=COMMAND`. 

### Argumentos

Muchos comandos reciben argumentos, tener en cuenta las siguientes características al momento de definir los argumentos:

- **Espacios** en blanco: En caso de que el argumento tenga espacios en blanco se debe de poner el argumento entre comillas simples: `'with blanks'`.
- **Variable**: Se pueden usar valores de variables como argumentos, para ello se debe de poner el nombre de la variable precedido por dos puntos: <br/> `:var_name` <br/> `:'var_name'` <br/> `:"var_name"` <br/> `:{?var_name}` <br/> Para más información revisar {ref}`psql-variables`.
- **Comandos de _shell_**: Se pueden utilizar comandos que se pasan a _shell_, se ejecutan en _shell_ y cuyos resultados se retornan en formato de texto, para ello se debe de poner el comando entre _backsticks_: <br/> \`shell command\`. <br/> Dentro del comando es posibles utilizar valores de variables usando `:var_name` o `:'var_name'`.

<br/>

### Ayuda

Estos comandos retornan ayuda sobre _psql_, los comandos, opciones, variables y SQL.

| **Comando**               | **Descripción**                                                                                      |
|---------------------------|------------------------------------------------------------------------------------------------------|
| `\? [commands]`            | Muestra ayuda sobre los comandos con barra invertida.                                               |
| `\? options`               | Muestra ayuda sobre las opciones de línea del comando `psql`.                                       |
| `\? variables`             | Muestra ayuda sobre las variables especiales.                                                       |
| `\h [NAME]`                | Muestra ayuda sobre la sintaxis de comandos SQL, utilizar `*` para todos los comandos.              |

<br/>

### Conexión

Comandos relaciones con la conexión a la base de datos.

| **Comando**               | **Descripción**                                                                                     |
|---------------------------|-----------------------------------------------------------------------------------------------------|
| `\c\[onnect\] \{\[DBNAME\|- USER\|- HOST\|- PORT\|-\] \| conninfo\}` | Se conecta a una nueva base de datos (actualmente desconectada).|
| `\conninfo`              | Muestra información sobre la conexión actual.                                                        |
| `\encoding [ENCODING]`   | Muestra o establece la codificación del cliente.                                                     |
| `\password [USERNAME]`   | Cambia de forma segura la contraseña de un usuario.                                                  |

<br/>

### Formato

Comandos para controlar el formato de salida, en operaciones _output_.

| **Comando**               | **Descripción**                                                                                      |
|---------------------------|------------------------------------------------------------------------------------------------------|
| `\a`                       | Alterna entre el modo de salida alineado y no alineado.                                             |
| `\C [STRING]`              | Establece el título de la tabla o lo desactiva si no hay ninguno.                                   |
| `\f [STRING]`              | Muestra o establece el separador de campo para la salida de consulta no alineada.                   |
| `\pset [NAME [VALUE]]`     | Establece opciones de salida de tabla.                                                              |
| `\T [STRING]`              | Establece los atributos de la etiqueta `<table>` de HTML o los desactiva si no hay ninguno.         |


<br/>

### General

Comandos de caracter general.

| **Comando**               | **Descripción**                                                                                      |
|---------------------------|------------------------------------------------------------------------------------------------------|
| `\bind [PARAM]...`         | Establece los parámetros de los _query_.                                                            |
| `\copyright`               | Muestra los términos de uso y distribución de PostgreSQL.                                           |
| `\crosstabview [COLUMNS]`  | Ejecuta una consulta y muestra el resultado en un _crosstab_.                                       |
| `\errverbose`              | Muestra el mensaje de error más reciente con la máxima verbosidad.                                  |
| `\g [(OPTIONS)] [FILE]`    | Ejecuta la consulta (y envía el resultado a un archivo o _pipe_ (`\|`).                              |
| `\gdesc`                   | Describe el resultado de la consulta, sin ejecutarla.                                               |
| `\gexec`                   | Ejecuta la consulta y luego ejecuta cada valor en su resultado.                                     |
| `\gset [PREFIX]`           | Ejecuta la consulta y almacena el resultado en variables de `psql`.                                 |
| `\gx [(OPTIONS)] [FILE]`   | Igual que `\g`, pero fuerza el modo de salida expandida.                                            |
| `\q`                       | Sale de `psql`.                                                                                     |
| `\watch [[i=]SEC] [c=N]`   | Ejecuta la consulta cada _SEC_ segundos, hasta _N_ veces.                                           |

<br/>

### Información

Estos comandos retornan información sobre la base de datos y sus objetos.

| **Comando**               | **Descripción**                                                                                      |
|---------------------------|------------------------------------------------------------------------------------------------------|
| `\d[S+]`                  | Enlista tablas, _views_ y secuencias.                                                                |
| `\d[S+]  NAME`            | Describe una tabla, _view_, secuencia o índice.                                                      |
| `\da[S]  [PATTERN]`       | Enlista funciones agregadas.                                                                         |
| `\dA[+]  [PATTERN]`       | Enlista métodos de acceso.                                                                           |
| `\dAc[+] [AMPTRN [TYPEPTRN]]`  | Enlista clases de operadores.                                                                   |
| `\dAf[+] [AMPTRN [TYPEPTRN]]`  | Enlista familias de operadores.                                                                 |
| `\dAo[+] [AMPTRN [OPFPTRN]]`   | Enlista operadores de las familias de operadores.                                               |
| `\dAp[+] [AMPTRN [OPFPTRN]]`   | Enlista funciones de soporte de las familias de operadores.                                     |
| `\db[+]  [PATTERN]`       | Enlista _tablespaces_.                                                                               |
| `\dc[S+] [PATTERN]`       | Enlista conversiones.                                                                                |
| `\dconfig[+] [PATTERN]`   | Enlista parámetros de configuración.                                                                 |
| `\dC[+]  [PATTERN]`       | Enlista _casts_.                                                                                     |
| `\dd[S]  [PATTERN]`       | Muestra descripciones de objetos no mostrados en otro lugar.                                         |
| `\dD[S+] [PATTERN]`       | Enlista dominios.                                                                                    |
| `\ddp    [PATTERN]`       | Enlista privilegios por defecto.                                                                     |
| `\dE[S+] [PATTERN]`       | Enlista tablas externas.                                                                             |
| `\des[+] [PATTERN]`       | Enlista servidores externos.                                                                         |
| `\det[+] [PATTERN]`       | Enlista tablas externas.                                                                             |
| `\deu[+] [PATTERN]`       | Enlista mapeos de usuarios.                                                                          |
| `\dew[+] [PATTERN]`       | Enlista _wrappers_ de datos externos.                                                                |
| `\df[anptw][S+] [FUNCPTRN [TYPEPTRN ...]]` | Enlista funciones [solo agregadas/normales/procedimientos/_triggers_/_window_].     |
| `\dF[+]  [PATTERN]`       | Enlista configuraciones de búsqueda de texto (_text search_).                                        |
| `\dFd[+] [PATTERN]`       | Enlista diccionarios de búsqueda de texto (_text search_).                                           |
| `\dFp[+] [PATTERN]`       | Enlista analizadores de búsqueda de texto (_text search_).                                           |
| `\dFt[+] [PATTERN]`       | Enlista plantillas de búsqueda de texto (_text search_).                                             |
| `\dg[S+] [PATTERN]`       | Enlista roles.                                                                                       |
| `\di[S+] [PATTERN]`       | Enlista índices.                                                                                     |
| `\dl[+]`                  | Enlista objetos grandes, igual que `\lo_list`.                                                       |
| `\dL[S+] [PATTERN]`       | Enlista lenguajes procedurales.                                                                      |
| `\dm[S+] [PATTERN]`       | Enlista _views_ materializadas.                                                                      |
| `\dn[S+] [PATTERN]`       | Enlista esquemas.                                                                                    |
| `\do[S+] [OPPTRN [TYPEPTRN [TYPEPTRN]]]` | Enlista operadores.                                                                   |
| `\dO[S+] [PATTERN]`       | Enlista _collations_.                                                                                |
| `\dp[S]  [PATTERN]`       | Enlista privilegios de acceso a tablas, _views_ y secuencias.                                        |
| `\dP[itn+] [PATTERN]`     | Enlista relaciones particionadas [solo índices/tablas] [n=nested].                                   |
| `\drds [ROLEPTRN [DBPTRN]]` | Enlista configuraciones de roles por base de datos.                                                |
| `\drg[S] [PATTERN]`       | Enlista permisos de roles.                                                                           |
| `\dRp[+] [PATTERN]`       | Enlista publicaciones de replicación.                                                                |
| `\dRs[+] [PATTERN]`       | Enlista suscripciones de replicación.                                                                |
| `\ds[S+] [PATTERN]`       | Enlista secuencias.                                                                                  |
| `\dt[S+] [PATTERN]`       | Enlista tablas.                                                                                      |
| `\dT[S+] [PATTERN]`       | Enlista tipos de datos.                                                                              |
| `\du[S+] [PATTERN]`       | Enlista roles.                                                                                       |
| `\dv[S+] [PATTERN]`       | Enlista _views_.                                                                                     |
| `\dx[+]  [PATTERN]`       | Enlista extensiones.                                                                                 |
| `\dX     [PATTERN]`       | Enlista estadísticas extendidas.                                                                     |
| `\dy[+]  [PATTERN]`       | Enlista _triggers_ de eventos.                                                                       |
| `\l[+]   [PATTERN]`       | Enlista bases de datos.                                                                              |
| `\sf[+]  FUNCNAME`        | Muestra la definición de una función.                                                                |
| `\sv[+]  VIEWNAME`        | Muestra la definición de una _view_.                                                                 |
| `\z[S]   [PATTERN]`       | Igual que `\dp` (enlista privilegios de acceso a tablas, _views_ y secuencias.).   |


<br/>

### Input/Output

Comandos relacionados con operaciones de entrada y salida.

| **Comando**               | **Descripción**                                                                                      |
|---------------------------|------------------------------------------------------------------------------------------------------|
| `\copy ...`                | Realiza la copia de SQL con un flujo de datos al _host_ del cliente.                                |
| `\echo [-n] [STRING]`      | Escribe una cadena en la salida estándar (usar _-n_ para no agregar nueva línea).                   |
| `\i FILE`                  | Ejecuta comandos desde un archivo.                                                                  |
| `\ir FILE`                 | Igual que `\i`, pero relativo a la ubicación del script actual.                                     |
| `\o [FILE]`                | Envía todos los resultados de consultas a un archivo o _pie_ (`|`).                                 |
| `\qecho [-n] [STRING]`     | Escribe una cadena en el flujo de salida `\o` (usar _-n_ para no agregar nueva línea).              |
| `\warn [-n] [STRING]`      | Escribe una cadena en la salida de error estándar (usar _-n_ para no agregar nueva línea).          |

<br/>

### Objetos grandes

Comandos relacionados con objetos grandes.

| **Comando**               | **Descripción**                                                                                      |
|---------------------------|------------------------------------------------------------------------------------------------------|
| `\lo_export LOBOID FILE`   | Escribe un objeto grande a un archivo.                                                              |
| `\lo_import FILE [COMMENT]` | Lee un objeto grande desde un archivo.                                                             |
| `\lo_list[+]`              | Enlista objetos grandes.                                                                            |
| `\lo_unlink LOBOID`        | Elimina un objeto grande.                                                                           |


<br/>

### Query Buffer

Comandos relacionados con el búfer de consultas.

| **Comando**               | **Descripción**                                                                                      |
|---------------------------|------------------------------------------------------------------------------------------------------|
| `\e [FILE] [LINE]`         | Edita el búfer de consultas (o archivo) con un editor externo.                                      |
| `\ef [FUNCNAME [LINE]]`    | Edita la definición de una función con un editor externo.                                           |
| `\ev [VIEWNAME [LINE]]`    | Edita la definición de una _view_ con un editor externo.                                            |
| `\p`                       | Muestra el contenido del búfer de consultas.                                                        |
| `\r`                       | Restablece (limpia) el búfer de consultas.                                                          |
| `\s [FILE]`                | Muestra el historial o lo guarda en un archivo.                                                     |
| `\w FILE`                  | Escribe el búfer de consultas en un archivo.                                                        |

:::{note}
Al ejecutar comandos como `\e`, `\ef` o `\ev` tener en cuenta lo siguiente
- Se abrirá un editor donde se puede escribir el _query_, función o _view_, por default ser abrirá _vim_, es posible modificar el editor con las {ref}`psql-variables-entorno`.
- En _vim_, una vez que se haya escrito el _query_, presionar _esc_ y escribir `:wq`, automáticamente se cerrará el editor y se ejecutará el _query_.
:::

<br/>

### Sentencias condicionales

Comandos para poder ejecutar sentencias condicionales.

| **Comando**               | **Descripción**                                                                                      |
|---------------------------|------------------------------------------------------------------------------------------------------|
| `\if EXPR`                 | Inicia un bloque condicional.                                                                       |
| `\elif EXPR`               | Alternativa dentro del bloque condicional actual.                                                   |
| `\else`                    | Alternativa final dentro del bloque condicional actual.                                             |
| `\endif`                   | Finaliza el bloque condicional.                                                                     |

<br/>

### Sistema operativo

Comandos que interactúan con el sistema operativo de la computadora.

| **Comando**               | **Descripción**                                                                                      |
|---------------------------|------------------------------------------------------------------------------------------------------|
| `\cd [DIR]`               | Cambia el directorio de trabajo actual.                                                              |
| `\getenv PSQLVAR ENVVAR`  | Obtiene una variable de entorno.                                                                     |
| `\setenv NAME [VALUE]`    | Establece o desactiva una variable de entorno.                                                       |
| `\! [COMMAND]`            | Ejecuta un comando _shell_ en el sistema o inicia un _shell_ interactivo.                            |

<br/>

### Variables

Comandos relacionados con variables.

| **Comando**               | **Descripción**                                                                                      |
|---------------------------|------------------------------------------------------------------------------------------------------|
| `\prompt [TEXT] NAME`      | Solicita al usuario que establezca una variable interna.                                            |
| `\set [NAME [VALUE]]`      | Establece una variable interna, o las lista todas si no se pasan parámetros.                        |
| `\unset NAME`              | Desactiva (elimina) una variable interna.                                                           |

<br/>

---
(psql-variables)=
## Variables

_psql_ provee de variables que son pares de _nombre-valor_ que permiten almacenar cualquier cadena de cualquier longitud. Los nombres de las variables deben de contener únicamente caracteres alfanuméricos o guión bajo. 

Se puede establecer el valor de una variable ya sea con el comando `\set` una vez que ya se está dentro de _psql_ o con la opción `--set` al momento de ejecutar _psql_. Para desestablecer una variable se puede usar el comando `\unset`:
```shell
# Establecer como opción: Establecer la variable foo con el valor bar
psql --set=FOO=BAR

# Establecer como comando: Establecer la variable foo con el valor bar
dbname=> \set foo bar

# Desestablecer como comando: Desestablecer la variable foo
dbname=> \unset foo
```

Para recuperar el valor de una variable se deben de usar dos puntos antes del nombre de la variable:
```shell
# Imprimir el valor de la variable foo
dbname=> \echo :foo # imprime 'bar'
```

<br/>

### Variables de psql

Las variables especiales de _psql_ son variables que forman parte de manera nativa en _psql_, estas variables generalmente no se pueden desestablecer ni establecer con valores inválidos. Todas las variables especiales en _psql_ tienen nombres en mayúsculas. A continuación se enlistan algunas de las variables más importantes:

:::{warning}
Esta no es una lista extensiva de las variables disponibles en _psql_. Para más información consultar la [documnetación](https://www.postgresql.org/docs/17/app-psql.html) de _psql_.
:::

```{list-table}
:header-rows: 1

* - Variable
  - Tipo de valor
  - Descripción
* - `AUTOCOMMIT`
  - {on, off}
  - Controla si las sentencias de SQL se "consolidan" (_commit_) automáticamente.  
* - `DBNAME`
  - _any_
  - Nombre de la base de datos a la que se está conectado actualmente.
* - `ECHO`
  - {all, errors, none, queries}
  - Indica qué tipo de información se imprimen de manera estándar.
* - `ENCODING`
  - _any_
  - Indica el conjunto de caracteres de codificación actual.
* - `ERROR`
  - _boolean_
  - Indica si el último _SQL query_ fue un error (_true_) o no (_false_).
* - `FETCH_COUNT`
  - _numeric_
  - Indica el número máximo de filas a mostrar en el _result set_ (0 = ilimitado).
* - `HISTFILE`
  - _any_
  - Define el nombre y la ubicación del archivo donde se guarda el historial de comandos.
* - `HISTSIZE`
  - _numeric_
  - Determina el número de comandos que se guardan en el historial (default 500).
* - `HOST`
  - _any_
  - _Host_ del servidor al que se está conectado actualmente.
* - `ON_ERROR_ROLLBACK`
  - {on, off}
  - Controla si se debe hacer `ROLLBACK` automáticamente al encontrar un error en una transacción.
* - `ON_ERROR_STOP`
  - {on, off}
  - Detiene la ejecución de scripts al encontrar un error.
* - `PORT`
  - _any_
  - _Puerto_ del servidor al que se está conectado actualmente.
* - `QUIET`
  - {on, off}
  - Suprime las salidas de los comandos, útil para ejecutar scripts en silencio.
* - `ROW_COUNT`
  - _numeric_
  - Indica el número de filas retornadas o afectadas por el último _SQL query_.
* - `SINGLELINE`
  - {on, off}
  - Controla si psql acepta solo una línea de entrada (igual que la opción `-S`).
* - `SINGLESTEP`
  - {on, off}
  - Pide confirmación antes de ejecutar cada comando (igual que la opción `-s`).
* - `USER`
  - _any_
  - Usuario del servidor desde el que se está conectado actualmente.
* - `VERBOSITY`
  - [default, verbose, terse, sqlstate]
  - Controla la cantidad de información que se muestra en los mensajes de error.
```

<br/>

### Variables de display

Son variables que controlan el formato de las tablas de resultados de _queries_ de SQL. Estas variables también se pueden establecer con el comando `\pset`.

:::{warning}
Esta no es una lista extensiva de las variables disponibles en _psql_. Para más información consultar la [documnetación](https://www.postgresql.org/docs/17/app-psql.html#APP-PSQL-META-COMMAND-PSET) de _psql_.
:::

:::{caution}
En la tabla si el tipo de valor es _string_ se debe de poner entre comillas simples
:::

```{list-table}
:header-rows: 1

* - Variable
  - Valor
  - Descripción
* - `border`
  - _numeric_
  - Estilo del borde. 
* - `columns`
  - _numeric_
  - Ancho de las columnas en el formato _wrapped_.
* - `csv_fieldsep`
  - _string_
  - Separador a usar para formatos _csv_ (default `','`).
* - `fieldsep`
  - _string_
  - Separador de campos a usar para el formato no alineado (default `'|'`).
* - `format`
  - \[aligned, asciidoc, csv, html, latex, latex-longtable, troff-ms, unaligned, wrapped]
  - Formato del _output_.
* - `linestyle`
  - \[ascii, old-ascii, unicode]
  - Estilo de línea del borde.
* - `null`
  - _string_
  - Cadena a usar en lugar de valores _null_ (default es una cadena vacía).
* - `recordsep`
  - _string_
  - Separador de registros (saltos de línea) en el formato no alineado.
* - `tableattr`
  - _string_
  - Especifica atributos del _tag_ `<table/>` de HTML para formato HTML.
* - `title`
  - _string_
  - Título de tabla para tablas subsecuentes.
```

<br/>

(psql-variables-entorno)=
### Variables de entorno

Las variables de entorno son variables a nivel de sistema que determinan como _psql_ y PostgreSQL funcionan. Estas variables se utilizan para determinar la conexión por default de _psql_ por lo que es útil personalizarlas. Para establecer el valor de una variable de entorno usar `export` (fuera de _psql_):

```shell
# Establecer variable de entorno (ejemplo con PGUSER)
export PGUSER=username
```
- Algunas de las variables de entorno útiles son `PGDATABASE`, `PGHOST`, `PGPORT` y `PGUSER` que representan el nombre de una base de datos, el _host_, el puerto y nombre de usuario por default que utilizará _psql_ para conectarse al servidor.

```{list-table}
:header-rows: 1

* - Variable
  - Tipo de valor
  - Descripción
* - `PGDATABASE`
  - _any_
  - Parámetro de conexión del nombre de la base de datos (_dbname_).
* - `PGHOST`
  - _any_
  - Parámetro de conexión del _host_.
* - `PGPASSFILE`
  - _any_
  - Nombre del archivo de contraseña.
* - `PGPASSWORD`
  - _any_
  - Parámetro de conexión la contraseña (no recomendado).
* - `PGPORT`
  - _any_
  - Parámetro de conexión del _port_.
* - `USER`
  - _any_
  - Parámetro de conexión del _user_.
* - `SQL_EDITOR, EDITOR, VISUAL`
  - _any_
  - Editor usado por los comandos `\e`, `\ef` y `\ev` respectivamente.
* - `SHELL`
  - _any_
  - _Shell_ usado por el comando `\!`.
* - `TMPDIR`
  - _any_
  - Directorio para los archivos temporales.
```