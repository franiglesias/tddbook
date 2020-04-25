# Conceptos básicos

## Test

Un test es una pequeña pieza de software, normalmente una función, que ejecuta otra pieza de software y verifica si produce un resultado o efecto que esperamos.

### Test como especificación

Un test como especificación utiliza ejemplos de uso de la pieza de software probada para describir cómo debería funcionar.

Se opone al test como verificación, propio de la QA, en el que se prueba la pieza de software de manera sistemática para verificar que cumple lo que se espera de ella.

### Test que falla
 
Un test que falla es una especificación que no se cumple todavía porque no se ha añadido el software de producción que permite hacerlo pasar. Típicamente los frameworks de testing lo representan en color rojo.

### Test que pasa

Un test que pasa es una especificación que ejecuta un código de producción que genera el efecto o respuesta esperado. Los frameworks de testing los suelen representar en color verde.

## Código de producción

En TDD nos referimos con el nombre de código de producción al código que escribimos para pasar los tests y que, eventualmente, acabará siendo ejecutado en un sistema en producción.

## Unidad de software

Unidad es software es un concepto bastante flexible y que hay que interpretar en un contexto, pero se refiere normalmente a una pieza de software que se puede ejecutar de forma unitaria y aislada incluso aunque esté compuesta de varios elementos.

## Subject under test

La unidad de software que es ejercitada en un test. Existe una discusión sobre cuál es el alcance de una unidad. En un extremo se encuentran quienes consideran que una unidad es una función, un método o incluso una clase. Sin embargo, también podemos considerar como unidad bajo test un conjunto de funciones o clases que son testeadas a través de la interfaz pública de una de ellas.

## Refactor

Refactor es un cambio en el código que no altera su comportamiento ni su interfaz. La mejor manera de asegurar esto es que exista al menos un test que ejercita el fragmento de código que se está modificando, de modo que tras cada cambio nos aseguremos de que el test sigue pasando.

Algunas técnicas o patrones de refactor están descritos en recopilaciones como esta de [Refactoring Guru](https://refactoring.guru/refactoring/catalog) o el libro de [Martin Fowler](https://martinfowler.com/books/refactoring.html)

### Refactor automático

Precisamente porque algunos refactors están muy bien identificados y caracterizados ha sido posible desarrollar herramientas capaces de ejecutarlos automáticamente. Estas herramientas están disponibles en los IDE.
