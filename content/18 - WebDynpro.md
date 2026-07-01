---
tags:
  - abap
  - sap-hr
  - webdynpro
bloque: "18"
---
# WebDynpro

Para crear una WebDynpro tenemos que acceder a la transacción SE80. Permite desarrollar interfaces Web. Es el modelo estándar de SAP para desarrollar interfaces de usuario *(UI)* basadas en web, basándose en la arquitectura **MVC *(Modelo-Vista-Controlador)***.

- **Vista**: Es la parte interactiva que tendrá el usuario. Contiene todos los elementos de la interfaz gráfica.
- **Controlador**: Su función es conectar la vista con el modelo.
- **Modelo**: Contiene toda la lógica de negocio.

## Componentes de una WebDynpro

![[Pasted image 20260630123259.png]]

### Ventana (Window)
Se trata de un elemento que contiene una o mas vistas. Además en estas ventanas se puede implementar código ABAP *(métodos)* o definir atributos *(variables)* que pertenecen a esta ventana, es decir, son locales. No las podríamos utilizar en otras ventanas. La única pestaña diferente que tiene de la *Vista* es:
	  - **Window**: es propia de la ventana. Nos muestra la estructura de la ventana y es donde insertamos las vistas que va a contener, además de definir los enlaces de navegación **entre** ellas.
	  ![[Pasted image 20260701082852.png|653]]
Además tiene sus propios métodos distintos a la *Vista* y *Component Controller*:
	1. *HANDLEDEFAULT*: recoge los parámetros de la aplicación. Es el método que se ejecuta por defecto al iniciar la aplicación.
	2. *WDDOINIT*: inicialización de la ventana, se ejecuta al crearse en memoria. *Similar al INITIALIZATION*.
	3. *WDDOEXIT*: se ejecuta al salir de la aplicación, libera objetos y variables de la memoria.
	4. *WDDOONOPEN*: se ejecuta al abrir la ventana. Se utiliza cuando la ventana se abre como un popup.
	5. *WDDOONCLOSE*: se ejecuta al cerrar la ventana.
>[!info]
>Cada componente puede tener varias ventanas, pero siempre tiene una ventana principal, que agrupará las distintas vistas y la que se utiliza al arrancar la aplicación.
### Vista (View)
Elemento que se visualiza por pantalla, a su vez, contendrá otros elementos gráficos, como botones, textos etc... En este componente podremos implementar código ABAP y atributos que pertenecen a la vista. Es decir, posee un **controlador local** encargado de procesar las acciones del usuario. Podemos agrupar las pestañas en 4 grupos:
1. **Pestañas de Interfaz y Diseño**
    - **Properties**: Contiene información básica del componente, como su descripción, el nombre de las clases autogeneradas y la asignación de la Clase de Asistencia si la hubiera.
    - **Layout**: Es exclusiva de las vistas *(views)*. Nos proporciona el entorno gráfico donde añadiremos los elementos visuales de las pantallas *(tablas, campos de texto, botones etc...)*.
       ![[Pasted image 20260630125039.png]]
2. **Pestañas de navegación (Presentes en componente Vistas y Ventanas)**
    - **Inbound Plugs**: Puntos de entrada para gestionar la navegación hacia una *Vista o Ventana*. Al crear uno, el sistema genera automáticamente un método *Event Handler* que se ejecutará al entrar a dicha vista.
        ![[Pasted image 20260630125605.png]]
    - **Outbound Plugs**: Puntos de salida de la navegación. No tienen código autogenerado; se disparan por código *(tras una acción, como puede ser pulsar un botón)* para saltar a otra vista.
    ![[Pasted image 20260630131400.png]]
3. **Pestañas de Datos y Variables**
	- **Context**: Nuestro contenedor para almacenar datos temporales, estructurado de forma jerárquica mediante nodos y atributos. Los nodos podrían entenderse como el *padre* y los atributos los hijos. Dentro de una *Vista* este **context** es local y se utiliza el *Data Binding* para enlazar los elementos del *Layout* con los atributos, de modo que cualquier cambio visual se almacene directamente.
	- **Attributes**: Permite declarar variables globales, constantes o referencias a objetos *(instancias de clases)* que se podrán utilizar dentro de los métodos del controlador actual *(vista actual)*.
	Es decir estas variables se podrán utilizar en cualquier *método o action* internos de esa **vista**.
4. **Pestañas de lógica y procesamiento**
	- **Actions**: Se utiliza para definir las acciones que desencadena alguna interacción del usuario con el elemento de la pantalla. El controlador local se encarga de procesar estas acciones creando un método automáticamente para cada una. Haciendo doble click sobre ella, accederemos a su código.
	![[Pasted image 20260630133544.png]]
	- **Methods**: Es donde vamos a escribir toda la lógica de la aplicación. Dependiendo del componente, encontraremos diferentes métodos estándar. En las vistas encontraremos:
	1. *WDDOINIT*: Se ejecuta al crearse la vista en memoria. Es donde preparamos los datos iniciales, cargamos valores por defecto y se rellenan las primeras tablas internas antes de mostrar nada en pantalla. *Similar al INITIALIZATION y START-OF-SELECTION*
	2. *WDDOBEFOREACTION*: Se dispara antes de procesar la acción del botón que hemos pulsado. Sirve para verificar si los datos introducidos en la pantalla son válidos. Si hay un error, el proceso se para. *Similar a AT SELECTION-SCREEN (validación)*
	3. *WDDOMODIFYVIEW*: Se ejecuta antes de pintar la pantalla en la web. Es el único método en el cual podemos modificar las propiedades de los elementos, como ocultar campos, hacerlos obligatorios etc... *Similar al PBO* 
	4. *WDDOEXIT*: Se ejecuta cuando el usuario sale de la aplicación, liberando los objetos y variables de la memoria del servidor.
## Component Controller
Es el controlador global de la aplicación: proporciona datos y métodos accesibles desde todas las vistas. En cuanto a las pestañas la unica diferencia es que tampoco tiene la pestaña *Layout* que tiene el componente *Vista*, ni plugs de navegacion.

![[Pasted image 20260701084450.png]]

La diferencia general está en el *Context*. El de la vista es local (solo se accede desde esa vista), el context del component controller es global. Cuando un dato tiene que usarse en varias vistas, el nodo se debe de crear en el component controller y luego se arrastra a las vistas que lo necesiten.

Este componente tiene sus propios métodos también:
	1. *WDDOINIT*: inicializa el componente, prepararemos todos los datos globales de arranque.
	2. *WDDOEXIT*: se ejecuta al finalizar para liberar objetos y variables de la memoria.
	3. *WDDOPOSTPROCESSING*: preparacion antes de mostrar la pantalla.
	4. *WDDOBEFORENAVIGATION*: se ejecuta antes de navegar entre vistas, usado mayormente para el tratamiento de errores de la aplicación antes de la navegación.
	5. *WDDOAPPLICATIONSTATECHANGE*: gestiona la suspensión y reanudación de la aplicación.

## Atributos

Cuando accedemos en cualquier método de un controlador, SAP nos proporciona de forma automática una serie de atributos predefinidos que empiezan por *WD_*. Se generan de forma automática cuando creamos nuestra webdynpro y nos darán acceso al controlador actual y sus datos. Los atributos principales son:

- **WD_THIS**: Es una referencia a la interfaz local del propio controlador en el que estamos. A través de ella accedemos a los elementos de este controlador como pueden ser sus métodos, atributos y las constantes que representan los nodos del context.
Su tipo cambia según el controlador en el que nos encontremos:
	1. En una **vista**: *WD_THIS* es del tipo *IF_MAIN*.
	2. En una **ventana**: es del tipo *IF_NOMBREVENTANA*
	3. En el **Component Controller**: es del tipo *IF_COMPONENTCONTROLLER*.
- **WD_CONTEXT**: Es una referencia al nodo raíz del context **DEL CONTROLADOR LOCAL**. A través de ella accedemos a los datos almacenados en el context; navegamos por los nodos, leemos y escribimos atributos etc... Es del tipo *IF_WD_CONTEXT_NODE*.
>[!info]
>Mientras que *WD_THIS* nos da acceso al controlador, es decir, a la lógica, *WD_CONTEXT* nos da acceso a sus datos, el context.

En la práctica estos dos atributos trabajan juntos de la mano para acceder a los datos. Usamos *WD_THIS* para obtener la constante que identifica un nodo y *WD_CONTEXT* para navegar hasta ese nodo y trabajar con el.
```abap
" Obtenemos la referencia al nodo del context
	lo_nd_nodo = wd_context->get_child_node(
		name = wd_this->wdctx_nombre_nodo
	).
" Volcamos los datos en el nodo
	lo_nd_nodo->bind_table( 
		new_items = lt_datos
	).
```
* *WD_COMP_CONTROLLER*: Es una referencia al component controller (el global). Su utilidad es poder acceder, desde dentro de una vista, a los métodos y a los datos globales del componente. Como el component controller es el almacén común de toda la aplicación, cuando una vista necesita algo global, accede a el a través de *WD_COMP_CONTROLLER*.

* *WD_ASSIST*: Es una referencia a la ***clase de asistencia (Assistance Class)*** del componente, si es que está definida. Se genera automáticamente solo como los demás atributos generados por SAP, siempre y cuando definamos la clase de asistencia. A través de *WD_ASSIST* llamaremos a los métodos de esta clase desde cualquier controlador.

## ASSISTANCE CLASS (Clase de asistencia)
Es una clase ABAP que asociamos a un componente WebDynpro para escribir en ella la lógica de negocio y los textos de la aplicación <- *(simbolos de texto)*. Su utilidad principal es separar la lógica de la interfaz, de esta forma: los controladores *(vistas, ventanas, component controller)* se centran en gestionar la pantalla, y toda la lógica pesada *(cálculos, lecturas a base de datos etc...)* se saca fuera, a la clase de asistencia.

La clase de asistencia se indica en la pestaña *properties* del componente, ![[Pasted image 20260701093617.png]] Una vez ingresado su nombre y presionar Enter, SAP nos preguntará si queremos crear este objeto, le diremos que si y ya tenemos nuestra clase de asistencia creada con su atributo generado automáticamente.
Dentro del árbol de la WebDynpro podemos consultar que contiene esta clase![[Pasted image 20260701093931.png]]
Los dos usos principales de la clase de asistencia son:
1. **Lógica de negocio**: En vez de escribir el código de consultas a base de datos, aplicar reglas, cálculos etc... dentro de los métodos de la vista, crearemos un método y escribiremos ese código dentro y desde el controlador solo los llamamos.
```abap
	wd_assist->get_data( ).
```
2. **Gestión de textos:** es el sitio recomendado para guardar los textos y mensajes de la aplicacion. Esta clase lleva asociado un repositorio de textos y nos ofrece el método *IF_WD_COMPONENT_ASSISTANCE~GET_TEXT* para recuperarlos por su numero asignado *(001, 002, 003, 004 ...)*, lo que facilita no tener que hardcodear textos.
```abap
	lv_texto = wd_assist->if_wd_component_assistance~get_text( '001' ).
```
