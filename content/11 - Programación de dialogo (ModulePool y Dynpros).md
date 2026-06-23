---
tags:
  - abap
  - sap-hr
  - module-pool
  - dynpros
  - dynpro
  - pbo
  - pai
  - status
  - estado
  - se51
  - se41
  - se93
  - includes
bloque: "11"
---
# Module Pool, Dynpros (PBO / PAI) y Estados

Los programas de diálogo de tipo *Module Pool* son distintos a los programas ejecutables *Reports*. Están pensados para que interactuemos con pantallas propias. No se ejecutan de manera secuencial, su flujo es marcado el ciclo **PBO - Process before output** y **PAI - Process after input**.
## Module Pool

El Module Pool es el tipo de programa (M), que actúa como contenedor del desarrollo de diálogo: básicamente el código ABAP *(módulos, subrutinas y datos globales),* las pantallas *dynpros* y los estados *status*. Lo único de lo que carece es una "puerta de entrada" para que pueda arrancar por si mismo.
Esa puerta de entrada se la podemos facilitar a través de la [[02 - Elementos básicos (Transacciones)#SE93 (Código de transacción)|creación de código de transacción]].
A diferencia del report, los module pool no se pueden ejecutar directamente, como hemos dicho antes es un contenedor. No dispone de un evento como *START-OF-SELECTION* que lo inicie, son un conjunto de pantallas, por lo tanto, hay que decirle por que pantalla empezar. Eso es lo que nos aporta la creación de código de transacción.

Los module pool suelen contener bastante código, con lo cual se organiza en [[09 - Modularización#Includes|includes]]. Recordamos que SAP genera la estructura de los *includes* automáticamente cuando creamos el programa.

## Dynpros (PBO / PAI)

Una dynpro es una pantalla *(cuando se ejecuta un report o programa)* y cada una se identifica con un número. Por defecto, cuando creamos y ejecutamos un reporte, sea crea una pantalla con el numero 1000. Existen otras pantallas con un numero por defecto, aunque también podemos crear las nuestras propias.
Cada Dynpro se compone de varias partes:
 1. **Atributos**: contiene el número de la pantalla, su tipo y la pantalla siguiente.
 2. **Layout**: el diseño visual con sus campos,textos y botones. Se crea en [[02 - Elementos básicos (Transacciones)#SE51 (Screen Painter / Creación de pantallas)|Screen Painter / Creación de pantallas]]. 
3. **Lista de campos**: las propiedades de cada campo de la pantalla .
4. **Lógica de flujo**: el código que controla el ciclo *PBO/PAI*.

 >[!info]
 >Para mas detalles de los componentes consultar los videos que se han dejado en [[02 - Elementos básicos (Transacciones)#SE51 (Screen Painter / Creación de pantallas)|Screen Painter / Creación de pantallas]]
### Process before output *(PBO)* / Process after input *(PAI)* 

Son los dos eventos principales de una pantalla y que se ejecutan automáticamente.
- **PBO *(Process before output)***: se ejecuta antes de mostrar la pantalla. Se utiliza para preparar los campos con datos a rellenar, establecer el estado de la pantalla y su titulo o activar o desactivar campos.
- **PAI *(Process after input)***: se ejecuta después de que pulsemos un botón o presionemos la tecla Enter. Se utiliza para validar los datos introducidos, ejecutar lógica de negocio y controlar la navegación.
En ambos eventos tendremos que declarar el nombre del proceso que queremos que se ejecute, con su respectiva lógica en un include propio.

Cada dynpro tiene su propio *PBO/PAI*, con la salvedad de la generada automáticamente por SAP al crear el programa.
![[Pasted image 20260617084455.png]]

```abap
	PROCESS BEFORE OUTPUT.
		MODULE status_0100.
	
	PROCESS AFTER INPUT.
		MODULE user_command_0100 AT EXIT-COMMAND.
		MODULE user_command_0100.
```

Al declarar cada proceso, si hacemos doble click sobre el, el propio sistema de SAP nos preguntará si deseamos crear el objeto o en caso de tenerlo ya creado nos llevará a el. En este caso a la lógica de cada modulo. El cual si está creado lo podemos encontrar en el *include status*.
![[Pasted image 20260617084757.png]]

Se recomienda un include de estado por cada pantalla. Es decir, el modulo *PBO* y el módulo *PAI* que estén juntos. 
Dentro de este include escribiremos toda la lógica que tenga que realizar nuestra pantalla.
```abap
	MODULE status_0100 OUTPUT.
		SET PF-STATUS 'STATUS_0100'.
		SET TITLEBAR 'TITLE_0100'.
	ENDMODULE.
	
	MODULE user_command_0100 INPUT.
		CASE ok_code.
			WHEN '&F03' OR '&F12' OR '&F15'.
				LEAVE TO SCREEN 0.
			WHEN 'ZINSERTAR' OR ' '. " Las comillas simples vacias en este caso 
									" indica cuando presionemos la tecla Enter
			" lógica para insertar
		ENDCASE.
	ENDMODULE.
```

Qué es el campo ok_code: Cuando pulsamos un boton o una tecla, el código de la función llega a un campo llamado normalmente ok_code, que se crea solo al definir la pantalla a través del menu layout de [[02 - Elementos básicos (Transacciones)#|SE51 Screen Painter / Creación de pantallas]], el cual lo vamos a leer en el *PAI* con un *CASE* para decidir que hacer en cada caso. Es el mismo concepto que la variable de sistemas *sy-ucomm*.

Dentro del modulo *PAI* podremos validar campos a través de:
- **FIELD (campo)** : validamos un campo en concreto. Si la validación falla, solo ese campo queda listo para que el usuario lo corrija.
- **CHAIN...ENDCHAIN**: agrupa varios campos para validarlos juntos. Si falla, todos los campos del grupo quedan listos para corregirlos.
```abap
	PROCESS AFTER INPUT.
		FIELD p_edad MODULE valida_edad.
* El MODULE valida_edad declaramos su lógica en el include del estado
  
	PROCESS AFTER INPUT.
		CHAIN.
			FIELD: p_fecha_ini, p_fecha_fin.
			MODULE valida_fechas.
		ENDCHAIN.
* El MODULE valida_fechas declaramos su lógica en el include del estado
```

## Estados / Status

El estado de una pantalla es el comportamiento respecto a los botones a mostrar en las barras superiores cuando aparezca esa pantalla. Al crear un programa o report SAP automáticamente genera un estado por defecto.
![[Pasted image 20260616131927.png]]

Al igual que podemos crear nuestros propios estados personalizados a través [[02 - Elementos básicos (Transacciones)#SE41 (Estados / Status)|SE41 Estados / Status]]

---
