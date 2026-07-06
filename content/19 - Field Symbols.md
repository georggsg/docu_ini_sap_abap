---
tags:
  - abap
  - sap-hr
  - fieldsymbol
bloque: "19"
---
# Field symbols

Son un tipo especial de variable que **no almacenan datos**, si no que **"apunta"** a otra variable o a un registro de una tabla interna. Podríamos asumir que trabaja como un *puntero*. No copia los datos, en cambio, hace referencia directa al dato original.
>[!info]
>Todo lo que hagamos a través del field symbol se aplica directamente a sobre aquello a lo que apunta.

La sintaxis para declarar un field symbol es como la declaracion de variables, cambiando la palabra *DATA* por *FIELD-SYMBOLS*.
```abap
	FIELD-SYMBOLS: <fs_tab> TYPE (tipo primitivo / estructura)
```


La diferencia mas notoria sobre los field-symbol y los workarea reside en: los workarea actúan como una copia del registro. Cuando en un *LOOP* volcamos una fila en el workarea, trabajamos sobre esa copia. Cuando se le asigna un *field-symbol*, trabajamos sobre la fila real de la tabla.

## Asignar field-symbol

Para que un field-symbol apunte a una variable usamos la palabra ***ASSIGN***. A partir de ahí, el *field-symbol* y la variable/estructura son "la misma cosa". Es decir, si cambiamos el field-symbol, cambia el valor de la variable, o el valor del campo de esa estructura.
```abap
	DATA: lv_var1(2) TYPE n VALUE 10.
	FIELD-SYMBOLS: <fs_var1> TYPE n.
	
	ASSIGN lv_var1 TO <fs_var1>.
	IF <fs_var1> IS ASSIGNED.
		<fs_var1> = 20.
	ENDIF.
```
>[!info]
>El bloque *IF field-symbol IS ASSIGNED* es obligatorio al igual que cuando hay que validar el *sy-subrc* despues de una select. Ya que si el field-symbol no esta asignado el report nos dará un error en tiempo de ejecución. 

### Asignar con offset

Con *ASSIGN* podemos apuntar no solo a una variable entera, si no a una parte de ella, usando *offset*. Tras el signo *+* indicamos la posición inicial que queramos seleccionar y, entre paréntesis, la longitud de lo que vamos a seleccionar.
```abap
	DATA: lv_var2(50) TYPE c VALUE 'Welcome to Home'.
	FIELD-SYMBOLS: <fs_var2> TYPE c.
	
" la palabra 'to' empieza en la posición 8 y tiene longitud 2
ASSIGN lv_var2+8(2) TO <fs_var2>
	IF <fs_var2> IS ASSIGNED.
	   <fs_var2> = 'at'.
	ENDIF.
" Ahora lv_var2 es 'Welcome at Home'
```
Esta es otra diferencia con el work area, podemos apuntara a una porción/parte de una variable mediante offset.

### Field symbols con tablas internas
Es donde más se usan y donde más rendimiento tienen. En lugar de un work area, asignamos la fila directamente al field symbol.
```abap
	TYPES: BEGIN OF ty_emp,
			pernr TYPE pernr_d,
			nachn TYPE nachn,
			END OF ty_emp.
	DATA: lt_empleados TYPE TABLE OF ty_emp.
	FIELD-SYMBOLS: <fs_emp> TYPE ty_emp.
	
	LOOP AT lt_empleados ASSIGNING <fs_emp>.
		IF <fs_emp> IS ASSIGNED.
			WRITE: / <fs_emp>-pernr, <fs_emp>-nachn.
		ENDIF.
	ENDLOOP.
```
#### LOOP con field symbol
Recorremos la tabla apuntando a cada fila directamente.
```abap
	LOOP AT lt_empleados ASSIGNING <fs_emp>.
		IF <fs_emp> IS ASSIGNED.
			WRITE: / <fs_emp>-pernr, <fs_emp>-nachn.
		ENDIF.
	ENDLOOP.
```
#### READ TABLE con field symbol
Exactamente igual que con work area, pero apuntamos a la fila encontrada.
```abap
	READ TABLE lt_empleados ASSIGNING <fs_emp> WITH KEY pernr = 1.
		IF <fs_emp> IS ASSIGNED.
			WRITE: / <fs_emp>-pernr, <fs_emp>-nachn.
		ENDIF.
```
>[!info]
>Con field symbols, si no se encuentra la fila: (sy-subrc <> 0) el field symbol queda sin asignar. Por esa razón comprobamos siempre con *IS ASSIGNED*. Si no está asignado, el bloque no se ejecuta y el programa continua sin dar error en tiempo de ejecución (dump).

#### MODIFY con field symbol
Aqui tenemos una de las ventajas mas claras de los field symbol. Con work area tenemos que volcar la fila, cambiarla y hacer un *MODIFY* para guardar el cambio en la tabla.
```abap
	LOOP AT lt_empleados INTO lw_empleados
		lw_empleados-vorna = 'Jose'.
		MODIFY lt_empleados FROM lw_empleados TRANSPORTING vorna.
	ENDLOOP.
```
En cambio, con el field symbol, como estamos apuntando directamente a la fila, el cambio se aplica solo.
```abap
	LOOP AT lt_empleados ASSIGNING <fs_emp>.
		IF <fs_emp> IS ASSIGNED.
			<fs_emp>-vorna = 'Jose'.
		ENDIF.
	ENDLOOP.
```
Esta es la diferencia más visible entre ambos: el workarea es una copia, por lo tanto necesita *MODIFY* para devolver un cambio a la tabla. El field symbol es una referencia a la fila real, el cambio queda hecho directamente.

#### APPEND con field symbol

También podemos añadir filas con field symbol. Con *APPEND INITIAL LINE TO... ASSIGNING* creamos una línea nueva vacía en la tabla y hacemos que el field symbol apunte a ella, luego la rellenamos campo a campo como hariamos con el work area. 
```abap
	APPEND INITIAL LINE TO lt_empleados ASSIGNING <fs_emp>.
	IF <fs_emp> IS ASSIGNED.
		<fs_emp>-pernr = 1.
		<fs_emp>-nachn = 'Rodriguez'.
	UNASSIGN <fs_emp>.
	ENDIF.
```
El flujo es: crear la línea vacía -> apuntar a ella con el field symbol -> rellenar los campos a través del field symbol.

#### UNASSIGN
Con esta palabra "soltamos" el field symbol, dejandolo sin apuntar a nada. Su funcionalidad es parecida al hacer un *CLEAR* a un work area, conceptualmente es distinto. el *CLEAR* vacía el contenido de una variable, mientras que *UNASSIGN* rompe la referencia, para volver a asignarlo después.