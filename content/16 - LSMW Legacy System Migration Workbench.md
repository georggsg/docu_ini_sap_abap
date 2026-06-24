---
tags:
  - abap
  - sap-hr
  - lsmw
  - diccionario
bloque: "16"
---
*LSMW* es una herramienta de SAP para migrar datos de forma masiva desde un sistema externo, como puede ser un sistema *legacy*, ficheros excel, csv, txt etc.. hacia el sistema de SAP. Nos evita tener que introducir datos manualmente de uno en uno. Con esta herramienta podremos insertar tantos registros como queramos de una vez.

A *LMSW* le cargamos un fichero, transforma los datos al formato que SAP espera *(tenemos opciones estándar que le indicaremos con que formato se cargaran los datos* y los graba en funcion del tipo de grabación que elijamos en las opciones.

Crearemos la LSMW a través de la transacción [[02 - Elementos básicos (Transacciones)#LSMW|LSMW]].

Esta herramienta se compone de tres elementos clave:

### Estructura
- **Proyecto**: el contenedor general.
- **Subproyecto**: una subdivisión dentro del proyecto.
- **Objeto**: el proceso concreto *por ejemplo: lectura_pernr*. Es donde definiremos los pasos que seguirá el proceso.

>[!info]
>Se puede decir que Proyecto y Subproyecto son solo *"carpetas"* contenedoras del objeto que es el proceso real.

Una vez creado el proyecto, subproyecto y el primer objeto, para acceder al objeto haremos doble click en el *el último del arbol*. 
![[Pasted image 20260623164120.png]]

Si queremos crear un objeto nuevo dentro de ese subproyecto pondremos el cursor en el subproyecto que desamos crearlo y presionaremos el boton *Create Entry* la cual nos abrirar una ventana modal pidiendonos el nombre del nuevo objeto.
![[Pasted image 20260623164711.png]]

Y si queremos crear un subproyecto nuevo, el proceso será idéntico al de crear un objeto, pero el cursor estará puesto sobre el proyecto, que esta en la posición superior de todos.
![[Pasted image 20260623164820.png]]

El cursor se marca con un recuadro rojo en el elemento seleccionado.
![[Pasted image 20260623164916.png]]

### Métodos de carga de datos

Los datos se pueden mediante LSMW se pueden grabar de distintos modos:
- **Batch Input/Direct Input**: simula la introducción de datos por pantalla, como si lo tecleasemos a mano. Método clásico.
- **BAPI**: usa una BAPI estándar para grabar los datos.
- **IDoc**: usa mensajes IDoc.

### Pasos de la LSMW

Una vez estemos dentro del objeto tendremos que completar una lista de pasos hasta llegar a la ejecución final. A través de esta lista iremos estableciendo las estructuras, los campos, ficheros etc... que irán dando forma al proceso final.

![[Memoria SAP HR (vault)/images/Pasted image 20260623173939.png]]
Cuando terminemos de configurar cada paso, automáticamente nos saltará al siguiente paso.
1. **Define Object Attributes**: En este paso eligiremos el método de carga *(Batch Input/Direct Input, Batch Input, BAPI, IDoc)*.
Si elegimos Batch Input tendremos que crear una grabación, que consiste en ejecutar una vez la ntransacción manualmente mientras se registra todos los campos y pasos. Cuando ejecutemos el proceso se repetirá la misma secuencia por cada registro del fichero.
![[Memoria SAP HR (vault)/images/Pasted image 20260624084241.png]]
El segundo icono de la derecha, nos abrira una pantalla en la que nos mostrará las grabaciones que hay. Si no existen podemos crear una desde esa misma pantalla desde la barra superior con el botón de *create record*.
Cuando tengamos la grabación creada veremos un árbol con todos los pasos que seguirá ésta para realizar el proceso. En los campos que vamos a añadir datos veremos otro rectángulo al lado del valor que hemos introducido para crear la grabación. Pondremos el cursor en el rectángulo vacío y le daremos al botón de *Default* en la barra superior.
![[Memoria SAP HR (vault)/images/Pasted image 20260624084654.png]]

Al volver a la pantalla de selección del método de carga al presionar el botón F4 nos rellenará el campo con automáticamente con la grabación que hemos creado. Si tuviésemos mas grabaciones nos abrirá una ventana en la que podremos seleccionar la que queramos.
Al elegir un método de carga, la lista de opciones se actualiza en función del método elegido, en este caso, lo haremos con grabación de Batch Input.
![[Memoria SAP HR (vault)/images/Pasted image 20260624084954.png]]
2. **Define Source Structures**: Definimos las estructuras que vamos a utilizar. Podemos crear una nueva ya que solo creas la estructura o podemos utilizar una ya creada.
![[Memoria SAP HR (vault)/images/Pasted image 20260624085456.png]]
3. **Define Source Fields**: Dentro de cada estructura, definiremos los campos que la van a formar. Si hemos declarado una estructura existente en el paso anterior, nos aparecerán los campos directamente. 
   Tendremos que indicar el nombre del campo, una etiqueta su longitud y el tipo.
![[Memoria SAP HR (vault)/images/Pasted image 20260624085752.png]]
![[Memoria SAP HR (vault)/images/Pasted image 20260624085704.png]]
4. **Define Structure Relations**: Relacionamos las estructuras que hemos creado con la estructura de la grabación.
![[Memoria SAP HR (vault)/images/Pasted image 20260624085949.png]]
5. **Define Field Mapping and Conversion Rules**: Aquí realizaremos el mapeo. Enlazaremos cada campo de nuestro fichero, con su campo del sistema de SAP. También aplicaremos conversiones como, formatos de fecha, valores fijos, mayúsculas etc...
   Para agregar cada enlace, pondremos el cursor en cada campo y accionaremos el botón de la barra superior *Source Fields*. Se puede hacer de uno en uno, pero si tenemos muchos campos, en la barra de herramientas en *Extras* tenemos una opción que es *Auto-Field Mapping* el cual nos enlazará cada campo con el suyo. Lo conveniente es que los campos estén declarados en el mismo orden que esta en el documento que LSMW va a leer.

![[Memoria SAP HR (vault)/images/Pasted image 20260624090653.png]]
6. **Define Fixed Values, Translations, User-Defined Routines**: Permite definir valores fijos, traducciones y rutinas propias reutilizables en el mapeo. En migraciones encillas no suele usarse.
![[Memoria SAP HR (vault)/images/Pasted image 20260624090836.png]]
7. **Specify Files**: Indicamos en que ruta se encuentra el fichero que vamos a cargar, podemos cargarlo desde nuestro ordenador o desde el servidor.
   Una vez elegido el fichero y puesto su nombre, tendremos que elegir el delimitador, esto quiere decir de que forma están los campos separados uno de otro. SAP nos da a elegir entre, **No Separator, Tabulator, Semi-Colon, Comma, Blanks, Other (personalizado)**. En el último mencionado tendremos que indicarle que se utiliza de delimitador. También hay que indicarle si nuestro archivo tiene cabecera o no.
   *Field names at start of file*, tiene cabecera.
   *Field order matches source structure definition*, no tiene cabecera.
![[Memoria SAP HR (vault)/images/Pasted image 20260624091403.png]]
![[Memoria SAP HR (vault)/images/Pasted image 20260624091437.png]]
8. **Assign Files**: Asignamos el fichero que vamos a cargar a la estructura que hemos creado en el paso 2.
![[Memoria SAP HR (vault)/images/Pasted image 20260624091544.png]]
9. **Read Data**: La LSMW lee el fichero y carga su contenido en memoria. Aquí comprobaremos cuántos registros se han leído.
![[Pasted image 20260624091703.png]]
En esta pantalla solo tenemos que ejecutar.
![[Pasted image 20260624091748.png]]
Esta pantalla nos mostrará cuantos registros se han leído.
10. **Display Read Data**: Comprobamos que los datos se han leído correctamente, revisando registro a registro como se han interpretado las columnas.
![[Pasted image 20260624091930.png]]
11. **Convert Data**: Se aplican las reglas de conversion del mapeo indicadas en el paso 5 y transforma los datos del fichero al formato de la estructura creada en el paso 2. 
12. **Display Converted Data**: Muestra una lista para poder revisar como han quedado los datos ya transformados antes de grabarlos. Es la última comprobación.
![[Pasted image 20260624092327.png]]
13. **Create Batch Input Session**: Se genera la sesión de Batch Input con los datos convertidos. Aun no se graba en SAP, solo se prepara la sesión.
![[Pasted image 20260624092350.png]]
14. **Run Batch Input Session**: Ejecutamos la sesión de Batch Input, es donde realmente se graban los datos en SAP, reproduciendo la grabación inicial que creamos en el paso 1 en el método de carga. Este paso abre la transacción *SM35*, donde procesamos la sesión y comprobamos si todos los registros se han grabado o si alguno ha dado error.
>[!info]
>En este paso, podemos ejecutar la sesión en distintos modos: **visible** *(vemos cómo se rellena cada pantalla)*, **solo errores** *(solo se muestra si algo falla)* o **en fondo** *(sin ver nada, el más rápido para cargas grandes)* 

![[Pasted image 20260624092733.png]]
Marcamos el juego de datos y ejecutamos.
![[Pasted image 20260624092758.png]]
Seleccionamos el modo de procesamiento.
Al finalizar nos genera un log, si accedemos podemos comprobar si la carga se ha realizado exitosamente.
![[Pasted image 20260624093031.png]]
