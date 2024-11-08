# Full text search

Es un método más eficiente para realizar queries en tipos de datos de texto. Es particularmente útil si los valores de una columna son cadenas bastante grandes. Básicamente convierte esos textos en vectores de palabras y realiza la búsqueda, incluso más eficiente que usar _REGEX_ y más flexible al poder buscar palabras en diferentes tiempos verbales, con errores gramáticas, singulares y plurales, entre otros.
```sql
-- Uso básico de full text search
WHERE to_tsvector(['text',] exp) @@ tsquery('word')
```
- Equivaldría a un `WHERE COL_NAME LIKE '%EXP%'`, pero no es sensible de mayúsculas/minúsculas y además retorna también resultados de palabras normalizadas (plurales, en otros tiempos verbales, etc.) y posibles errores gramáticales.
- `to_tsvector()` es una función que crea una lista de _tokens_. Palabras con poco significado se omitirán como artículos (_the_) o conjugaciones (_and_, _or_). Cada palabra tendrá un apuntador (posición en el documento) y las palabras se convertirán a un lexema normalizado, es decir que palabras como _'jumped'_ se convierten a _'jump'_.
- Se puede especificar un idioma en particular, para ello poner al inicio de `to_tsvector()` el idioma entre comillas simples y en inglés.
- _exp_ - `TEXT`: es una expresión de cadena, también pueden ser columnas de tablas que sean cadenas.
- `tsquery()` es una función que busca en un vector las ocurrencias de determinadas palabras o frases.
- _words_ es la palabra que se buscará, preferentemente debe estar es su forma normalizada (en singular y presente). Se pueden usar algunos operadores para utilizar más de una palabra:
    - `&`: And.
    - `|`: Or.
    - `!`: Not.
    - `<->`: Para buscar palabras que estén juntas por ejemplo `'jump <-> quick'`
    - `<N>`: Para buscar palabras separadas por _N-1_ palabras, por ejemplo `'jump <2> quick'`, buscará palabras que estén a una palabra de separación
