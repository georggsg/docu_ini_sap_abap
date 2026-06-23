---
tags:
  - abap
  - sap-hr
  - modularizacion
  - form
  - perform
  - includes
  - funciones
bloque: "09"
---

# Modularización (FORM / PERFORM, INCLUDES, módulos de funciones)

Modularizar consiste en dividir el programa en bloques reutilizables, en lugar de tener todo el código seguido. Nos aporta ciertas ventajas, como: evitar repetir código (lo escribes una vez y después lo puedes reutilizar), su mantenimiento es más sencillo (si necesitamos cambiar algo, solo lo tenemos que cambiar en un sitio), hacemos el programa más legible.

Las subrutinas *(FORM)* es un bloque de lógica que se ejecuta cuando se le llama *(PERFORM)*, pero SOLO DENTRO DE SU PROGRAMA. Un INCLUDE es un trozo de código fuente que se inserta dentro del programa, no es una llamada, sino una forma de organizar el código. Un módulo de funciones, también se ejecuta al llamarlo, pero es accesible desde cualquier programa del sistema.

## Subrutinas (FORM/PERFORM)

Son bloques de código que le asignamos un nombre. Se definen dentro del propio programa y las podemos llamar tantas veces como queramos o necesitemos.

Se define con:

```abap
FORM f_nombreform.
  " bloque de código
ENDFORM.
```

Se llama con:

```abap
PERFORM f_nombreform.
```

Normalmente las subrutinas se definen al final del programa y pueden ser llamadas desde cualquier punto. No importa el orden.

La clave de las subrutinas es el paso de parámetros, que permiten enviarles datos y recoger resultados. Los más utilizados son *USING* y *CHANGING.*

- USING: Son los parámetros de entrada, los datos que pasamos a la subrutina para que pueda trabajar. (input)
- CHANGING: Son los parámetros de entrada/salida, que entran, se modifican dentro de la subrutina y salen ya cambiados. Sirven para devolver resultados. (output)

```abap
" Definición de la subrutina
FORM calcular_total USING pi_valor1 TYPE i
        pi_valor2 TYPE i
      CHANGING po_total TYPE i.

  po_total = pi_valor1 + pi_valor2.
ENDFORM.

PERFORM calcular_total USING 5 3 CHANGING lv_total.
```

En la llamada de la subrutina tenemos que usar el nombre de las variables que queramos utilizar y no tenemos que declarar el tipo.

En la declaración de la subrutina no es necesario utilizar el nombre de la variable declarada, es recomendable, como buena práctica que a los parámetros del USING comiencen por *"pi"* y los parámetros de salida en CHANGING empiecen por *"po"*. De esta forma sabremos cuales serán los parámetros de entrada y cuales de salida sin tener que consultar la declaración de la variable. Otra buena práctica es declarar el tipo de la variable que vamos a utilizar.

Hay que respetar el orden de los parámetros en la llamada, si el primero declarado es variable1 y el segundo variable2, en la llamada PERFORM tendremos que llamarlas en el mismo orden, primero variable1 y después variable2.

Cualquier variable que declaremos DENTRO de la declaración de la subrutina solo existirá única y exclusivamente cuando se ejecute la subrutina. Diríamos que, es una variable local de la subrutina.

## Includes

Los INCLUDES son ficheros independientes que contienen una porción de código y que los tenemos que insertar dentro del programa principal. Los INCLUDES no se pueden ejecutar por si solos, dependen siempre de un programa. Como existen distintos tipos de includes, para *VARIABLES GLOBALES, FORMS, PBO* y *PAI* cuando el programa necesita utilizar cualquier elemento que esté dentro de alguno de estos includes, accede a el y reconoce el elemento necesario o lo ejecuta. Estos archivos nos facilitan la legibilidad del código y su modularización permitiéndonos tener nuestro programa segmentado de una forma limpia y ordenada.

Para insertar cualquier include, dentro del programa tendremos que escribir la siguiente sentencia:

```abap
INCLUDE nombre_include.
```

Existen una nomenclatura concreta para cada tipo de INCLUDE y es conveniente respetar:

- **_TOP:** Estos include contienen las declaraciones globales (tipos, variables, estructuras y tablas). Siempre debe de estar insertado el primero ya que el código del programa va a depender de las variables, tipos, estructuras y tablas declaradas dentro de él.
- **_F01, _F02…:** Estos include contienen las declaraciones de las subrutinas *(FORM).*
- **_O01, I01…:** Estos include contienen los módulos de salida (PBO O01) y los de entrada (PAI I01) que son utilizados sobre todo en los programas de diálogo (Module Pool).

Cuando creamos un programa de tipo *MODULE POOL*, SAP genera automáticamente esta estructura de includes de la que hemos hablado. La idea es la misma que dividir un proyecto en varios ficheros para tenerlo ordenado, con la diferencia de que aquí todos los trozos se combinan en un único programa que comparte los mismos datos globales. Con datos globales nos referimos a el contenido de cada INCLUDE.

## Modulo de funciones
[[02 - Elementos básicos (Transacciones)#SE37 (Módulo de funciones)|Modulo de funciones (SE37]]

---
[[08 - Pantalla de selección|← Pantalla de selección (PARAMETERS / SELECT-OPTIONS)]]  ·  [[00 - Índice|Índice]]  ·  [[10 - Salidas de datos (WRITE y ALV)|Salidas de datos (WRITE y ALV) →]]
