# sql-styles

# Indice
- Resumen
- General
- - Hacer
- - Evitar
- Convenciones de nombres
- - General
- - Tablas
- - Columnas
- - Alias o correlaciones
- - Procedimientos almacenados
- - Sufijos uniformes
- Sintaxis de consultas
- - Palabras reservadas
- - Espacio en blanco
- - Indentación
- - Formalismos preferidos
- Sintaxis CREATE
- - Elección de tipos de datos
- - Especificación de valores predeterminados
- Notas

# General
Este documento está diseñado como una guía para establecer estándares de codificación de T-SQL limpios y concisos, que sean lo más independientes de la versión posible.

Esta guí­a de estilo está basada en el Original SQL style guide por Simon Holywell bajo una licencia Creative Commons Attribution-ShareAlike 4.0 International License.

## Hacer

- Asegurarse de que todos los nombres de objetos, operaciones, consultas y comentarios estí©n escritos en inglí©s, para mantener la consistencia y facilitar la comprensión entre los desarrolladores.
- Utilizar identificadores y nombres consistentes y descriptivos.
- Encerrar nombres de objetos entre corchetes.
- Aprovechar la versión actual de SQL Server (Developer 2023), que ahora se encuentra optimizada para devolver objetos JSON de manera eficiente (con queries muy simples). Siempre que sea viable, preferir que la base de datos entregue JSON estructurado (por ejemplo, al enviar parámetros a unStored Procedure), en lugar de devolver listas de valores y convertirlos a JSON en el backend.
- Utilizar nombres de dos partes para bases de datos aisladas y nombres de tres partes cuando se utilicen bases de datos externas.
- Hacer un uso juicioso de los espacios en blanco y la sangrí­a para facilitar la lectura del código.
- Almacenar información de fecha y hora compatible con ISO-8601 (YYYY-MM-DD HH:MM:SS.SSSSS) con DATE, TIME, DATETIME2 y DATETIMEOFFSET.
- Mantener el código sucinto y sin SQL redundante, como comillas o parí©ntesis innecesarios o cláusulas WHERE que de otro modo se pueden derivar.
- Incluir comentarios en el código SQL donde sea necesario. Utilizar la apertura de estilo C, es decir utilizar /* y el cierre */ cuando sea posible, de lo contrario, preceder los comentarios con -- y terminarlos con una nueva lí­nea.
- Utilizar lenguaje de [Flow Control](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/control-of-flow?view=sql-server-ver16) cuando se emplee scripting avanzado o programabilidad.
- Proporcionar palabras clave TRY...CATCH para sentencias DML operativas.

```sql
SELECT [file_hash]  -- stored ssdeep hash
  FROM [dbo].[file_system]
 WHERE [file_name] = '.vimrc';
/* Updating the file record after writing to the file */
UPDATE [file_system]
   SET [file_modified_date] = '1980-02-22 13:19:01.00000',
       [file_size] = 209732
 WHERE [dbo].[file_name] = '.vimrc'
```

## Evitar

- CamelCase: es difí­cil de leer rápidamente.
- Plurales: utilice un tí©rmino colectivo más natural cuando sea posible. Por ejemplo, [Personal] en lugar de [Empleados], o [Gente] en lugar de [Personas]. Esto para no romper con la regla "is a". Ver notas al final del documento.
- Identificadores entre comillas.
- Comentar código antiguo o sentencias grandes que no están en uso. Comentar no es igual a control de versiones.

# Convenciones de nombres

## General

- Asegurar que el nombre sea único y no exista como palabra clave reservada.
- Mantener la longitud máxima de 30 bytes; en la práctica, esto equivale a 30 caracteres a menos que se utilice un conjunto de caracteres multibyte.
- Utilizar solo letras, números y guiones bajos en los nombres.
- Los nombres deben comenzar con una letra y no pueden terminar con un guion bajo.
- Evitar el uso de múltiples guiones bajos consecutivos, ya que pueden ser difí­ciles de leer.
- Utilizar guiones bajos donde se incluirí­a naturalmente un espacio en el nombre (por ejemplo, first_name en lugar de first name).
- Utilizar guiones bajos para separar palabras.
- Evitar abreviaturas y, si se deben usar, asegurarse de que sean de uso común.

```sql
  SELECT [first_name]
    FROM [dbo].[staff];
```
## Tablas
- Usar un nombre colectivo o, menos ideal, una forma singular. Por ejemplo (en orden de preferencia) personal y empleado.
- No utilizar prefijos como "tbl" o cualquier otro prefijo descriptivo o notación húngara.
- Nunca dar a una tabla el mismo nombre que una de sus columnas y viceversa.
- Evitar, en la medida de lo posible, concatenar dos nombres de tablas para crear el nombre de una tabla de relación. En lugar de "cars_mechanics", preferir "servicios".
- Evitar el uso de guiones bajos en los nombres, a menos que sea para designar un sufijo que diferencie dos versiones de una tabla, como "personal" y "personal_backup20170101".

## Columnas
- Utilizar siempre el nombre en singular.
- Evitar, en la medida de lo posible, utilizar el nombre simple "id" como identificador principal de la tabla.
- No agregar una columna con el mismo nombre que su tabla y viceversa.
- Utilizar siempre minúsculas, excepto cuando tenga sentido no hacerlo, como en nombres propios.

## Alias 
- Deben estar relacionados de alguna manera con el objeto o expresión que están aliasando.
- Como regla general, el nombre de alias debe ser la primera letra de cada palabra en el nombre del objeto.
- Si ya existe una correlación con el mismo nombre, agregar un número.
- Siempre incluir un alias explí­cito para facilitar la lectura. Por ejemplo (en orden de preferencia) [dbo].[employee] AS [emp] y [emp] = [dbo].[employee]. El primero es compatible con ANSI, el segundo solo es especí­fico para T-SQL.
- Para datos calculados (SUM() o AVG()), utilizar el nombre que se le darí­a si fuera una columna definida en el esquema.

```sql
  SELECT [first_name] AS [fn]
    FROM [staff] AS [s1]
    JOIN [dbo].[students] AS [s2]
      ON [s2].[mentor_id] = [s1].[staff_num];
  SELECT SUM([s].[monitor_tally]) AS [monitor_total] -- Importante ver este alias
    FROM [staff] AS [s];
```

## Stored Procedures

- El nombre debe contener un verbo.
- Usar el prefijo usp_ y no sp_, que podrí­a entrar en conflicto con los Stored Procedures del sistema.

## Sufijos uniformes

Los siguientes sufijos tienen un significado universal que garantiza que las columnas se puedan leer y entender fácilmente desde el código SQL. Utilizar el sufijo correcto cuando sea apropiado.

- _id: un identificador único, como una columna que es clave primaria.
- _status: valor de flag u otro tipo de estado, como "publication_status".
- _total: el total o suma de una colección de valores.
- _name: indica un nombre, como first_name.
- _date: indica una columna que contiene la fecha de algo.
- _count: un recuento.
- _size: el tamaí±o de algo, como un tamaí±o de archivo o ropa.


# Sintaxis de consulta

## Palabras reservadas

- Siempre usar mayúsculas para palabras clave reservadas como SELECT y WHERE.
- Es mejor evitar palabras clave abreviadas y usar las de longitud completa cuando estí©n disponibles (preferir ABSOLUTE a ABS).
- No utilizar palabras clave especí­ficas del servidor de base de datos cuando ya existe una palabra clave ANSI SQL que realiza la misma función. Esto ayuda a hacer que el código sea más portable.

```sql
SELECT [model_num]
  FROM [dbo].[phones] AS p
 WHERE [p].[release_date] > '2014-09-30';
```

## Espacios en blanco
Para facilitar la lectura del código, es importante utilizar la cantidad adecuada de espaciado. No abarrotar el código ni eliminar espacios del lenguaje natural.

## Espacios
Los espacios deben utilizarse para alinear el código de tal manera que todas las palabras clave principales terminen en el mismo lí­mite de caracteres. Esto forma un "rí­o" en el medio, facilitando que el ojo del lector pueda escanear el código y separar las palabras clave de los detalles de implementación. Los rí­os son malos en tipografí­a, pero útiles aquí­.

```sql
SELECT [f].[average_height], [f].[average_diameter]
  FROM [flora] AS f
 WHERE [f].[species_name] = 'Banksia'
    OR [f].[species_name] = 'Sheoak'
    OR [f].[species_name] = 'Wattle';
```

Fí­jate que SELECT, FROM, etc. están alineados a la derecha, mientras que los nombres de las columnas y los detalles especí­ficos de implementación están alineados a la izquierda.

Aunque no es exhaustivo, siempre incluir espacios:

- antes y despuí©s de los iguales (=)
- despuí©s de las comas (,)
- rodeando apóstrofes (') donde no estí©n entre parí©ntesis o con una coma o punto y coma final.

```sql
SELECT [a].[title], [a].[release_date], [a].[recording_date]
  FROM [albums] AS [a]
 WHERE [a].[title] = 'Charcoal Lane'
    OR [a].[title] = 'The New Danger';
```

## Espaciado de lí­neas (Enter)
Siempre incluir saltos de lí­nea/espacio vertical:

- antes de AND u OR
- despuí©s de los puntos y coma para separar las consultas y facilitar la lectura
- despuí©s de cada statement de un kewyord (por ejemplo SELECT,FROM, UPDATE o WHERE)
```sql
UPDATE [dbo].[albums]
   SET [release_date] = '1990-01-01 01:01:01.00000'
 WHERE [title] = 'The New Danger';
```
- despuí©s de una coma al separar grupos de columnas
```sql
INSERT INTO [dbo].[albums] ([title], [release_date], [recording_date])
VALUES ('Charcoal Lane', '1990-01-01 01:01:01.00000', '1990-01-01 01:01:01.00000'),
       ('The New Danger', '2008-01-01 01:01:01.00000', '1990-01-01 01:01:01.00000');
```

- para separar el código en secciones relacionadas, lo que ayuda a facilitar la legibilidad de grandes bloques de código.
```sql
SELECT [a].[title],
       [a].[release_date], [a].[recording_date], [a].[production_date] -- fechas agrupadas juntas
  FROM [albums] AS [a]
 WHERE [a].[title] = 'Charcoal Lane'
    OR [a].[title] = 'The New Danger';
``` 

Mantener todas las palabras clave alineadas al lado derecho y los valores alineados a la izquierda crea un espacio uniforme en el centro de la consulta Tambií©n facilita mucho el escaneo rápido de la definición de la consulta.


## Indentación
Para asegurarse de que el código SQL sea legible, es importante seguir los estándares de indentación.

### Joins
Los joins deben ser indentados hacia el otro lado del "rí­o", un tab más, y agrupados con una nueva lí­nea cuando sea necesario:

```sql
SELECT [r].[last_name]
  FROM [riders] AS [r]
       INNER JOIN [bikes] AS [b]
       ON [r].[bike_vin_num] = [b].[vin_num]
          AND [b].[engines] > 2
       INNER JOIN [crew] AS [c]
       ON [r].[crew_chief_last_name] = [c].[last_name]
          AND [c].[chief] = 'Y';

```
### Subqueries
Las subconsultas tambií©n deben estar alineadas en el lado derecho del "rí­o" y luego tener el mismo estilo que cualquier otra consulta. A veces, tiene sentido tener el parí©ntesis de cierre en una nueva lí­nea en la misma posición de carácter que su compaí±ero de apertura, especialmente cuando se tienen subconsultas anidadas:

```sql
SELECT [r].[last_name],
       (SELECT MAX(YEAR([championship_date]))
          FROM [champions] AS [c]
         WHERE [c].[last_name] = [r].[last_name]
           AND [c].[confirmed] = 'Y') AS [last_championship_year]
  FROM [riders] AS [r]
 WHERE [r].[last_name] IN
       (SELECT [c].[last_name]
          FROM [champions] AS [c]
         WHERE YEAR([championship_date]) > '2008'
           AND [c].[confirmed] = 'Y');

```           

## Formalismos
- Utilizar ">=" y "<" en lugar de BETWEEN para rangos de fecha.
- Use "IN()" en lugar de múltiples cláusulas OR.
- Evite el uso de cláusulas UNION y tablas temporales cuando sea posible. Si el esquema se puede optimizar para eliminar la dependencia de estas caracterí­sticas, probablemente deberí­a hacerse.
- Envuelva el contenido del procedimiento almacenado en declaraciones BEGIN...END.
- Use INNER JOIN en lugar de JOIN para hacer coincidir más claramente la sintaxis LEFT JOIN y RIGHT JOIN.

```sql
SELECT CASE [postcode]
       WHEN 'BN1' THEN 'Brighton'
       WHEN 'EH1' THEN 'Edinburgh'
       END AS [city]
  FROM [office_locations]
 WHERE [country] = 'United Kingdom'
   AND [opening_time] BETWEEN 8 AND 9
   AND [postcode] IN ('EH1', 'BN1', 'NN1', 'KW1');
```

# Sintaxis para CREATE
Cuando se crean objetos, tambií©n es importante mantener el código legible para los humanos. Para facilitar esto, asegúrese de que las definiciones de columnas estí©n ordenadas y agrupadas donde tenga sentido hacerlo.

Coloque una sangrí­a en las definiciones de columna por un TAB ó cuatro (4) espacios dentro de la definición CREATE.

```sql
CREATE TABLE [dbo].[my_table](
    [id] [int] IDENTITY(1,1) NOT NULL,
    [name] [varchar](50) NULL,
    [description] [varchar](255) NULL,
    [created_at] [datetime2](7) NOT NULL CONSTRAINT [DF_my_table_created_at] DEFAULT (GETUTCDATE()),
    [updated_at] [datetime2](7) NOT NULL CONSTRAINT [DF_my_table_updated_at] DEFAULT (GETUTCDATE())
) ON [PRIMARY];
```

## Selección de tipos de datos
- Solo use tipos REAL o FLOAT cuando sea estrictamente necesario para matemáticas de punto flotante. En todos los demás casos, prefiera NUMERIC y DECIMAL Esto evita los errores de redondeo de punto flotante.
- No utilice tipos de datos obsoletos como NTEXT, TEXT e IMAGE. En su lugar, use sus equivalentes (NVARCHAR(MAX), VARCHAR(MAX) y VARBINARY(MAX)).
- Defina explí­citamente la precisión (lo más pequeí±a posible) de una columna para los tipos de datos aplicables.

## Diseí±os a evitar
- Los principios de diseí±o orientado a objetos no se traducen eficazmente a diseí±os de bases de datos relacionales, evita esta trampa.
- Evitar colocar el valor en una columna y las unidades en otra columna. La columna debe hacer que las unidades sean evidentes por sí­ mismas para evitar la necesidad de combinar columnas nuevamente más tarde en la aplicación. Usa CHECK() para garantizar que se inserten datos válidos en la columna.
- Dividir los datos que deben estar en una tabla en muchas debido a preocupaciones arbitrarias como el archivado basado en el tiempo o la ubicación en una organización multinacional. Luego, las consultas deben trabajar en varias tablas con UNION en lugar de simplemente consultar una tabla. 


# Notas
La regla "is a" en SQL no es un concepto general en el modelado de datos. 

La regla "is a" se utiliza para describir una relación en la que un objeto o entidad "es un tipo de" otro objeto o entidad. Por ejemplo, considera la relación entre las entidades "Vehí­culo" y "Automóvil". Un "Automóvil" es un tipo especí­fico de "Vehí­culo", por lo que podrí­amos decir que un Automóvil "es un" Vehí­culo, y esto establece una relación de herencia entre las dos entidades.
