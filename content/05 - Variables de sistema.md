---
tags:
  - abap
  - sap-hr
  - variables-sistema
  - sy
bloque: "05"
---

# Variables de sistema

Son un conjunto de variables predefinidas que el propio entorno de SAP rellena y mantiene actualizadas automáticamente. Es el propio sistema de SAP el que las genera a la hora de ejecutar un programa.

Se agrupan bajo la estructura *"SY o SYST"* se accede a ellas con la siguiente sintaxis: *sy-nombre.*

Las variables de sistema más utilizadas son:

- Sy-subrc: Es el código de retorno de la última operación que se haya ejecutado. La última línea procesada. Tiene múltiples valores, pero se tomará el valor 0 como válido, es decir, que la línea anterior se procesó correctamente. Es muy utilizado para control de errores.
Condiciones.
```abap
IF sy-subrc = 0.
	" código
ENDIF.
```

```abap
IF sy-subrc <> 0.
	" código
ENDIF.
```

- Sy-tabix: El índice de la fila actual al recorrer una tabla interna o tras un *READ TABLE*.
- Sy-index: El número de iteración (vuelta) dentro de un bucle *DO o WHILE.*
- Sy-datum: La fecha actual del sistema.
- Sy-uzeit: La hora actual del sistema.
- Sy-uname: El nombre del usuario que ha iniciado sesión.


---
[[06 - Operadores y control de flujo|← Operadores y control de flujo]]  ·  [[00 - Índice|Índice]]  ·  [[07 - Eventos (report)|Eventos (report) →]]
