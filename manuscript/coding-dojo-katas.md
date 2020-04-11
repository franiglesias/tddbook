# Coding-dojo y katas

## Kata

En el mundo del software llamamos *katas* a ejercicios de diseño y programación que plantean problemas relativamente sencillos y acotados con los que practicar metodologías de desarrollo.

El término es un préstamo de la palabra japonesa que designa los ejercicios de entrenamiento característicos de las artes marciales. Se atribuye su introducción a Dave Thomas (The Pragmatic Programmer), refiriéndose a la realización de pequeños ejercicios de código, repetidos una y otra vez hasta alcanzar un alto grado de fluidez o automatización.

Aplicado a TDD, las katas persiguen entrenar los ciclos de test-producción-refactor y la capacidad de añadir comportamiento mediante pequeños incrementos de código.

La idea es repetir una misma kata muchas veces. Además de adquirir soltura con el proceso, en cada una de las repeticiones existe la posibilidad de descubrir nuevas

Puedes ejercitarte con katas de forma individual o con otras personas. Una forma sistemática de hacerlo es mediante un *Coding Dojo*.

## Coding-dojo

Un *coding dojo* es un taller en el que un grupo de personas, con independencia de su nivel de conocimiento, realiza una kata de forma colaborativa y no competitiva.

La idea de *Coding Dojo* o *Coder's Dojo* fue presentada en la conferencia XP2005 por Laurent Bossavit y Emmanuel Gaillot.

La estructura básica de un coding-dojo es bastante sencilla:

* Presentación del problema, organización del ejercicio (5-10 min)
* Sesión de código (30-40 min)
* Puesta en común sobre el estado del ejercicio (5-10 min)
* Continua la sesión de código (30-40 min)
* Puesta en común de las soluciones alcanzadas

La sesión de código puede desarrollarse de varias formas:

* **Prepared kata.** Un presentador explica cómo resolver el ejercicio, pero contando con el feedback de las personas presentes. No se avanza hasta no tener un consenso. Es una forma muy adecuada de trabajar cuando el grupo se está iniciando y pocas personas están acostumbradas a la metodología.
* **Randori kata.** La kata se realiza en pairing usando algún sistema para alternar entre el conducto (al teclado) y el copiloto. Los demás presentes colaboran haciendo sugerencias.
* **Hands-on workshop.** Una alternativa es que los participantes formen parejas y trabajen colaborativamente en la kata. A mitad del ejercicio se hace una parada para comentar sobre lo realizado unos minutos. Al final de la sesión se presentan las distintas soluciones en el punto donde hayan llegado. Cada pareja puede elegir el lenguaje de programación preferido, por lo que es una gran oportunidad para quienes quieran iniciarse en uno nuevo.

## Consejos para realizar las katas individualmente

Al principio puede ser buena idea asistir a una kata dirigida. Básicamente se trata de una kata que hace una persona experta en forma de *live coding* mientras explica o comenta con la audiencia los distintos pasos, de modo que puedes ver la dinámica en acción. Si no tienes esta posibilidad, que puede ser lo más habitual, es buena idea ver alguna kata en vídeo. En los capítulos dedicados a cada kata puedes encontrar algunos enlaces.

Ante todo, el objetivo de las katas es ejercitar la disciplina TDD, la aplicación de las tres leyes y el ciclo red-green-refactor. El código de producción es lo de menos en le sentido de que no es el objeto del aprendizaje, aunque siempre será correcto si los tests pasan. Sin embargo, cada ejecución de la kata nos puede llevar a descubrir detalles nuevos y formas diferentes de afrontar cada fase.

Es decir, las katas están diseñadas para aprender a desarrollar software usando los tests como guía y para entrenar el mindset y los procesos de razonamiento y análisis que nos ayudan en esa tarea. Por lo general, desarrollar una buena metodología TDD nos ayudará a escribir mejor software gracias a las restricciones que nos impone.

Obviamente los primeros intentos te llevarán su tiempo, te meterás en caminos aparentemente sin retorno o te saltarás los pasos del ciclo. Cuando ocurre eso, no tienes más que retroceder o volver a empezar de cero. Se trata de ejercicios que no tienen una respuesta correcta única.

De hecho, cada lenguaje de programación, enfoque o entorno de test podrían favorecer unas soluciones u otras. Puedes hacer una kata varias veces intentando asumir diferentes supuestos de partida en cada intento o aplicando distintos paradigmas o condiciones.

Si encuentras puntos en los que puedes elegir diversos cursos de acción, toma nota de ellos para repetir el ejercicio y probar otro camino a ver a dónde te dirige.

En TDD es muy importante centrarse en **el aquí y el ahora** que nos define cada test que no pasa y no agobiarse por alcanzar el objetivo final. Esto no quiere decir dejarlo de lado o dedicarnos a otra cosa. Simplemente quiere decir que hay que recorrer ese camino paso a paso, y hacerlo así nos llevará a la meta casi sin darnos cuenta, con mucho menos esfuerzo y más solidez.

Si es posible prueba a hacer la misma kata en diferentes lenguajes, incluso en diferentes frameworks de testing. Las dos familias más conocidas son:

* **xSpec**, que están orientados a TDD y tienden a favorecer el testing mediante ejemplos, proporcionando sintaxis y utilidades específicas. Su hándicap es que no suelen funcionar bien para QA.
* **xUnit**, que son frameworks de testing más genéricos, aunque más orientados a QA. Sin embargo, puedes usarse para TDD perfectamente.

## Repositorios de katas

* [Katalyst](https://katalyst.codurance.com)
* [Kata-log](https://kata-log.rocks/index.html)
* [Coding dojo](http://codingdojo.org)
* [Codekata](http://codekata.com)
* [Agile kata](http://agilekatas.co.uk)

## Referencias

* [The Programming Dojo](http://www.butunclebob.com/ArticleS.UncleBob.TheProgrammingDojo)
* [What is coding dojo](http://codingdojo.org/WhatIsCodingDojo/)
* [The Coder’s Dojo – A Different Way to Teach and Learn Programming - Abstract](https://link.springer.com/chapter/10.1007%2F11499053_54)
