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

![[Pasted image 20260623173939.png]]
Cuando terminemos de configurar cada paso, automaticamente nos saltará al siguiente paso.
1. **Define Object Attributes**: