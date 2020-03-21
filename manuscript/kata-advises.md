# Consejos para realizar las katas

Al principio puede ser buena idea asistir a una kata dirigida. Básicamente se trata de una kata que hace una persona experta en forma de *live coding* mientras explica o comenta con la audiencia los distintos pasos, de modo que puedes ver la dinámica en acción. Si no tienes esta posibilidad, que puede ser lo más habitual, es buena idea ver alguna kata en vídeo. En los capítulos dedicados a cada kata puedes encontrar algunos enlaces.

Ante todo, el objetivo de las katas es ejercitar la disciplina TDD, la aplicación de las tres leyes y el ciclo red-green-refactor. El código de producción es lo de menos en le sentido de que no es el objeto del aprendizaje, aunque siempre será correcto si los tests pasan.

Es decir, las katas están diseñadas para aprender a desarrollar software usando los tests como guía y para entrenar el mindset y los procesos de razonamiento y análisis que nos ayudan en esa tarea. Por lo general, desarrollar una buena metodología TDD nos ayudará a escribir mejor software gracias a las restricciones que nos impone.

Obviamente los primeros intentos te llevarán su tiempo, te meterás en caminos aparentemente sin retorno o te saltarás los pasos del ciclo. Cuando ocurre eso, no tienes más que retroceder o volver a empezar de cero. Se trata de ejercicios que no tienen una respuesta correcta única.

De hecho, cada lenguaje de programación, enfoque o entorno de test podrían favorecer unas soluciones u otras. Puedes hacer una kata varias veces intentando asumir diferentes supuestos de partida en cada intento o aplicando distintos paradigmas o condiciones.

Si encuentras puntos en los que puedes elegir diversos cursos de acción, toma nota de ellos para repetir el ejercicio y probar otro camino a ver a dónde te dirige.

En TDD es muy importante centrarse en el aquí y el ahora que nos define cada test que no pasa y no agobiarse por alcanzar el objetivo final. Esto no quiere decir dejarlo de lado o dedicarnos a otra cosa. Simplemente quiere decir que hay que recorrer ese camino paso a paso, y hacerlo así nos llevará a la meta casi sin darnos cuenta, con mucho menos esfuerzo y más solidez.

Si es posible prueba a hacer la misma kata en diferentes lenguajes, incluso en diferentes frameworks de testing. Las dos familias más conocidas son:

* xSpec, que están orientados a TDD y tienden a favorecer el testing mediante ejemplos, proporcionando sintaxis y utilidades específicas. Su hándicap es que no suelen funcionar bien para QA.
* xUnit, que son frameworks de testing más genéricos, aunque más orientados a QA. Sin embargo, puedes usarse para TDD perfectamente.

