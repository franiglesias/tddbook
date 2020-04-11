# Greeting

## Una kata funcional para dominarlas todas

La idea de crear un función pura mediante Test Driven Development me parece muy interesante porque nos fuerza a pensar en un algoritmo que debe evolucionar mientas que lo único que podemos saber sobre él desde el punto de vista del test son sus inputs y sus outputs.

Podríamos decir que eso es lo que ocurre en cualquier desarrollo TDD y es cierto, pero este ejercicio nos fuerza a enfrentarnos a ello sin ayuda. Cuando trabajamos en orientación a objetos podemos contar con variables de instancia o métodos privados, que de alguna manera nos proporcionan varios apoyos. Un método de una clase que puede repartir responsabilidades en otros métodos privados o que puede usar campos para guardar un estado no es una función pura.

Pero una función pura no tiene dependencias y no produce más efectos que su respuesta. Y este es el reto: forzar el cambio del algoritmo a través de tests.

## Historia

Esta kata no es muy conocida. La hemos encontrado en TestDouble, donde se menciona como autor a Nick Gauthier.

## Enunciado

El enunciado de esta kata es muy simple. Se trata de crear una función pura `greet()` que devuelva un string con un saludo. Se le pasa como parámetro el nombre de la persona a la que saludar.

Seguidamente se van añadiendo requisitos que nos obligarán a extender el algoritmo para darles soporte únicamente a través de la entrada y salida de esta función. Para cada requisito se nos proporciona un ejemplo. Son los siguientes:

| Requisitos | input | output |
|:----|:------|:-------|
| 1. Interpolar nombre en un saludo sencillo | "Bob"   | Hello, Bob. |
| 2. Si no se pasa nombre, retornar alguna fórmula genérica  | null  | Hello, my friend. |
| 3. Si nos gritan, contestar con un grito   | "JERRY" | HELLO, JERRY! |
| 4. Manejar dos nombres | "Jill", "Jane" | Hello, Jill and Jane. |
| 5. Manejar cualquier número de nombras, con coma estilo Oxford   | "Amy", "Brian", "Charlotte" | Hello, Amy, Brian, and Charlotte. |
| 6. Permitir mezclar nombres normales y gritados, pero separar las respuestas.  | "Amy", "BRIAN", "Charlotte" | Hello, Amy and Charlotte. AND HELLO BRIAN!|
| 7. Si un nombre contiene una coma, separarlo | "Bob", "Charlie, Dianne" | Hello, Bob, Charlie, and Dianne. |
| 8. Permitir escapar las comas de #7   | "Bob", "\"Charlie, Dianne\" | Hello, Bob and Charlie, Dianne. |

## Orientaciones para resolverla

Parte del interés de esta kata reside en trabajar con un requerimiento cada vez por lo que es importante no adelantarse e ir uno por uno.

La dificultad es resolverla sin crear unidades extra, sólo a través de la interfaz `greet()`. En realidad, cada uno de los requerimientos nos permite construir un test que nos fuerce a extender el algoritmo, aunque podemos crear cuantos tests nos parezcan necesarios.

Por otro lado, es muy importante el paso atrás del que hablábamos en la kata Bowling. Al resolver un requisito, haciendo pasar el test correspondiente, nos veremos en la necesidad de preparar el terreno para poder implementar el siguiente, manteniendo todos los tests actuales pasando. 

En resumen:

* Céntrate en un requisito cada vez, en el orden propuesto.
* Una vez logrado, refactoriza para facilitar la consecución del siguiente requisito.

## Enlaces de interés sobre la kata Greetings

* [Greetings Kata](https://github.com/testdouble/contributing-tests/wiki/Greeting-Kata)

