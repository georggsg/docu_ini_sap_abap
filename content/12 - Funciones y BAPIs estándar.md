---
tags:
  - abap
  - sap-hr
  - infotipo
  - funciones
  - module-pool
  - bapi
  - bapis
  - alv
bloque: "12"
---
# Funciones HR.

## HR_READ_INFOTYPE

Lee los registros de un infotipo de un empleado y los devuelve en una tabla interna. 
```abap
	CALL FUNCTION 'HR_READ_INFOTYPE' 
	EXPORTING pernr = p_pernr " el empleado 
		infty = '0002' " el infotipo a leer 
		begda = sy-datum " desde qué fecha 
		endda = sy-datum " hasta qué fecha 
	TABLES infty_tab = lt_pa0002. " tabla con los registros leídos
```
>[!warning]
>Aunque no es aconsejable realizar consultas *SELECT* a un infotipo directamente, se puede hacer si es solo para lectura. Si necesitamos datos de algún infotipo, como buena práctica debemos de utilizar la función HR_READ_INFOTYPE.

## HR_INFOTYPE_OPERATION

Crea, modifica o borra registros de un infotipo de forma segura, aplicando todas las validaciones y reglas de consistencia que el sistema de SAP tiene definidas. Las operaciones que podemos indicar son:
- **INS - insertar**.
- **MOD - modificar**.
- **DEL - borrar**.
```abap
	CALL FUNCTION 'HR_INFOTYPE_OPERATION'
		EXPORTING
			infty = '0105'
			number = p_pernr " empleado 
			subtype = '0010' " subtipo (correo electrónico) 
			validitybegin = sy-datum 
			validityend = '99991231' 
			record = lw_pa0105 " registro con los datos 
			operation = 'INS' " operación a realizar 
		IMPORTING 
			return = lv_return. " resultado de la operación
```

## BAPI_EMPLOYEE_ENQUEUE

Bloquea a un empleado para que ningún otro proceso modifique sus datos a la vez. Se llama antes de modificar o insertar alguna operacion en un infotipo.
```abap
	CALL FUNCTION 'BAPI_EMPLOYEE_ENQUEUE' 
	EXPORTING 
	number = p_pernr 
	IMPORTING 
	return = lv_return.
```

>[!info]
>La variable RETURN será de tipo bapireturn1. Nos permitirá manejar la validación de si el empleado esta bloqueado que queremos hacer y si no esta bloqueado que queremos hacer.
>
>DATA: lv_return TYPE bapireturn1


## BAPI_EMPLOYEE_DEQUEUE

Desbloquea al empleado bloqueado previamente. Se llama después de terminar la operación.
```abap
	CALL FUNCTION 'BAPI_EMPLOYEE_DEQUEUE' 
	EXPORTING 
	number = p_pernr.
```

# Salida de datos

## [[10 - Salidas de datos (WRITE y ALV)#ALV (ABAP List Viewer)|REUSE_ALV_GRID_DISPLAY / REUSE_ALV_LIST_DISPLAY]]

## SSF_FUNCTION_MODULE_NAME

Nos devuelve el nombre del módulo de funciones generado automáticamente para un SmartForm.
Ese nombre cambia entre sistemas, es decir, su nombre en el entorno de desarrollo, puede no ser el mismo en calidad o en producción. Para no correr el riesgo de hardcodear ese nombre y que en alguno de los entornos no funcione con esta función le daremos un nombre específico para que siempre sepa a que modulo apuntar el programa cuando se ejecute.
```abap
	CALL FUNCTION 'SSF_FUNCTION_MODULE_NAME' 
	EXPORTING 
		formname = 'ZSFRM1_GSG' " nombre del SmartForm 
	IMPORTING 
		fm_name = lv_fname " nombre del módulo de funciones generado 
	EXCEPTIONS 
		no_form = 1 
		no_function_module = 2 
		OTHERS = 3.
```
Cuando llamemos al módulo de funciones de nuestro SmartForm utilizaremos *CALL FUNCTION lv_fname.* En vez de su nombre hardcodeado.
>[!info]
>La variable lv_fname tiene que ser del tipo rs38l_fnam.
>DATA: lv_fname TYPE rs38l_fnam.


# Interfaz de usuario
