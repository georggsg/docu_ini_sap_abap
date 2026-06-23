---
tags:
  - sap-hr
  - abap
  - debugger
bloque: "15"
---
# Debugger

Es la herramienta que nos permite ejecutar un programa paso a paso, observando en cada momento el valor de las variables y el flujo que sigue el código.

La sesión del debugger se puede abrir escribiendo [[02 - Elementos básicos (Transacciones)#Elementos básicos (Transacciones)|/h]] en el campo de código de transaccion o poniendo *BREAK-POINTS* (puntos de ruptura).

## Puntos de ruptura

Es una marca que ponemos en una línea de código para que la ejecución se detenga ahí y se abra el debugger. Existen dos tipos de puntos de ruptura:
1. **Dinámicos (de sesión)**: se ponen haciendo doble clic en el margen de una línea en el editor *(SE38/SE80)*. No modifican el código y solo valen para nuestra sesión. Es la forma habitual.![[Pasted image 20260622113921.png]]
2. **Estáticos**: se escriben dentro del código con la sentencia ***BREAK-POINT***. El programa se detiene siempre que llega a esa línea.
```abap
	BREAK-POINT.
```
De esta forma, siempre se abrirá la sesión del debugger. 
>[!warning]
>Hay que tener ojo al utilizarla ya que podriamos mandar el programa a producción con esta instrucción y cada vez que el cliente utilice el programa le salte el debugger.

Una variante de la sentencia *BREAK-POINT* es ***BREAK usuario***, que solo detiene la ejecución para ese usuario concreto. De esta forma podremos depurar el programa en un sistema compartido y sin afectar a los demás.
```abap
	BREAK STR_CC05. 
```

## [[02 - Elementos básicos (Transacciones)#Elementos básicos (Transacciones)|Activar el debugger sobre una transacción.]]

## Movimiento por el código (teclas de paso)

Una vez estamos dentro de la sesión del debugger nos encontraremos la siguiente pantalla:

![[Pasted image 20260622114627.png]]

Las teclas de paso, las encontraremos en la barra superior, justo debajo del título de la pantalla.
![[Pasted image 20260622114713.png]]
Podremos accionarlas haciendo click sobre ellas o con el atajo que cada una tiene asignado. La lista será de izquierda a derecha según la imágen.
1. **Paso a paso (F5)**: ejecuta la línea actual y, si es una llamada a una subrutina o módulo, entra dentro de ella.
2. **Ejecutar (F6)**: ejecuta la línea actual, sin entrar en las subrutinas; las ejecuta de golpe.
3. **Retorno (F7)**: ejecuta hasta salir del bloque actual (vuelve a quien lo llamó) (subrutinas, módulos y bucles).
4. **Continuar (F8)**: sigue la ejecución hasta el siguiente punto de ruptura o hasta final del programa.
>[!info]
>Entre *paso a paso (F5)* y *ejecutar (F6)* no hay diferencia salvo que haya alguna llamada a subrutinas o módulos harán exactamente lo mismo. Ya que, paso a paso saltará hasta la siguiente línea que este cerrada con un punto *"."* y ejecutar salta a la siguiente línea, que, siempre estará cerrada con el punto *"."*

## Inspeccionar y modificar variables

El debugger nos permite ver en cada paso el valor de las variables, así como el contenido de las estructuras y de las tablas internas, a través de la siguiente ventana en la parte derecha del debugger.![[Pasted image 20260622120602.png]]

Para que se muestra la variable, estructura o tabla que queramos seguir, basta con escribir el nombre o hacer doble click sobre el.
Además podemos modificar el valor de una variable durante la depuración, lo que nos permite probar distintos resultados sin tener que cambiar el código ni volver a ejecutar. La modificación se realizara una vez se haya seleccionado la variable en la pestaña de Modif. nos aparecerá un icono de un lápiz al cual le tenemos que hacer click y nos desbloqueara la pestaña de Val. 
![[Pasted image 20260622121359.png]]
Una vez escrito el nuevo valor le daremos a la tecla Enter o haremos click sobre el disquete de guardado en la parte superior de la pestaña Modif.
![[Pasted image 20260622121510.png]]
