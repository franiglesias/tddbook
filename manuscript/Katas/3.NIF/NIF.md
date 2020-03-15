# NIF

## Comienza por los *sad paths* y aplaza las soluciones

Esta kata consiste en crear un Value Object para representar el NIF, o número de identificación fiscal español. Su forma habitual es una cadena de ocho caracteres numéricos y una letra de control, que nos ayuda a asegurar su validez.

Al tratarse de un Value Object queremos poder instanciarlo a partir de un `string` y garantizar que sea válido para poder utilizarlo sin problemas en cualquier lugar del código de una aplicación.

Una de las dificultades para desarrollar este tipo de objetos en TDD es que a veces no necesitan implementar comportamientos significativos y es más importante asegurarse de que se crean consistentes.

El algoritmo para validarlo es relativamente sencillo, como veremos, pero el interés está sobre todo en cómo librarnos de todas las cadenas de caracteres que no pueden formar un NIF válido.

## Historia

La kata surgió por casualidad al preparar un pequeño taller de introducción a TDD y como parte de un live coding acerca de los beneficios de utilizar la metodología en el trabajo cotidiano.

Al profundizar en este ejemplo se fueron poniendo de manifiesto dos cuestiones que resultan muy interesantes:

* Empezar por tests que descarten los casos inválidos nos permite evitar atacar el desarrollo del algoritmo nada más empezar, quitándolos de en medio y reduciendo el espacio del problema. La consecuencia es que acabamos desarrollando objetos más resilientes, con algoritmos más limpios y contribuye a prevenir la aparición de bugs en el código de producción.
* Se pone de manifiesto el mecanismo de aplazar la solución de cada problema hasta el siguiente test. Es decir, para hacer que cada nuevo test pase introducimos una implementación inflexible que nos permita pasar ese test, pero para que los anteriores sigan pasando nos vemos obligados a refactorizar el código que ya teníamos antes.

## Enunciado

Crear un clase Nif, que será un Value Object para representar el Número de Identificación Fiscal de España. Este número es una cadena de ocho caracteres numéricos, con una letra final que actúa como carácter de control.

Esta letra de control se obtiene calculando el resto de dividir la parte numérica del NIF entre 23 (mod 23). El resultado nos indica la fila en la que consultar la letra de control de la siguiente tabla:

-- Tabla de letras de control del nif

Existe un caso especial de NIF que es el NIE o Número de Identificación para Extranjeros. En este caso, el primer carácter será una letra X, Y ó Z. Para el cálculo del mod 23 se reemplazan por los valores 0, 1 y 2, respectivamente.

## Orientaciones para resolverla

Al tratarse de un Value Object intentaremos crear una clase a la que se le pasa la cadena candidata en el constructor. Si la cadena resulta ser inválida para un NIF el constructor lanzará una excepción, impidiendo que se puedan instanciar objetos con valores inadecuados. Fundamentalmente, nuestros primeros tests esperarán excepciones.

De todas las infinitas cadenas que podría recibir este constructor sólo unas pocas serán NIF válidos, por lo que nuestro primer objetivo podría ser eliminar las más obvias: las que nunca podrían serlo porque tienen el número de caracteres inadecuado.

En una segunda fase, buscaremos controlar aquellas que no podrían ser un NIF por su estructura, básicamente porque no siguen el esquema de ocho caracteres numéricos y una letra final, teniendo en cuanta la excepción de los NIE, que sí podrían tener una letra al principio.

Con esto tendríamos todo preparado para implementar el algoritmo de validación ya que sólo tendríamos que manejar `strings` que "parecen" NIF.

Una cosa que los pasos anteriores nos garantizan es que los tests no empezarán a fallar cuando introduzcamos el algoritmo ya que **nunca** podrían ser válidos. Si comenzamos usando `string` que estructuralmente podrían ser NIFs, aunque los hayamos escrito al azar, podríamos encontrarnos con alguno que casualmente fuese válido y al implementar la parte correspondiente del algoritmo ese test fallaría por la razón equivocada.

Por cierto, encontrar casos validos e inválidos es relativamente fácil. Puesto que se trata de un mod 23, cualquier cadena de siete u ocho `0` acabada en un número de 0 a 22 te servirá, acompañada de la letra correspondiente para casos válidos y de otra cualquiera para casos no válidos.

00000000T es válido
00000000S no es válido

## Enlaces de interés sobre la kata


