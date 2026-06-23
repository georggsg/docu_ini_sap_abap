---
tags:
  - abap
  - sap-hr
  - variables
bloque: "03"
---
## Variables

Para declarar una variable utilizaremos la sentencia *DATA*.

```abap
	DATA lv_num TYPE i.
```

Si queremos declarar mas de una variable en una misma sentencia *DATA* añadiremos *":"* al final y separaremos las variables por comas.

```abap
	DATA: lv_num1 TYPE i,
			lv_fecha TYPE sy-datum, " sy-datum es una variable de sistema
			lv_nombre(8) TYPE c.
```

Si la variable que vamos a utilizar es local, siempre comenzará con la siguiente nomenclatura ***lv_*** y si es global con ***gv_***.

## Constantes

Para declarar constantes se seguirá la misma forma que las variables, pero la instrucción es *CONSTANTS*. La única distinción es que las constantes siempre tiene que estar inicializadas con un valor y las variables es opcional inicializarlas.
```abap
	CONSTANTS lc_num TYPE i VALUE 2.
```
```abap
	CONSTANTS: lc_num TYPE i VALUE 4,
				lc_pais(2) TYPE c VALUE 'ES'.
```


