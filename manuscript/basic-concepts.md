# Conceptos básicos

## Test

Un test es una pequeña pieza de software, normalmente una función, que ejecuta otra pieza de software y verifica si produce un resultado o efecto que esperamos.

### Test como especificación

Un test como especificación utiliza ejemplos de uso de la pieza de software probada para describir cómo debería funcionar.

Se opone al test como verificación, propio de la QA, en el que se prueba la pieza de software de manera sistemática para verificar que cumple lo que se espera de ella.

### Test que falla
 
Un test que falla es una especificación que no se cumple todavía porque no se ha añadido el software de producción que permite hacerlo pasar. Típicamente los frameworks de testing lo representan en color rojo.

### Test que pasa

Un test que pasa es una especificación que ejecuta un código de producción que genera el efecto o respuesta esperado.

## Código de producción

En TDD nos referimos con el nombre de código de producción al código que escribimos para pasar los tests y que, eventualmente, acabará siendo ejecutado en un sistema en producción.

## Unidad de software

Unidad es software es un concepto bastante flexible y que hay que interpretar en un contexto, pero se refiere normalmente a una pieza de software que se puede ejecutar de forma unitaria y aislada incluso aunque esté compuesta de varios elementos.

## Subject under test

La unidad de software que es ejercitada en un test.
