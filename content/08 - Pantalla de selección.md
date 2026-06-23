---
tags:
  - abap
  - sap-hr
  - pantalla-seleccion
  - parameters
  - select-options
  - tablas-internas
  - sql
bloque: "08"
---

# Pantalla de selección (PARAMETERS / SELECT-OPTIONS)

Es la pantalla inicial donde el usuario introduce los datos con los que va a trabajar el programa. Se genera automáticamente al declarar los elementos *PARAMETERS* o *SELECT-OPTIONS.*

- **PARAMETERS:** Crea un único campo de entrada para un solo valor.

```abap
PARAMETERS: p_pernr TYPE pernr_d.
```

Esto sería su salida:
![[Pasted image 20260616093139.png]]

- **SELECT-OPTIONS:** Crea dos campos de entrada para un rango de valores. Internamente no es una variable, si no una tabla interna que SAP genera automáticamente, con una estructura fija de 4 columnas, donde cada fila es una condición de selección.
- **SIGN:** Es un valor que indica si la condición incluye o excluye. (i) include o (e) exclude.
- **OPTION:** Es el operador de comparación, en base al numero introducido en los campos de entrada.
- **EQ – equal:** (igual).
- **BT – between:** (entre dos valores).
- **GT – greater than:** (mayor que).
- **LT – lower than:** (menor que).
- **LOW:** El primer valor de la condición. Primer campo de entrada introducido.
- **HIGH:** El segundo valor de la condición. Segundo campo de entrada introducido.

Al declarar el SELECT-OPTIONS se indica un campo de referencia (FOR `<campo>`) Ese campo solo sirve para dar el tipo y la longitud a las columnas LOW y HIGH, no almacena ningún valor.

Esta tabla se rellena sola según lo que el usuario introduzca en la pantalla de selección, convirtiendo cada entrada en una fila.

Si introducimos un único valor, se crea una fila con SIGN = I, OPTION = EQ y el valor introducido en LOW.

Si introducimos un rango, el operador (OPTION) pasa a ser BT y se rellenan los campos LOW y HIGH con los respectivos valores introducidos.

Si excluimos un valor, la fila se crea con SIGN = E.

La tabla se utiliza en las consultas con el operador IN. Al escribir la condición en la consulta, el operador coge toda la tabla de condiciones y la traduce automáticamente.

[81 - ABAP Programming - Parts of Select Options Part1](https://www.youtube.com/watch?v=99_r3JBXH-A)
[82 - ABAP Programming - Parts of Select Options Part2 - YouTube](https://www.youtube.com/watch?v=pGZFUv7tuQc)

En los dos videos se explica muy bien y paso a paso cómo funciona SELECT-OPTIONS.

```abap
" Campo de referencia (solo aporta el tipo a LOW y HIGH
DATA: lv_pernr TYPE pernr_d.

" Declaracion del SELECT-OPTIONS
SELECT-OPTIONS: s_pernr FOR lv_pernr.

START-OF-SELECTION.
* Recuperamos los empleados que entran en el rango introducido.

SELECT pernr, vorna, nachn
 FROM pa0002
 INTO TABLE @DATA(lt_empleados)
 WHERE pernr IN @s_pernr.
```

Dentro del SELECT-OPTIONS tenemos 3 variantes que le podemos añadir, se pueden combinar entre ellas o usar todas juntas:

```abap
SELECT-OPTIONS: variable FOR variable_local OBLIGATORY. "Indicamos que ambos campos deben de estar rellenos.

SELECT-OPTIONS: variable FOR variable_local NO-EXTENSION. "Ocultamos el boton extra para hacer múltiples selecciones.

SELECT-OPTIONS: variable FOR variable_local NO INTERVALS. "Ocultamos la opcion de poder introducir un valor en la segunda casilla (high).
```

## Bases de datos y tablas internas

### Operaciones con bases de datos

Para leer datos de las tablas de bases de datos se utiliza la sentencia *SELECT*. Lo habitual es trae los datos a una estructura o a una tabla interna para poder trabajar con ellos.

Para unas buenas prácticas a la hora de hacer consultas. En los SELECT siempre tenemos que indicar el/los campos que queremos y nunca utilizar el "\*".

Es muy recomendable siempre que sea posible utilizar la condición WHERE.

Después de hacer una consulta SELECT se recomienda tratar el *sy-subrc* para comprobar si la consulta ha traido registro de los campos indicados o no.

Si, sy-subrc = 0. Es que la consulta ha funcionado correctamente.

Si, sy-subrc = 4. No se han encontrado registros.

Las formas más habituales de consultas son:

- SELECT SINGLE: Lee un único registro y lo guarda en una estructura. Se usa cuando queremos un único registro.

```abap
SELECT SINGLE pernr, vorna, nachn
  FROM pa0002
  INTO @lw_empleado
  WHERE pernr = @p_pernr.
```

- SELECT…INTO TABLE: Lee todos los registros que contenga la tabla de base de datos y los guarda en una tabla interna.

```abap
SELECT pernr, vorna, nachn
  FROM pa0002
  INTO TABLE @lt_empleados
  WHERE pernr = @p_pernr.
```

>[!info]
>En las últimas versiones de ABAP, las variables del programa que aparecen dentro de un *SELECT* se marcan con *"@"* para distinguir los campos de nuestra estructura o tabla interna local de las tablas de bases de datos. Además podemos declarar la tabla en la propia sentencia con:


```abap
INTO TABLE @DATA(lt_empleados).
```

## Operaciones con tablas internas

Una vez tenemos los datos en una tabla interna, volcados anteriormente desde la consulta SELECT con *INTO TABLE*, o queremos rellenarla nosotros, sin utilizar registros de una tabla de bases de datos estas son las operaciones que podemos utilizar:

- APPEND: Añade una fila al final de la tabla. Es la forma habitual de ir rellenando una tabla STANDARD.

```abap
APPEND lw_empleado TO lt_empleados.
```
* REFRESH: Elimina los datos de una tabla interna.
```ABAP
REFRESH lt_empleados.
```
Normalmente el APPEND lo utilizamos al final de tener la estructura rellena con los datos que queremos insertar en la tabla interna. Con cada APPEND, insertamos un registro, con lo cual, si queremos insertar una única fila con varios registros, primero tenemos que asignarle el valor a cada campo de la estructura antes de hacer el APPEND.

- INSERT: También añade filas, pero es mas utilizado en tablas SORTED o HASHED, colocando la fila en su sitio respetando la clave.

```abap
INSERT lw_empleado INTO TABLE lt_empleados.
```

- READ TABLE: Lee UNA fila concreta, puede leer por su posición (índice) o por el valor de un campo clave.

```abap
READ TABLE lt_empleados INTO lw_empleado WITH KEY pernr = p_pernr.  " lectura por clave

READ TABLE lt_empleados INTO lw_empleado INDEX 1.
" lectura por posición (índice)
```

- LOOP AT: Recorre todas las filas de la tabla, una a una a la estructura, permitiéndonos así asignar valores a cada campo de la estructura.

```abap
LOOP AT lt_empleados INTO lw_empleado.
 " lógica a realizar por cada vuelta
ENDLOOP.
```

- MODIFY: Modifica una fila ya existente de la tabla. Las formas mas utilizadas son, dentro de un loop o dándole una posición.

```abap
LOOP AT lt_empleados INTO lw_empleado.
  lw_empleado-nachn = 'García'.
  MODIFY lt_empleados FROM lw_empleado. " modifica la fila actual del loop. Si asignamos mas valores.
ENDLOOP.

MODIFY lt_empleados FROM lw_empleado INDEX 1. " por  posicion.
```

- DELETE: Elimina filas, ya se una concreta o todas las que cumplan la condición.

```abap
DELETE lt_empleados WHERE pernr = p_pernr. " elimina las filas que cumplan la condición.
```

- SORT: Ordena la tabla por uno o varios campos.

```abap
SORT lt_empleados BY pernr.
```


---
[[07 - Eventos (report)|← Eventos (report)]]  ·  [[00 - Índice|Índice]]  ·  [[09 - Modularización|Modularización (FORM / PERFORM, INCLUDES, módulos de funciones) →]]
