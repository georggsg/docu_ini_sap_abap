---
tags:
  - abap
  - sap-hr
  - eventos
  - report
  - selection-screen
bloque: "07"
---

# Eventos (report)

Como se menciona al principio, ABAP es un lenguaje orientado a eventos, es decir, un programa ejecutable *(report)* no solo se ejecuta de arriba abajo de forma secuencial, si no que se organiza en eventos. El sistema va lanzando esos eventos en un orden específico, en respuesta a acciones del sistema o del usuario y nosotros escribimos el código dentro del evento que corresponda. Cada tipo de programa tiene su propio conjunto de eventos, en este apartado veremos los del programa ejecutable *(report),* es el más común:

## Eventos de la pantalla de selección (INITIALIZATION/AT SELECTION-SCREEN ON/ AT SELECTION-SCREEN)

  - INITIALIZATION: Se ejecuta una vez, antes de mostrar la pantalla de selección. Se utiliza para dar valores por defecto a los campos de entrada.

```abap
INITIALIZATION.
p_fecha = sy-datum.  " por defecto, la fecha de hoy
```

- **AT SELECTION-SCREEN ON `<campo>`:** Sirve para comprobar que el valor que el usuario ha escrito en un campo concreto es correcto. Se ejecuta en el momento en que el usuario confirma la pantalla (pulsando Enter o ejecutar (F8)), pero revisa únicamente el campo que le indiquemos. Si el valor no es válido, la pantalla vuelve a parecer con el cursor ya colocado en ese campo, indicando al usuario exactamente qué tiene que corregir. Se usa para validaciones que afectan a un solo campo.

```abap
AT SELECTION-SCREEN ON p_edad.
  IF p_edad < 18.
    MESSAGE 'Debe ser mayor de edad' TYPE 'E'.
  ENDIF.
```

- **AT SELECTION-SCREEN:** Comprueba la pantalla completa, teniendo en cuenta todos los campos. Se ejecuta después de las validaciones individuales de cada campo (AT SELECTION-SCREEN ON) justo antes de que el programa pase al procesamiento principal. Es el lugar indicado para validaciones que dependen de varios campos al mismo tiempo. Si algo no es correcto, la pantalla se vuelve a mostrar para que el usuario lo corrija.

```abap
AT SELECTION-SCREEN.
  IF p_fecha_ini > p_fecha_fin.
    MESSAGE 'La fecha inicial no puede ser mayor que la final' TYPE 'E'.
  ENDIF.
```

Existe también el evento *AT SELECTION-SCREEN OUTPUT,* que permite modificar la pantalla de forma dinámica (ocultar o activar campos).

## Eventos de procesamiento (START-OF-SELECTION / END-OF-SELECTION)

Son los que controlan el flujo principal del programa, el momento en que se lee los datos, se procesan y se preparan los resultados. Se ejecutan una vez que la pantalla de selección *(AT SELECTION-SCREEN)* ya ha sido validada correctamente.

- **START-OF-SELECTION:** Es el evento central, donde va el grueso del programa (leer datos, procesarlos, montar tablas internas etc…) Si no se indica ningún evento, el código se asume implícitamente aquí.

```abap
START-OF-SELECTION.
  SELECT pernr, vorna, nachn
    FROM pa0002
    INTO TABLE @lt_tabla_interna
    WHERE pernr = @p_pernr.
```

- **END-OF-SELECTION:** Se ejecuta una vez, después de START-OF-SELECTION. Ideal para cálculos finales sobre todos los datos o para lanzar la salida.

```abap
END-OF-SELECTION.
  WRITE: / 'Total de empleados encontrados',
    lv_total.
```

## Eventos de lista e interactividad (TOP-OF-PAGE / AT LINE-SELECTION / AT USER-COMMAND)

Sirve para mostrar los datos procesados. La forma más básica es con un listado: una pantalla de texto donde el programa escribe los resultados línea a línea (WRITE). Los eventos de este grupo le dan formato y permiten reaccionar a lo que hace usuario sobre él. Para mostrar los datos hoy en día la sentencia WRITE apenas se usa. Siendo el ALV la manera de mostrar resultados. Es una forma más moderna que pinta una tabla con columnas ordenables, filtros y exportaciones integradas.

- **TOP-OF-PAGE / END-OF-PAGE:** Se ejecutan al inicio y al final de cada página del listado, se usan para cabeceras y pies de página.

```abap
TOP-OF-PAGE.
  WRITE: / 'Listado de empleados'.
  ULINE. " pinta una barra de separación

END-OF-PAGE.
  WRITE: / 'Página',
      sy-pagno.
```

- **AT LINE-SELECTION:** Se dispara al hacer doble clic sobre una línea.

```abap
AT LINE-SELECTION.
  WRITE: / 'Has seleccionado la línea',
      sy-lilli.
```

- **AT USER-COMMAND:** Se ejecuta al pulsar un botón de la barra de herramientas o una opción de menú. El código del botón llega en sy-ucomm.

```abap
AT USER-COMMAND.
  CASE sy-ucomm.
   WHEN 'BACK'.
    LEAVE SCREEN.
  ENDCASE.
```

## Esqueleto mínimo de report

El esqueleto mínimo de report juntando los eventos básicos, podría ser:

```abap
INITIALIZATION.
  " valores por defecto.
AT SELECTION-SCREEN.
  " validaciones, si es que las tenemos.
START-OF-SELECTION.
  " lógica principal.
END-OF-SELECTION.
  " cálculos finales y salida.
```


---
[[05 - Variables de sistema|← Variables de sistema]]  ·  [[00 - Índice|Índice]]  ·  [[08 - Pantalla de selección|Pantalla de selección (PARAMETERS / SELECT-OPTIONS) →]]
