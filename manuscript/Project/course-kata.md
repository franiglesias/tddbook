## Course kata

## Historia

La kata Course ha sido diseñada a propósito para este libro. 

El enfoque outside-in se puede aplicar a varias katas que presenten un problema más o menos complejo en el que varios objetos interactúan para resolver el problema. Una de las katas más conocidas sobre este enfoque es la [Bank](https://katalyst.codurance.com/bank) y era la prevista inicialmente para incluir en esta parte.

Sin embargo, después de considerarlo, parecía más conveniente inventar un nuevo ejercicio, de modo que fuese más fácil integrarlo en los objetivos del libro.

## Enunciado

Supongamos que este libro se convierte en un curso de TDD y con el objeto de certificar la participación y el aprovechamiento de las estudiantes diseñamos un sistema de evaluación que requiere completar una serie de tareas. Estas tareas pueden ser de tres tipos (tests, ensayo y katas), cuyo peso por tipo en la nota final es como detalla esta tabla:

| Tipo de tarea | Peso (%) |
|:--------------|---------:|
| Test          | 20%      |
| Kata          | 70%      |
| Ensayo        | 10%      |

Cada prueba se califica en una escala de 0 a 10.

Los estudiantes tienen que haber realizado al menos 1 ensayo, 3 katas y 2 tests para poder obtener la calificación final del curso, la cual será la media ponderada de las notas en cada prueba.

Por ejemplo, una estudiante ha presentado los siguientes resultados:

| Tarea | Tipo | Calificación |
|:--|:--|:--:|
| Test 1 | Test | 6 |
| Kata 1 | Kata | 7 |
| Kata 2 | Kata | 8 |
| Test 2 | Test | 5 |
| Ensayo | Ensayo | 7 |
| Kata 3 | Kata | 7 |

En cada categoría se debe calcular la nota media de las entregas. Esta nota media se multiplica por la ponderación de la categoría y la nota final del curso es la suma de este resultado. Si se cumple el objetivo de haber hecho las entregas mínimas en cada categoría y la nota final es mayor o igual a 5, se considera a la estudiante APTA.

| Tipo | Entregas | Calificación | % | Pond |
|:--|:--:|:--:|:--:|:--:|
| Test | 2 | 5.5 | 20 | 1.1 |
| Kata | 3 | 7.3 | 70 | 5.1 |
| Ensayo | 1 | 7 | 10 | 0.7 |
| **Media del curso** | | | | **6.9** |
|  | | |  | **Apta** |

Si alguno de los dos requisitos no se cumple, se considera NO APTA. He aquí un ejemplo usando unos resultados similares, salvo el hecho de no haber presentado el Ensayo:

| Tipo | Entregas | Calificación | % | Pond |
|:--|:--:|:--:|:--:|:--:|
| Test | 2 | 5.5 | 20 | 1.1 |
| Kata | 3 | 7.3 | 70 | 5.1 |
| Ensayo | 0 | 0 | 10 | 0 |
| **Media del curso** | | | | **6.2** |
|  | | |  | **No Apta** |

El objetivo de la kata es escribir un programa capaz de generar este informe tras recopilar los datos con las entregas y sus calificaciones.

## Orientaciones para resolverla

Sería ideal plantear este ejercicio con el desarrollo de un endpoint de un API y empezar con un test end to end que lo invoque y verifique las respuestas. Lo malo es que eso puede introducir bastante ruido en el ejercicio y si el controlador es lo bastante "fino" no debería introducir ningún aspecto interesante en su resolución. 

Por esa razón vamos a usar como punto de entrada un Use Case.

En Domain Driven Design solemos llamar Use Case a un componente de la capa de aplicación que representa las intenciones de un usuario del sistema y que opera con el dominio para devolver una respuesta o ejecutar un comando.

Por lo general, los UseCase se modelan usando el patrón Command + Command Handler. La parte Command es un objeto inmutable que lleva los datos necesarios y el Command Handler es capaz de recibirlo y usar sus para ejecutar la operación. 

En esta ocasión vamos a resolver las kata usando tres enfoques distintos:

* **TDD clásico**. Desarrollamos una clase Use Case a partir del cual iremos extrayendo colaboradores especializados mediante *refactoring*.
* **TDD outside-in mockista**. Desarrollamos el mismo Use Case decidiendo en cada punto qué colaboradores necesitamos e introduciéndolos primero mediante dobles.
* **TDD outside-in iterativa**. Es un planteamiento similar al anterior, pero en lugar de usar Mocks utilizaremos implementaciones reales de los colaboradores necesarios que se irán desarrollando de forma iterativa. En cierto modo, es un planteamiento mixto.




