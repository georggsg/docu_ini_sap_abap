---
tags:
  - abap
  - sap-hr
  - salidas
  - write
  - concatenate
  - alv
bloque: "10"
---

# Salidas de datos (WRITE y ALV)

Una vez hemos procesado los datos, tenemos la opción de mostrarlos mediante la sentencia *WRITE* o a través de ALV (ABAP List Viewer). Si queremos mostrar solo el valor de una variable, no necesitamos enredarnos creando un ALV, utilizamos WRITE. Pero, si queremos mostrar los datos de una tabla, utilizaremos ALV. Ya que si usamos WRITE no podremos darle el mismo formato y el código sería muy largo.

## Write

Es la forma más básica de mostrar resultados, el programa escribe el texto en pantalla línea a línea. Es antigua y no muy utilizada actualmente.

```abap
WRITE 'Hola Mundo'. " nos pintará una única línea
```

Si queremos pintar mas de un valor, o variable tendremos que utilizar los dos puntos ":" después de WRITE. Esto nos permitirá poder concatenar valores a mostrar. Cabe destacar que si utilizamos el slash "/" después de *WRITE:* realizaremos un salto de línea.

```abap
WRITE: / 'Hola mundo',
     lv_empleado1,
         lv_edad.
```

## Concatenate

Esta sentencia NO PODEMOS utilizarla para salida de datos, pero si para manipular que datos queremos mostrar en la salida.

Las variables que puede recibir esta sentencia tienen que ser de tipo carácter *(C, N, D, T o STRING)*. No podemos concatenar directamente un número. Para utilizar esa variable que es un numero tenemos que convertirlo (cast) a un tipo carácter. La salida, la variable que vamos a guardar esta concatenación tendrá que ser de tipo string o de tipo c (char) con una longitud que el mensaje almacenado "entre" dentro de la variable.

Si vamos a concatenar una frase, es lógico y más cómodo utilizar la salida de tipo string.

```abap
DATA: lv_edad TYPE i VALUE 30,
  lv_edad_c TYPE c LENGTH 3,
  lv_texto TYPE string.

lv_edad_c = lv_edad.
CONCATENATE 'Edad:' lv_edad_c INTO lv_texto SEPARATED BY space.
WRITE: / lv_texto.
```

Con SEPARATED BY space le estamos diciendo a la variable que guarda la concatenación que estará separada por espacios en blanco. Esto no es obligatorio, si queremos usar algún otro tipo de separador, CONCATENATE admite añadir caracteres entre comillas simples.

```abap
CONCATENATE lv_day '/' lv_month '/' lv_year INTO lv_texto.
```

En las versiones mas nuevas de ABAP podemos concatenar sin necesidad de usar la sentencia.

```abap
DATA: lv_num1 TYPE i,
  lv_num2 TYPE i,
  lv_total TYPE string.

lv_total = |Resultado de la suma : { lv_num1 + lv_num2 }|.
WRITE: / lv_total.
```

## ALV (ABAP List Viewer)

Es la forma mas utilizada y moderna de mostrar datos. Con ALV mostraremos los datos de una tabla interna de dos formas, rejilla *(grid)* o lista *(list)*. ALV se divide en tres elementos clave:

- Tabla interna: Es la tabla que vamos a utilizar como salida, los datos que vamos a querer mostrar.
- Catálogo(fieldcatalog): Es una tabla interna que ya existe en el entorno de SAP. Describe las columnas del ALV; cuantas hay, que campo de datos representa cada una, el título/nombre y en que orden se van a mostrar.

Tenemos que declarar una tabla interna y una estructura para poder crear nuestro catálogo. La tabla interna será del tipo *SLIS_T_FIELDCAT_ALV* y la estructura que será la empleada para darle las propiedades a la tabla será del tipo *SLIS_FIELDCAT_ALV.*

Como norma general la declaración de la tabla y la estructura será la siguiente:

```abap
DATA: i_fieldcat TYPE slis_t_fieldcat_alv, " tabla interna que usaremos para mostrar los datos
  wa_fieldcat TYPE slis_fieldcat_alv. " estructura para crear nuestro catálogo
```

Los campos más utilizados en la estructura para definir sus propiedades son:

- **FIELDNAME:** El nombre del campo en la tabla interna de datos. Es obligatorio y debe coincidir exactamente con el nombre del campo en la tabla.
- **SELTEXT_M/L/S:** El texto de la cabecera de la columna. Si usamos la M será versión media (la más utilizada), la versión L es la larga y la versión S es la corta. Será el titulo/nombre de la columna.
- **COL_POS:** La posición de la columna (el orden en que aparece de izquierda a derecha).
- **OUTPUT_LEN:** El ancho de la columna en caracteres.
- **KEY:** Asignando el valor 'X' marcamos la columna como clave, resaltando y fijando a la izquierda.

Estas propiedades van a ir seguidas de la estructura declarada.

```abap
wa_fieldcat-fieldname = 'PERNR'.
wa_fieldcat-seltext_m = 'Nº personal'.
wa_fieldcat-output_len = 15.
wa_fieldcat-key = 'X'.
APPEND wa_fieldcat TO i_fieldcat.
```

El código anterior sería un ejemplo de una columna, hay que repetirlo tantas veces o columnas necesitemos.

- Layout: Configura el conjunto de la rejilla o lista: aspectos visuales y como se comporta el ALV. *"layout"* es una estructura del tipo *SLIS_LAYOUT_ALV* y tenemos que pasarle la variable declarada con ese tipo al parámetro *is_layout*.
	Los campos más utilizados en layout son:
	1. **ZEBRA**: Asignamos al campo el valor 'X' y nos va a sombrear las filas de forma alterna.
		```abap
		lw_layout-zebra = 'X'.
		```
	2. **COLWIDTH_OPTIMIZE**: También se asigna dando el valor 'X', nos ajustara el ancho de cada columna a su contenido automáticamente.
		```abap
		lw_layout-colwidth_optimize = 'X'.
		```
	3. **WINDOW_TITLEBAR**: Nos permite asignar el titulo de la ventana del ALV.
		```abap
		lw_layout-window_titlebar = 'Vista ALV'.
		```
	4. **NO_TOOLBAR**: Se asigna con el valor 'X'. Nos ocultará la barra de herramientas del ALV.
		```abap
		lw_layout-no_toolbar = 'X'.
		```
	5. **NO_COLHEAD**:  Se asigna con el valor 'X'. Nos ocultara las cabeceras de las columnas.
		```abap
		lw_layout-no_colhead = 'X'.
		```
	6. **EDIT**: Se asigna con el valor 'X'. Hace que toda la rejilla sea editable. Si queremos que solo sea editable un campo, en el catálogo tendremos que decirle a la propiedad *edit* que su valor es X y que se pueda marcar para editar.
		```abap
		lw_layout-edit = 'X'.
		```
	>[!info] 
	Para editar un campo solo, podemos asignar el edit en la declaración de la columna en el catálogo.


	Los campos de layout que referencian a un campo son:
	1. **BOX_FIELDNAME**: Añade una columna de selección (checkbox) al principio del ALV, para que el usuario pueda marcar filas. Las filas seleccionadas quedan con el valor 'X' en el campo indicado.
		*Para utilizar BOX_FIELDNAME, tenemos que declarar un campo con el nombre que queramos del tipo char con longitud 1.
		Después indicaremos en la estructura del layout que hemos declarado el nombre del campo creado.
			```abap
			TYPES: BEGIN OF str,
					pernr TYPE pernr_d,
					nachn TYPE nachn,
					sel(1) TYPE c,
					END OF str.
			lw_layout-box_fieldname = 'SEL'.
			```
	2. **LIGHTS_FIELDNAME**: Muestra una luz de color por fila según el valor que le asignemos al campo: *(1 - rojo)*, *(2 - amarillo)*, *(3 - verde)*. El valor se lo daremos nosotros según la lógica.
		```abap
			TYPES: BEGIN OF str,
					pernr TYPE pernr_d,
					nachn TYPE nachn,
					semaforo(1) TYPE c,
					END OF str.
			lw_layout-lights_fieldname = 'SEMAFORO'
			
			IF ls_empleado-pernr IS NOT INITIAL.
				ls_empleado-semaforo = '3'. " verde
			ELSE.
				ls_empleado-semaforo = '1'. " rojo
			ENDIF.
		```
	Juntando todas estas propiedades y parámetros son las que nos ayudaran a tener una vista personalizada del ALV. Para generar el ALV tendemos que usar la sentencia *CALL FUNCTION REUSE_ALV_GRID_DISPLAY* o *CALL FUNCTION REUSE_ALV_LIST_DISPLAY*, dependiendo de si queremos mostrar la rejilla o una lista. El código que nos encontraremos al llamar esta función desde ![[Pasted image 20260615171126.png]] que nos abrirá la siguiente ventana:
	![[Pasted image 20260615171159.png]]
	En la cual en el campo CALL FUNCTION escribiremos la función a la que queremos llamar.
	El código que nos insertara será el siguiente:
	```abap
		 CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY'  
			    EXPORTING  
			*     I_INTERFACE_CHECK  = ' '  
			*     I_BYPASSING_BUFFER = ' '  
			*     I_BUFFER_ACTIVE    = ' '  
			      i_callback_program = sy-repid  
			*     I_CALLBACK_PF_STATUS_SET          = ' '  
			*     I_CALLBACK_USER_COMMAND           = ' '  
			*     I_CALLBACK_TOP_OF_PAGE            = ' '  
			*     I_CALLBACK_HTML_TOP_OF_PAGE       = ' '  
			*     I_CALLBACK_HTML_END_OF_LIST       = ' '  
			*      i_structure_name   = 'ZSEJER14'  
			*     I_BACKGROUND_ID    = ' '  
			*     I_GRID_TITLE       =  
			*     I_GRID_SETTINGS    =  
			*     IS_LAYOUT          =  
			      it_fieldcat        = i_fieldcat  
			*     IT_EXCLUDING       =  
			*     IT_SPECIAL_GROUPS  =  
			*     IT_SORT            =  
			*     IT_FILTER          =  
			*     IS_SEL_HIDE        =  
			*     I_DEFAULT          = 'X'  
			*     I_SAVE             = ' '  
			*     IS_VARIANT         =  
			*     IT_EVENTS          =  
			*     IT_EVENT_EXIT      =  
			*     IS_PRINT           =  
			*     IS_REPREP_ID       =  
			*     I_SCREEN_START_COLUMN             = 0  
			*     I_SCREEN_START_LINE               = 0  
			*     I_SCREEN_END_COLUMN               = 0  
			*     I_SCREEN_END_LINE  = 0  
			*     I_HTML_HEIGHT_TOP  = 0  
			*     I_HTML_HEIGHT_END  = 0  
			*     IT_ALV_GRAPHICS    =  
			*     IT_HYPERLINK       =  
			*     IT_ADD_FIELDCAT    =  
			*     IT_EXCEPT_QINFO    =  
			*     IR_SALV_FULLSCREEN_ADAPTER        =  
			*     O_PREVIOUS_SRAL_HANDLER           =  
			* IMPORTING  
			*     E_EXIT_CAUSED_BY_CALLER           =  
			*     ES_EXIT_CAUSED_BY_USER            =  
			    TABLES  
			      t_outtab           = lt_itab2  
			    EXCEPTIONS  
			      program_error      = 1  
			      OTHERS             = 2.  
			  IF sy-subrc <> 0.  
			* Implement suitable error handling here  
			    MESSAGE 'Error de lectura' TYPE 'E'.  
			  ENDIF.
	```
	Como podemos comprobar contiene muchos parámetros, pero, la mayoría son opcionales y los utilizaremos según nuestras necesidades.
	Los parámetros que sí son obligatorios son:
	3. **t_outtab**: Es la tabla encargada de la salida de los datos que vamos a querer mostrar.
	4. **i_structure_name**: Igual que creamos nuestro propio catálogo para la vista, podemos utilizar una estructura. Esta estructura tiene que estar en el diccionario de datos *(SE11)*. La vista adoptara todas las columnas como campos tenga la estructura. No nos permitirá utilizar el layout para modificar los campos. Es mucho mas cómodo y rápido si no necesitamos editar nada.
	5. **it_fieldcat**: Le pasamos la tabla a la que hemos hecho el append despues de dar las propiedades a cada columna. En general, siempre sera *it_fieldcat*.
	6. **i_callback_program**: En este parámetro tenemos que indicar el programa en el cual se va a ejecutar el ALV. Tenemos dos opciones:
		1. Llamamos directamente a *sy-repid*.
		2. Declaramos una variable global, del tipo *sy-repid*, le damos el valor de sy-repid *(lv_programa = sy-repid)* y utilizamos esta variable en el parámetro callback.

>[!info]
>Tanto **i_structure_name** como **it_fieldcat** son alternativas la una de la otra. Siempre tendrá que estar una presente.



---
[[09 - Modularización|← Modularización (FORM / PERFORM, INCLUDES, módulos de funciones)]]  ·  [[00 - Índice|Índice]]
