---
bloque: "13"
tags:
  - abap
  - sap-hr
  - hcm
  - infotipo
  - infty
  - subtipo
  - subty
  - pa
  - pa20
  - pa30
---
# HR / HCM Infotipos

El módulo sobre el que vamos a trabajar es *HCM* o *HR* y se encarga de gestionar toda la información del personal de una empresa. Esa información no se guarda de cualquier manera sino que se organiza a través de los Infotipos y se manipula con [[12 - Funciones y BAPIs estándar#Funciones HR.| funciones específicas]] nunca con consultas SQL directas.

## Infotipos

>[!tip]
>De cara a los infotipos tenemos dos tipos:
>- **PA *(administración de personal)***: sirven para guardar información sobre el empleado. Se almacenan en tablas **PA + número *(PA0000, PA0001, PA0002...)*** su numeración va del 0000 al 0999. A partir del 1000 son infotipos PD. Se identifican por el campo **PERNR**.
>  Los infotipos PA **personalizados *(Z)*** se comprenden desde el 9000 al 9999.
>- **PD *(planificación y desarrollo de personal)***: sirven para guardar información sobre los *objetos de la organización*, no sobre personas: como puede ser la *unidad organizativa, puestos, funciones etc...* Forman parte de la gestión de la organización *(organigrama de la empresa)*. Se almacenan en las tablas **HRP + número *(HRP1000, HRP1001 etc...***, su numeración comienza desde el 1000 y se identifican a través del **tipo de objeto *(OTYPE)*** y el **ID del objeto *(OBJID)***.
>
>En este documento nos centramos sobretodo en los de tipo **PA**. Que son los más utilizados en el día a día.


Un infotipo PA, es una tabla de base de datos que agrupa ciertos registros para identificar a una misma persona. Cada infotipo tiene campos distintos pero, hay 4 campos que todo infotipo comparte:
- **PERNR**: El numero de personal. El numero que identifica al empleado.
- **SUBTY**: El subtipo del infotipo. Hay ciertos infotipos que disponen de subtipos.
- **BEGDA**: Fecha de inicio de validez de registro.
- **ENDDA**: Fecha de fin de validez de registro.

Las tablas de los infotipos PA se denominan con **PA + nº de infotipo**. Los más habituales son:
1. **PA0000**: Medidas (altas, bajas, cambios...)
2. **PA0001**: Asignación organizativa (área de trabajo, departamento, posición...)
3. **PA0002**: Datos personales (nombre, apellidos, fecha de nacimiento...)
4. **PA0006**: Direcciones.
5. **PA0008**: Emolumentos (retribución/remuneración/salario).
6. **PA0105**: Comunicación (correo electrónico, usuario, teléfono...)

Para visualizar un infotipo PA tenemos que ir a la transacción [[02 - Elementos básicos (Transacciones)#PA20 (Visualizar infotipos PA)|PA20 (Visualizar infotipos PA)]]
Para crear o modificar un infotipo PA tenemos que ir a la transacción [[02 - Elementos básicos (Transacciones)#PA30 (Crear / Modificar infotipos)| PA30 (Crear / Modificar infotipos.]]


## Subtipos

Los subtipos subdividen los infotipos en variantes. Por ejemplo, el infotipo *0105 - comunicación* usa el subtipo para distinguir el tipo de comunicación: *subtipo 0010* es el correo electrónico etc... Entonces, para leer el email de un empleado filtraremos por **subty = '0010'***

## Lectura de infotipos

Para leer datos en un infotipo tenemos dos formas:
1. **SELECT** directo sobre la tabla PA: Solo está permitido para leer.
```abap
	SELECT SINGLE pernr
		FROM pa0002
		INTO lw_pa0002
		WHERE pernr = p_pernr.
```
2. [[12 - Funciones y BAPIs estándar#HR_READ_INFOTYPE|Función HR_READ_INFOTYPE]]: es la forma correcta para leer un infotipo, ya que respeta las autorizaciones y estructura del infotipo.

## Modificación de infotipos

Nunca debemos hacer *INSERT, UPDATE, MODIFY ni DELETE* directamente sobre tablas PA. Funcionalmente se podría, pero, las tablas PA tienen reglas de consistencia, validaciones y dependencias temporales que cualquiera de las operaciones mencionadas se saltarían, dejando datos corruptos.
Para hacer una modificación de un infotipo siempre usaremos la función [[12 - Funciones y BAPIs estándar#HR_INFOTYPE_OPERATION|HR_INFOTYPE_OPERATION]] que aplicará las correspondientes validaciones por nosotros.
Para utilizar *HR_INFOTYPE_OPERATION* tenemos que bloquear el empleado para que nadie pueda realizar ninguna acción sobre el y genere conflictos con nuestro proceso y al terminar de operar con el lo tenemos que desbloquear. Para bloquear el empleado usaremos la función [[12 - Funciones y BAPIs estándar#BAPI_EMPLOYEE_ENQUEUE|BAPI_EMPLOYEE_ENQUEUE]] la cual manejaremos a través de la variable *RETURN* en función de si el bloqueo ha sido exitoso o ha fallado.
El desbloqueo lo realizaremos con [[12 - Funciones y BAPIs estándar#BAPI_EMPLOYEE_DEQUEUE|BAPI_EMPLOYEE_DEQUEUE]] una vez haya finalizado la operacion a realizar en ese empleado.
Por último a tener en cuenta, las modificaciones no se graban en la base de datos hasta que no se confirme, por lo tanto dentro del las validaciones tendremos que utilizar:
- **COMMIT WORK**: confirma y graba los cambios de forma definitiva. Si la variable lv_return tiene el valor de éxito.
- **ROLLBACK WORK**: deshace los cambios realizados desde la última confirmación. Si la variable lv_return tiene el valor de error.

## Bases de datos lógica (LDB)

La base de datos lógica es un programa de ABAP independiente y reutilizable. No es una tabla, ni una vista, ni una función. Es un **programa** que sabe como leer y navegar un conjunto concreto de tablas PA de SAP, aplicar la lógica necesaria para extraer los datos correctos y entregarlos a nuestro report de forma ordenada.
Al ser una base de datos lógica, no almacena datos, si no que sabe como acceder a ellos correctamente. Realiza una lógica que en su defecto tendriamos que realizar nosotros en nuestro report y es reutilizable por cualquier programa que se vincule a ella.
Cada base de datos lógica esta diseñada para un área funcional específica de SAP. 
1. **PNP** entiende de empleados, infotipos **PA**; como leer los infotipos, manejo de fechas y filtrar por criterios siempre relacionados con el personal.
2. **PCH**: entiende de estructura organizativa, está vinculada a los infotipos **PD**; como navegar a través de las unidades organizativas, posiciones y sus relaciones. 
>[!info]
>Ninguna sirve para realizar la lógica de la otra, porque cada una conoce en profundidad sus tablas **PA en PNP** y **PD en PCH** y su propia logica de negocio.

En SAP existe una **LDB** para cada área funcional. Para el area de *HCM/HR*, las principales son **PNP, PNPCE y PCH**. 
### PNP

Es la base de datos lógica del modulo de *HCM/HR*. Trabaja siempre con referencia a el **PERNR**.

PNP gira en torno al empleado individual. Sabe como seleccionar empleados según criterios organizativos y temporales. Sabe como leer sus infotipos de forma correcta y eficiente. En nuestro **REPORT** simplemente declaramos que necesita y la reaccion cuando PNP nos entregue los datos.

Para vincular un PNP a nuestro report, tendremos que ir a la transacción [[02 - Elementos básicos (Transacciones)#SE38 (Report / Programa)|SE38]] e introducir el nombre del programa nuevo para seguido presionar el boton de crear, nos aparecera la pantalla a con los campos a rellenar y al seleccionar el tipo de programa como ejecutable nos aparecera un campo denominado **BD lógica**

![[Pasted image 20260618101747.png]]
En ese campo tendremos que escribir que tipo de *LDB* queremos enlazar a nuestro programa.![[Pasted image 20260618101829.png]]

Si queremos modificar un programa ya existente, debemos introducir el nombre en la transacción [[02 - Elementos básicos (Transacciones)#SE38 (Report / Programa)|SE38]] y marcar el radiobutton de *Atributos* ![[Pasted image 20260618102004.png]]
Acto seguido presionaremos el botón de modificar y nos aparecerá la misma ventana que en la creación de un report nuevo, pero con los campos rellenos.![[Pasted image 20260618102106.png]]
En el campo de BD lógica deberemos introducir el tipo de *LDB* que queramos enlazar al programa
![[Pasted image 20260618102144.png]]

La *LDB* PNP contiene tres componentes internos:
1. **Programa de selección**: el responsable de la pantalla. Define todos los campos de selección que aparecen automáticamente en tu report. Una vez vinculado nuestro report a PNP, esta pantalla de selección se genera y los campos aparecen solos sin haber escrito una línea de código.![[Pasted image 20260618103433.png]]
2. **Estructura**: define la jerarquía de nodos que PNP conoce. En este caso, el nodo principal es **PERNR**. La jerarquía le dice al sistema en qué orden se van a entrega los datos y qué relación tienen entre sí.
3. **Programa de base de datos**: es el código que se ejecuta en tiempo de ejecución de nuestro report. Consulta las tablas físicas, aplica los filtros que hayamos seleccionado, lee los infotipos declarados en nuestro report y dispara el evento *GET* para cada empleado.

Para indicar a el PNP que infotipos tiene que leer utilizaremos en el código la instrucción *INFOTYPES* seguido de el numero de los infotipos que el PNP tiene que procesar:
```abap
	INFOTYPES: 0001,
				0002,
				0105.
```

De esta forma al ejecutar el programa y comience la ejecución del PNP, este sabrá que estructuras de datos tiene que generar. Genera una tabla interna por cada infotipo declarado con todos sus registros válidos y con su estructura exacta de los campos de ese infotipo. 
>[!info]
>Es recomendable declarar solo los infotipos que vamos a utilizar y borrar los no utilzados ya que, los usemos o no, si estan declarados PNP va a generar esa tabla y por lo tanto ralentizara la ejecución del programa.

El flujo de ejecución completo del *PNP*:
1. **Pantalla de selección**: muestra en la pantalla los campos de selección generados automáticamente por el PNP y queda a la espera de rellenar los criterios/filtros.
2. **Selección de PERNRs**: PNP toma esos criterios y consulta las tablas maestras de HCM para construir la lista de empleados que cumplen las condiciones dadas en la pantalla de selección. Es decir, consulta la tabla PA0001 y filtra qué empleados cumplen los criterios que hemos introducido en la pantalla de selección. Esto ocurre antes de que se haya procesado cualquier lógica escrita por nosotros.
3. **Bucle por empleado**: PNP itera internamente sobre esa lista de PERNRs introducidos. Para cada empleado filtrado en el *paso 2* lee las tablas físicas correspondientes, las declaradas en infotypes, filtrando los registros por el periodo de selección. Además, llena las tablas correspondientes que genera respecto a los infotipos declarados. Siguiendo el ejemplo anterior, llenaría las tablas: *P0001, P0002 y P0105*.
4. **Evento GET pernr**: una vez las tablas están rellenas, se dispara este evento el cual nos habilita el acceso a todas las tablas ya cargadas con los datos de ese empleado específico y nuestro código hace lo que sea necesario en ese momento. (lógica nuestra del programa).
5. **Siguiente empleado**: Cuando el evento GET pernr termina, el control se devuelve al PNP que limpia las tablas y carga el siguiente empleado si lo hay, repitiendo el ciclo desde el paso 3.

### Temporalidad

Es uno de los conceptos más importantes en el módulo de *HCM*. Todos los infotipos tienen temporalidad: cada registro tiene ***BEGDA*** y ***ENDDA***.
Para un mismo empleado e infotipo, pueden existir varios registros en distintos periodos de tiempo. En este caso, cuando PNP llena la tabla para un empleado, no mete todos los registros que existen en el infotipo PA para ese **PERNR**. Mete únicamente los registros que cumplan el rango con el periodo temporal que se introdujo en la pantalla de selección.

Es común que un empleado pueda tener mas de un registro si en algún momento ha tenido cualquier cambio organizativo. En cada cambio es un registro nuevo, por ejemplo: si recibe un incremento salarial, su registro vigente finaliza y se crea un registro nuevo con la nueva banda salarial, y así con cualquier cambio.

Es muy importante que no asumamos que nuestra tabla generada por el PNP va a tener un único registro. En algunos casos, hay que recorrer esa tabla para poder recuperar el registro que necesitemos.

### ABAP clásico vs ABAP moderno

- Declaración implícita: En ABAP clásico existía la posibilidad de declarar una tabla interna de forma que el nombre solo tuviera un área de trabajo y una tabla interna bajo una sola declaración, sin necesidad de tenerlas por separado. Se conseguía a través de *WITH HEADER LINE*. Esto a día de hoy sigue ocurriendo en las bases de datos lógicas *PNP*. Internamente generan algo equivalente a:
```abap
	DATA p0001 TYPE TABLE OF pa0001 WITH HEADER LINE
```
No se ve en el código, pero ocurre por debajo. El resultado es que tenemos p0001[] como tabla y p0001 como área de trabajo implícita, sin haberlo declarado nosotros en ningún sitio.
- Declaración moderna: En ABAP moderno, el área de trabajo y la tabla interna son dos objetos independientes con nombres distintos. Los declaramos por separado. De esta forma solucionados los problemas que pueda ocurrir de ambigüedad posible ya que nunca compartirán nombre. Para estructuras internas utilizaremos la nomeclatura *lw_* y para las tablas internas *lt_* si son locales. Si son globales, para la estructura utilizaremos *gw_* y para las tablas internas *gt_*.

Para distinguir la declaración implícita de una moderna en código:
```abap
	LOOP at p0001.
		lv_centro = p0001-werks.
	ENDLOOP.
```
La forma mas clara de identificar es que este LOOP no lleva *INTO* ya que como hemos dicho antes la declaración implícita utiliza el mismo nombre tanto para la estructura como para la tabla interna. Esto ocurre al declarar INFOTYPES, y esta instrucción usa *WITH HEADER LINE* internamente.

```abap
	LOOP AT lt_empleados INTO lw_empleados.
		lv_centro = lw_empleado-werks.
	ENDLOOP.
```
En este caso, este bucle si que lleva incluido un *INTO*, por lo tanto entendemos que es una declaración moderna ya que tenemos por separado la tabla interna de la estructura.

En el único de los casos que utilizaremos la declaración implícita es cuando estemos trabajando con *PNP* ya que el propio sistema de SAP nos obliga a utilizar este método al realizar la operación por debajo. Para cualquier otra situación, utilizaremos la declaración moderna, es decir, separaremos la tabla interna del área de trabajo.

```abap
*&---------------------------------------------------------------------*
*& Report: ZHR_ESQUELETO_PNP
*&---------------------------------------------------------------------*
REPORT zhr_esqueleto_pnp.

* ---------------------------------------------------------------
* INFOTYPES
* Declara para cada número indicado una tabla con WITH HEADER
* LINE de forma implícita. Es sintaxis antigua que PNP usa
* internamente y que en este contexto tienes que aceptar.
*   - P0001[]  → cuerpo de la tabla (todos los registros del
*                empleado actual que solapan con el período
*                de selección introducido en pantalla)
*   - P0001    → header / área de trabajo implícita (el registro
*                que está siendo procesado dentro del LOOP)
* Solo declara los infotipos que vayas a usar realmente.
* PNP carga todos los declarados aunque no los uses.
* ---------------------------------------------------------------
INFOTYPES: 0001,   "Asignación organizativa  → genera P0001
           0002 .  "Datos personales          → genera P0002

* PERNR es la estructura que PNP rellena en cada GET PERNR
* con los datos básicos del empleado que está procesando.
TABLES: pernr.

* ---------------------------------------------------------------
* Variables propias del report.
* Fuera del contexto INFOTYPES, siempre declaración explícita:
* tabla por un lado, área de trabajo por otro.
* ---------------------------------------------------------------
DATA: lv_nombre   TYPE p0002-vorna,
      lv_apellido TYPE p0002-nachn.

* ---------------------------------------------------------------
* INITIALIZATION (opcional)
* Se ejecuta antes de mostrar la pantalla de selección.
* Útil para precargar valores por defecto en los campos
* de selección que PNP genera automáticamente.
* ---------------------------------------------------------------
INITIALIZATION.
  " Ejemplo: precargar fecha actual como período de selección.

* ---------------------------------------------------------------
* START-OF-SELECTION
* Se ejecuta una sola vez antes de que PNP inicie su bucle
* interno sobre los PERNRs. Aquí se cargan tablas auxiliares
* o se inicializan variables que necesites antes del bucle.
* ---------------------------------------------------------------
START-OF-SELECTION.
  " Inicializaciones previas al bucle de empleados.

* ---------------------------------------------------------------
* GET PERNR
* PNP entra aquí una vez por cada empleado que cumple
* los criterios de la pantalla de selección.
* En este momento P0001[] y P0002[] ya están cargadas
* con los registros válidos de ese empleado.
* ---------------------------------------------------------------
GET pernr.

  " CHECK filtra empleados dentro del GET PERNR.
  " Si la condición no se cumple, PNP abandona este PERNR
  " y salta al siguiente de forma limpia.
  " CHECK pernr-stat2 = '3'.  " Solo empleados activos

  " -------------------------------------------------------
  " LOOP AT sobre tabla generada por INFOTYPES.
  " Al usar WITH HEADER LINE implícito, no necesita INTO:
  " cada registro se vuelca automáticamente en el header P0001.
  " Dentro del loop accedes a P0001-campo directamente.
  " -------------------------------------------------------
  LOOP AT p0001.
    " P0001 aquí es el header: el registro actual del loop.
    lv_nombre = p0001-werks.  "centro de trabajo
  ENDLOOP.

  LOOP AT p0002.
    " P0002 aquí es el header: el registro actual del loop.
    lv_nombre   = p0002-vorna.   "nombre
    lv_apellido = p0002-nachn.   "apellido
  ENDLOOP.

* ---------------------------------------------------------------
* END-OF-SELECTION
* Se ejecuta una vez cuando PNP ha terminado de iterar
* sobre todos los PERNRs. Es el lugar correcto para volcar
* resultados finales: ALV, WRITE masivo, totales, etc.
* No lo uses para lógica por empleado, eso va en GET PERNR.
* ---------------------------------------------------------------
END-OF-SELECTION.
  " Aquí iría por ejemplo la llamada al ALV.
```



---
