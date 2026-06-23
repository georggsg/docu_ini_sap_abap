---
tags:
  - abap
  - sap-hr
  - transacciones
  - se11
  - se37
  - se38
  - diccionario
  - se93
  - se51
  - se41
  - pa20
bloque: "02"
---

# Elementos básicos (Transacciones)

En todo momento trabajaremos en el entorno de SAP, por el cual nos moveremos a las distintas funcionalidades que ofrece a través de las *TRANSACCIONES.*
Si tenemos una transaccion abierta y queremos cerrarla y abrir otra nueva directamente tendremos que escribir en este campo ![[Pasted image 20260617111547.png]]
**/ntransaccion**.
/nse38 <-- ejemplo

Si queremos abrir una ventana nueva y mantener la que tenemos abierta en el mismo campo escribiremos
**/otransaccion**.
/ose38 <-- ejemplo

Si en ese campo escribimos **/h** y ejecutamos un programa, se nos abre el debugger sin haber puesto ningun break-point.

Hay una multitud de transacciones en SAP, no obstante, al comienzo utilizaremos una pequeña parte de ellas:

## SE11 (Diccionario de datos)

Una de las transacciones que más vamos a utilizar, ya que desde ella podremos visualizar y crear los elementos clave y predeterminados de SAP para utilizar en un programa.
![[Pasted image 20260616092207.png]]

A través de los 3 botones inferiores *(visualizar, modificar y crear)* podremos realizar las tareas únicas y básicas.
![[Pasted image 20260616092218.png]]

- **Tabla de bases de datos:** Tablas definidas en el diccionario de datos y que existen físicamente en la base de datos del sistema. Sus datos son persistentes en el tiempo. Son accesibles desde cualquier programa. Para la creación de la tabla de base de datos tendremos que rellenar la descripción y la *clase de entrega* que normalmente es el tipo ***A***.
![[Pasted image 20260616092227.png]]

En la pestaña *campos* declararemos los campos y su tipo, también, si ese campo será *clave (clave primaria)* y si tendrá un valor inicial.
![[Pasted image 20260616092240.png]]

- **Tipos de datos:** Podremos consultar elementos de datos, estructuras y tablas existentes o incluso crearlas en el diccionario de datos.
![[Pasted image 20260616092250.png]]

## SE37 (Módulo de funciones)

Para entender el módulo de funciones, hay que explicar primero el *grupo de funciones.*

Los *grupos de funciones* **SON CONTENEDORES** para los módulos de funciones. Son necesarios para poder crear un módulo de función y comparten datos globales entre los módulos de función que contienen. Para poder crear un grupo de funciones tenemos que acceder a la transacción ***SE80.***

A través de esta transacción podremos crear funciones para poder ser llamadas desde cualquier programa con la sentencia *CALL FUNCTION*. Es obligatorio que un módulo de funciones pertenezca a un grupo de funciones.
![[Pasted image 20260616092319.png]]

Al crear un módulo de función nos pedirá asignarle un grupo de funciones al que va a pertenecer y una descripción breve.
![[Pasted image 20260616092327.png]]

Dentro del módulo de funciones encontraremos las pestañas *IMPORT, EXPORT, CHANGING, EXCEP Y COD.FTE.*

- **Import:** En esta pestaña añadiremos todas las variables que queramos que al llamar a la función desde cualquier programa nos pida asignarle un valor a través de una variable del mismo tipo. Se podría resumir en que es como un *(input).*
- **Export:** En esta pestaña añadiremos todas las variables que queramos que al llamar la función nos entregue un valor asignándole una variable del mismo tipo de nuestro programa. Se podría resumir en que es como un *(output).*
- **Changing:** En esta pestaña añadiremos todas las variables que van a cambiar el valor en algún momento de ejecución de la función. Al llamar a la función desde el programa le asignaremos una variable del mismo tipo. Se podría resumir en que es como un *(input/output).*
- **Excep:** En esta pestaña añadiremos nuestras propias excepciones para poder dar mas información cuando la función falle.
- **Cod.fte:** Esta pestaña contendrá toda la lógica de negocio que realizará la función en (background) al ser llamada desde el programa.

Destacamos que las pestañas de *import, export* se denominan así cuando creamos la función en la transacción SE37, pero, cuando esa función es llamada desde el programa, el *IMPORT PASA A SER EXPORT Y EL EXPORT PASA A SER IMPORT.*

Este cambio de vista ocurre porque IMPORTING y EXPORTING siempre se escriben desde el punto de vista de la función. Al crear el módulo, definimos qué recibe (IMPORTING) y qué devuelve (EXPORTING). Al llamarlo desde el programa, la perspectiva se invierte: lo que el programa coloca bajo EXPORTING es lo que envía a la función, y se corresponde con los parámetros IMPORTING del módulo; mientras que lo que coloca bajo IMPORTING es lo que recibe de la función, es decir, sus parámetros EXPORTING.

## SE38 (Report / Programa)

A través de esta transacción crearemos, modificaremos y visualizaremos programas.
![[Pasted image 20260616092339.png]]

Ingresamos el nombre en el campo programa y seleccionamos la acción que queramos realizar.

Al crear un programa tendremos que indicar un titulo y seleccionar el tipo de programa y su estado.
![[Pasted image 20260616092348.png]]

La mayor parte de las ocasiones el programa tipo será ejecutable y el estado será el seleccionado en la imagen.
![[Pasted image 20260616092357.png]]


Estos son los tipos que podemos crear, pero, se recomienda que si no es programa ejecutable, los demás se creen desde la transacción **SE80.**

## SE93 (Código de transacción)

A través de esta transacción podremos crear códigos de transacción para enlazar nuestro report o programa de dialogo para que el usuario tenga acceso. Ya que en la mayoría de las ocasiones el usuario final no tiene forma de acceder a la transacción *SE38* ya que solo suele estar activa para los usuarios desarrolladores.
Introduciremos un nombre descriptivo para nuestro código de transacción y presionaremos el botón de *Crear*. Evidentemente, después de ser creado o incluso códigos de transacción ya creados podremos visualizarlos o modificarlos.
Nos aparecerá las siguiente pantalla para rellenar obligatoriamente el *texto breve* y *el objeto inicio*
![[Pasted image 20260616095627.png]]

- **Programa y dynpro (transacción de diálogo)**: Es la opción que elegiremos cuándo queramos crear un enlace a un [[11 - Programación de dialogo (ModulePool y Dynpros)#Module Pool|Module Pool]]. 
![[Pasted image 20260616095903.png]]
Tendremos que indicarle el programa/module pool y el nº de dynpro por la que vamos a comenzar al ejecutar el código de la transacción creado.
- **Programa e imagen de selección (transacción de report)**: Opción que elegiremos cuando queramos enlazar el código de transacción a un programa[[07 - Eventos (report)| report]].
![[Pasted image 20260616100313.png]]
Tendremos que rellenar únicamente el programa *(nombre del report)* al que queramos enlazarlo ya que todos los report por defecto SAP los crea con la dynpro 1000.

## SE51 (Screen Painter / Creación de pantallas)

En esta transacción podremos crear una pantalla propia para nuestro programa. Para poder crearla, como obligación nuestra pantalla tiene que ir enlazada a un programa.
![[Pasted image 20260616114512.png]]

Una vez indicado el nombre del programa al que queramos enlazar la pantalla, en *nº dynpro* escribiremos el número que queramos *como buena práctica se utilizan números redondos: '0100', '0200', '0300' etc...* menos el 1000 o el 0.
>[!info]
>Las dynpro nº 1000 y 0 estan reservadas ya que se generan automáticamente por SAP. 
>nº 1000: Se genera en cuanto creamos un report o programa y lo ejecutamos.
>nº 0: No es una pantalla real, pero queda reservada para utilizarla en *LEAVE TO SCREEN 0* para abandonar la pantalla en la que nos encontramos y volver hacia atrás.

Una vez presionado el botón de crear se nos abrirá una ventana de *Atributos* en la cual le indicaremos una descripción breve de nuestra pantalla y si fuera necesario, cambiaremos el tipo de dynpro (*pantalla*) al que necesitemos.
Para que la sentencia *LEAVE TO SCREEN 0* funcione como nosotros queremos, en *otros atributos* tendremos que indicar cual es la siguiente dynpro *(pantalla)* a la que va a navegar el programa cuando ejecutemos la sentencia. Esta opción no es obligatoria de cambiar ya que hay otras formas de salir de la pantalla.
![[Pasted image 20260616115553.png]]

Tipos de Dynpro:
1. **Normal**: Es la pantalla estándar y más habitual. Ocupa todo el área de trabajo es la utilizada para la mayoría de pantallas.
2. **Subscreen *(subpantalla)***: Está diseñada para aparecer dentro de un área de otra pantalla, no para mostrarse por sí sola. Se puede llamar desde la pantalla principal usando la sentencia *CALL SUBSCREEN*.
3. **Ventana diálogo modal**: Es una ventana emergente *(popup)*, que aparece encima de la pantalla actual y **bloquea** la interacción con el resto de pantallas hasta que la cerremos. Su uso mas habitual es para confirmaciones.
4. **Dynpro selección**: Es la pantalla que SAP genera automáticamente a partir de los *PARAMETERS* o *SELECT-OPTIONS*. Por defecto SAP la genera con el nº 1000

Consultar los siguientes videos para profundizar mas sobre las propiedades de la creación de una pantalla:
[6 - Module Pool Programming - Screen Painter Part1](https://www.youtube.com/watch?v=hBy35HXOd0Y)
[7 - Module Pool Programming - Screen Painter Part2](https://www.youtube.com/watch?v=cEGKrg3FgKo)
[8 - Module Pool Programming - Screen Painter Part3](https://www.youtube.com/watch?v=SAepsYFBd3Q&t=39s)
[9 - Module Pool Programming - Screen Painter Part4](https://www.youtube.com/watch?v=ysbpU3ArNaA)

## SE41 (Estados / Status)
En esta transacción crearemos un estado propio para la/las pantallas del programa o reporte. Si el estado ya esta creado podremos visualizarlo o modificarlos.
![[Pasted image 20260616133541.png]]
El estado siempre irá enlazado con un programa o report. 
Para agilizar la creación del estado podemos copiarlo de uno de los muchos estándar que tiene SAP. Para eso presionaremos el botón ![[Pasted image 20260616133746.png]]
y se nos abrirá la siguiente pantalla:
![[Pasted image 20260616133817.png]]
En la cual tendremos que indicar desde que programa y estado queremos copiar a que programa y que estado. El estado destino tiene que estar creado para poder copiar uno existente.
>[!info]
>Para estados personalizados para [[10 - Salidas de datos (WRITE y ALV)#ALV (ABAP List Viewer)| ALV]] podemos utilizar el **Programa**: *SAPLKKBL* y **Estado:** *STANDARD*.
>El único inconveniente es que tienes muy pocos espacios para agregar botones propios y aunque borres algún botón de los estándar SAP no los va a detectar ya que el espera "ese" botón estándar específico.

Si queremos crear nuestro propio estado, con los botones justos y necesarios, dentro del estado podremos observar la siguiente pantalla:
![[Pasted image 20260616134447.png]]

Para desplegar ambas barras: *Barra pulsadores* y *Teclas de función* tendremos que presionar el botón ![[Pasted image 20260617082822.png]]

Los menús que nos vamos a encontrar están los botones copiados del estado *STANDARD* y a mayores tenemos 2-3 huecos libres para agregar nuestros botones.
![[Pasted image 20260617082928.png]]

En este caso tenemos nuestro botón personalizado *ZINSERTAR*.
Si vamos a añadir un boton creado por nosotros en el campo *código func* tendremos que escribir el nombre EXACTO con el cual declararemos la lógica del botón, y, hay que rellenar el nombre en mayúsculas.
![[Pasted image 20260617083101.png|697]]

Además de tener que rellenar el campo de *Texto función* con una breve descripción y rellenar el campo *Icono* con el icono que queremos que se muestre.
>[!warning]
>Si queremos ver que iconos existen predeterminados en SAP, colocamos el cursor en el campo de icono y presionamos la tecla F4, para desplegar la ventana que nos ayudara a buscar un icono introduciendo un nombre identificativo y presionando el botón de los prismáticos si no sabemos el nombre exacto del icono que queremos. Si en la primera página no aparece el icono que queremos, presionaremos el botón de la derecha *prismáticos con una cruz* para pasar a la siguiente página de lista de iconos.
>
![[Pasted image 20260617083409.png]]

## PA20 (Visualizar infotipos PA)

A través de esta transacción podremos visualizar los datos maestros de una persona. La pantalla que vamos a ver al entrar en esta transaccion es: 
![[Pasted image 20260617112019.png]]

Obligatoriamente para poder visualizar cualquier dato, tendremos que introducir el **Nº personal** que equivale al campo **PERNR**.

Cuando introduzcamos un **PERNR** válido obtendremos en la misma pantalla una vista rápida de ciertos datos:
![[Pasted image 20260617112229.png]]

Tenemos dos opciones de elegir que infotipo queremos consultar. A través de la bloque:
![[Pasted image 20260617112426.png]]

El cual tenemos un limite de infotipos a consultar.

O a través de la selección directa, el campo que hay debajo de este bloque:
![[Pasted image 20260617112527.png]]

Para elegir el infotipo en este campo podemos presionar el boton F4 para que se nos abra la ventana de ayuda de búsqueda y ver todos los infotipos que existen o escribir el nº de infotipo directamente en el campo.
El campo **C1** sirve para indicar el subtipo, si lo tiene, que queremos consultar. Por ejemplo, el infotipo 0105 subtipo 10 podremos consultar el correo electrónico del nº personal que hemos elegido.
![[Pasted image 20260617112732.png]]
En el campo **C1** tendremos que introducir los 4 dígitos, si por ejemplo, ponemos solo 10, no nos detectara ningún subtipo de ese infotipo.

Para visualizar los registros de ese nº personal, tenemos dos opciones:
1. **Visualizar el último registro**: Para visualizar el último registro de esa persona en la parte superior de la ventana, presionaremos el siguiente botón: ![[Pasted image 20260617112918.png]]  y nos abrirá una pantalla como la siguiente:

![[Pasted image 20260617113019.png]]

>[!info]
>Esta pantalla será distinta en función del infotipo y/o infotipo/subtipo que queramos visualizar.

2. **Visualizar todos los registros existentes de ese infotipo**: Para visualizar todos los registros existentes de esa persona en ese infotipo, presionaremos el botón ![[Pasted image 20260617113148.png]] y nos abrirá una pantalla como la siguiente:
![[Pasted image 20260617113229.png]]

## PA30 (Crear / Modificar infotipos)

La navegación funciona exactamente de la misma forma que la [[02 - Elementos básicos (Transacciones)#PA20 (Visualizar infotipos PA)| PA20 (Visualizacion infotipos)]]
salvando que en está transaccion además de visualizar, puedes crear un registro en el infotipo seleccionado al nº personal. Este registro se crea presionando el botón ![[Pasted image 20260617115413.png]] después de haber introducido el nº personal y el infotipo. Se nos abrirá la siguiente pantalla:
![[Pasted image 20260617115525.png]]

En la cual rellenaremos los campos necesarios y presionaremos el botón Enter del teclado para validar los datos que hemos introducido sean correctos y con el botón de grabar ![[Pasted image 20260617131857.png]] o *(CTRL + S)* para insertar el registro en el infotipo manualmente.

## SMARTFORMS

A través de esta transacción accederemos a la creación, visualización y modificación de smartforms.
![[Pasted image 20260618151150.png]]
Para crear un smartform nuevo, introducimos un nombre y presionamos el boton de crear.
Para visualizar o modificar un formulario, introducimos el nombre del formulario existente y presionamos el botón que necesitemos.
Al acceder a un smartform ya sea nuevo o existente nos encontraremos con la siguiente pantalla:

![[Pasted image 20260618152707.png]]

Los componentes que vamos a encontrarnos son:
	1. **Interfaz**: los parámetros que el SmartForm recibe del programa *(import, export, excepciones)*. Es como la interfaz de un módulo de funciones: define que datos le pasamos.
	2. **Definiciones Globales**: variables, tiposs y estructuras que vamos a utilizar en todo el formulario.
	3. **Páginas**: el formulario se divide en páginas y cada una contiene ventanas.
	4. **Ventanas**: son las áreas de la página donde colocamos el contenido.
	5. **Nodos**: dentro de las ventanas insertamos nodos según lo que queramos, texto, tablas, bucles *(loop)*, condiciones, gráficos etc...

[1 - Smartforms - Introduction](https://www.youtube.com/watch?v=xAgcimaCFXQ&list=PLM3lhmHjSeISdGKM2GJQHY9vgFE86ejY0)

A través de este enlace se accede a una amplia lista de reproducción que explica al detalle como trabajar con los smartforms.

## LSMW

A través de esta transacción podremos crear un proceso de LMSW. La pantalla que se nos abrirá será la siguiente:

![[Pasted image 20260623161830.png]]
Para crear un LSMW tendremos que rellenar los tres campos *project, subproject* y *object*
Una vez introducido presionaremos el boton en la barra superior que se llama *Create Entry*, la hoja en blanco ![[Pasted image 20260623162512.png]], nos pedirá una descripción y ya tendremos creado el LSMW.
![[Pasted image 20260623162545.png]]

Una vez creado, tendremos la siguiente vista del proyecto el cual se divide en 3 posiciones, la mas alta es el proyecto, la intermedia es el subproyecto y la última es el objeto.
![[Pasted image 20260623162922.png]]



---
[[01 - Introducción|← Introducción]]  ·  [[00 - Índice|Índice]]  ·  [[04 - Tipos (TYPES)|Tipos (TYPES) →]]
