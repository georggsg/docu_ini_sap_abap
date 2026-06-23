---
tags:
  - abap
  - sap-hr
  - control-flujo
  - if
  - case
  - bucles
bloque: "06"
---

# Operadores y control de flujo

Son muy parecidos a los de los otros lenguajes, el cambio mas grande es la sintaxis.

Los operadores de comparación hay dos formas de escribirlos, con símbolo o con texto.

- = o EQ: Igual.
- <> o NE: Distinto.
- (> o GT: Mayor).
- < o LT: Menor.
- (>= GE: Mayor o igual).
- <= LE: Menor o igual.

Además, podemos utilizar los operadores lógicos:

- AND: Y.
- OR: O.

## IF: Condicional

```abap
IF lv_edad >= 18. " es mayor de edad
ELSEIF lv_edad > 0. " es menor de edad
ELSE. " valor no valido, ninguna de las anteriores
ENDIF.
```

## CASE

Para comparar una variable contra varios valores posibles.

```abap
CASE lv_opcion.
  WHEN 1.
  " opción 1
  WHEN 2.
  " opción 2
  WHEN OTHERS.
  " cualquier otro valor
ENDCASE.
```

## Bucles

### DO

Repite un número de veces. La variable de sistema *sy-index* lleva la cuenta de la vuelta.

```abap
DO 5 TIMES.
 " lógica
ENDDO.
```

### WHILE

Repite mientras se cumpla una condición.

```abap
WHILE condición.
 " lógica
ENDWHILE.
```

### LOOP AT

Recorre las filas de una tabla interna ( de los mas usados en ABAP).

```abap
LOOP AT lt_empleados INTO ls_empleado.
 " lógica a ejecutar por cada vuelta
ENDLOOP.
```


---
[[04 - Tipos (TYPES)|← Tipos (TYPES)]]  ·  [[00 - Índice|Índice]]  ·  [[05 - Variables de sistema|Variables de sistema →]]
