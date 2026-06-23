---
tags:
  - abap
  - sap-hr
  - tipos
  - types
  - estructuras
  - tablas-internas
bloque: "04"
---

# Tipos (TYPES)

## Tipos primitivos

Como todo lenguaje de programación, ABAP tiene sus tipos primitivos y algunos particulares respecto a los comunes de otros lenguajes de programación.

- **Tipo C:** Carácter. Se le puede asignar longitud de las siguientes formas:
  - DATA: lv_variable TYPE c LENGTH x. (número de longitud).
  - DATA: lv_variable(5) TYPE c. (el número indicado entre paréntesis es la longitud).
- **Tipo N:** Texto numérico, no es un tipo que se utilice para operaciones/cálculos. Su utilidad es para almacenar números como caracteres.
- **Tipo I:** Entero. (int en otros lenguajes).
- **Tipo P:** Número empaquetado. Se utiliza para asignar el número de decimales exacto, uso para temas de divisas (monedas). La asignación de decimales es de la siguiente forma:
  - DATA: lv_variable TYPE p DECIMALS 2.
- **Tipo F:** Coma flotante, para mostrar decimales aproximados.
- **Tipo D:** Fechas. Son de 8 caracteres con formato 'AAAAMMDD'.
- **Tipo T:** Hora. Son de 6 caracteres con formato 'HHMMSS'.
- **Tipo string:** Cadena de caracteres.

## Tipos compuestos

A diferencia de los tipos primitivos, que almacenan un único valor, los tipos compuestos agrupan varios valores dentro de una sola *"variable"*. Se utilizan para representar datos más complejos. Los tipos compuestos pueden ser:

- **Estructuras:** Agrupan varios campos de distintos tipos bajo una misma variable. Se podría equiparar a una fila de una tabla. Son las que nos van a permitir trabajar con datos para posteriormente insertarlos a una tabla interna.

Para declarar la estructura utilizaremos la siguiente estructura:

```abap
TYPES: BEGIN OF str,  " Es la definición de la plantilla que tendrá nuestra variable.
Campo TYPE i,
Campo1 TYPE c LENGTH 10,
Campo2 TYPE d.
END OF str.
DATA: lw_estructura TYPE str.
```

*Podemos usar el nombre que queramos para la estructura, cuanto más descriptivo, mejor*.

Después de haber declarado la estructura y una variable con esa estructura, cuando queramos utilizar algún campo de esa estructura o asignar un valor, valdrá con llamar a la variable, seguido de un guion y el nombre del campo.

```abap
lw_estructura-campo = 5.
lw_estructura-campo1 = 'Hola Mundo'.
lw_estructura-campo2 = '20260605'.  " Fecha, formato 'AAAAMMDD'.
```

Una estructura puede contener en vez de campos, otra estructura, una tabla interna o una mezcla entre todos, en función de lo que necesitemos. Para incluir una estructura dentro de otra o una tabla interna sus sentencias son distintas.

```abap
TYPES: BEGIN OF estructura,
Campo6 TYPE i,  " Este campo es opcional, podemos añadir solo una estructura.
INCLUDE TYPE str.  " Incluimos TODA la estructura anterior declarada.
END OF estructura.
DATA: lw_estructura2 TYPE estructura.
```

Ahora podremos usar la última estructura declarada como lw_estructura2, que contiene el campo, campo6 de tipo I y a mayores, todos los campos de la primera estructura.

```abap
lw_estructura2-campo = 2.
lw_estructura2-campo1 = 'Mundo Hola'.
lw_estructura2-campo2 = '20260606'.
lw_estructura2-campo6 = 1.
```

*\*Si queremos incluir una estructura que está definida en el DICCIONARIO DE DATOS (SE11) en vez de usar INCLUDE TYPE usaremos INCLUDE STRUCTURE.\**

```abap
TYPES: BEGIN OF estructura,
INCLUDE STRUCTURE estructura_diccionario_de_datos.
END OF estructura.
```

- **Tablas Internas:** Como su nombre indica es una tabla que adopta la forma a través de una estructura. Su utilidad es almacenar información traída desde una tabla de bases de datos o datos que asignemos nosotros a través de la variable de la estructura. Estas tablas son temporales ya que existen solo en tiempo de ejecución de programa. Se definen de la siguiente forma.

```abap
DATA: lt_tabla TYPE TABLE OF nombre_estructura.
```

Podemos asignar 3 tipos de tablas internas, por defecto SAP asume que con la declaración anterior es de tipo STANDARD.

- **STANDARD:** Es la más común. Se accede a sus filas por su posición *(índice). sy-tabix*

```abap
DATA: lt_tabla TYPE STANDARD TABLE OF nombre_estructura.
```

Se asigna por defecto si no le declaramos otro de los tipos a la tabla.

- **SORTED:** Se mantiene siempre ordenada automáticamente por la clave que le indiquemos. Su acceso es mucho mas rápido que una tabla de tipo STANDARD.

```abap
DATA: lt_tabla TYPE SORTED TABLE OF nombre_estructura WITH UNIQUE KEY campo.
```

La clave única se puede utilizar cuando queremos insertar registros y que nunca se repitan.

- **HASHED:** Solo se accede a sus filas por una clave única, sin usar índice. Es la más rápida para buscar por clave en tablas muy grandes.

```abap
DATA: lt_tabla TYPE HASHED TABLE OF nombre_estructura WITH UNIQUE KEY campo.
```


---
[[02 - Elementos básicos (Transacciones)|← Elementos básicos (Transacciones)]]  ·  [[00 - Índice|Índice]]  ·  [[06 - Operadores y control de flujo|Operadores y control de flujo →]]
