# Enfoques en TDD

La metodología Test Driven Development se basa en un conjunto relativamente reducido de reglas o principios. Una cosa que no se define explícitamente es el modo en que esto puede aplicarse a diferentes situaciones de desarrollo.

Así, por ejemplo, es muy evidente la forma en que podemos dirigir mediante tests el desarrollo de una clase o una función. Una buena parte de las katas de este libro y, en general, las katas de iniciación a TDD, hacen exactamente eso.

El problema viene con el salto al *mundo real*, un momento en el que muchas personas no consiguen encontrar rendimiento a la introducción de TDD en su proceso de desarrollo. 

La cuestión clave es que una historia de usuario no consiste normalmente en desarrollar una clase y listo, sino que lo normal es desarrollar features que implican un conjunto de componentes, incluyendo algún tipo de interfaz al mundo exterior (UI, API), así como use case, entidades y servicios de dominio, etc.

Esto genera una pregunta muy simple: ¿por dónde empezar?

Las diferentes formas de responder a esta pregunta podrían clasificarse en tres, no tan separadas entre sí como se podría pensar. De hecho, no son excluyentes.

## Clásico o Detroit School

Este enfoque recibe ambos nombres debido a ser, por así decir, el modelo original de TDD propuesto por los fundadores del paradigma Extreme Programming (Kent Beck, Ward Cunningham, Ron Jeffries), surgido en el contexto del proyecto Chrysler Comprehensive Compensation System en Detroit.

Habitualmente desde este enfoque un proyecto complejo se abordaría definiendo las unidades de software necesarias y creando cada una de ellas mediante un proceso estándar de TDD.

Por poner un ejemplo muy simplista. Imagina que nuestra tarea es diseñar un endpoint de un API.

Esto supondría crear, al menos, un controlador, un use case, una o dos entidades y sus correspondientes repositorios.

En este enfoque clásico de TDD, una vez determinados los componentes necesarios iríamos creándolos en orden de dependencia, empezando por la entidades de dominio y avanzando hacia afuera. Es decir, si para construir una unidad, necesito usar otra unidad, construiré primero esta última.

Algunos rasgos que caracterizan este modelo son:

* Se testea contra las API públicas de las unidades, lo que se suele llamar *black box testing*
* Hincapié en la fase de refactor, de hecho es la fase en la que se introduce el diseño.
* Minimizar el uso de dobles de test, limitándolos fundamentalmente a las fronteras de arquitectura.
* Desarrollo desde dentro hacia fuera. Prioriza la identificación y desarrollo de la lógica de dominio.
* Se centra en el estado y los outcomes de los objetos y sus métodos.

Este enfoque aporta los beneficios esperables de TDD:

* Trabajar en incrementos pequeños y manejables.
* Generar una red de seguridad de abundantes tests de regresión.
* Posibilidad de refactorizar la implementación con gran seguridad.

En el lado de los inconvenientes habría que señalar:

* Los tests no ayudan realmente a dirigir el diseño, sino la implementación de las unidades. El diseño se hace durante la fase de refactor y puede dar lugar a la extracción de colaboradores de una unidad que se testean a través de la interfaz pública de ésta.
* Se corre el riesgo de crear unidades de software muy grandes, algo que se puede afrontar aplicando refactor de manera intensa, particularmente extrayendo a métodos privados y luego a colaboradores.
* Riesgos de crear funcionalidad no necesaria en las unidades más internas al no tener claras las necesidades de los componentes que dependen de ellas.
* Posibles problemas en la integración de los componentes.

## Outside-in o London School

Su origen está también en la comunidad de extreme programming, pero en este caso la londinense. El nombre viene de que promueve una metodología basada en comenzar a partir de las necesidades de los consumidores de un sistema. 

Un proyecto complejo se abordaría definiendo su interfaz más externa y trabajando hacia adentro, descubriendo o definiendo en el camino las unidades necesarias con la ayuda de dobles.

Algunos rasgos que caracterizan este modelo son:

* Se testean las interacciones entre las unidades, lo que conocemos como *white box testing*. Es decir se hacer aserciones sobre los mensajes que unos componentes envían a otros.
* La fase de refactor es menos importante y el diseño se hace con el test en rojo.
* Los dobles de tests se usan de manera generosa, en cada momento se decide qué colaboradores maneja una unidad y se crean dobles para definir sus interfaces. Las clases reales se implementan posteriormente mediante un proceso TDD clásico. Por esta razón también se conoce este enfoque como *Mockist TDD*.
* El desarrollo va desde fuera hacia dentro.
* Se centra en la comunicación entre objetos, por lo que se podría considerar incluso un enfoque más OOP, en el sentido original de Alan Kay.

Beneficios

* Nos proporciona un enfoque de trabajo que encaja especialmente bien equipos multidisciplinares y tiene más orientación a negocio.
* Reduce o elimina los problemas de integración del producto final.
* Reduce la probabilidad de escribir código innecesario.
* Introduce la consideración del diseño desde el principio del proceso de desarrollo.
* Prestamos más atención a las interacciones entre objetos. Tener que usar primero los dobles para diseñar sus interfaces nos ayuda a que sean más concisas y fáciles de manejar.

Inconvenientes

* El coste del refactor es más alto porque se centran en interacciones y los tests tienden a ser más frágiles por acoplamiento a la implementación.

## Behavior Driven Development

Se podría decir que si empezamos desde aún más afuera con el Outside-in development nos encontramos con [Behavior Driven Development](https://franiglesias.github.io/bdd-business-devel/).

TDD en sus dos escuelas principales es una metodología centrada en el proceso técnico de desarrollar software. Pero BDD da un paso más allá integrando el negocio en ese desarrollo.

Esquemáticamente sigue siendo TDD. Se comienza con un tests y el desarrollo es impulsado por nuevos tests. La diferencia es que en BDD nos preguntamos por comportamientos o features en los que estamos interesadas y las describimos en lenguaje de negocio con ejemplos. 

Estas descripciones se traducen en forma de tests de aceptación y se desarrollan utilizando una metodología bastante similar a Outside-in que, a su vez, utiliza el enfoque clásico de TDD cuando toca implementar las unidades concretas de software. Con todo, el tipo de tests unitarios favorecidos por BDD tienden a usar un estilo "especificación mediante ejemplos" en oposición a aserciones.

En la práctica BDD es Outside-in TDD pero tomando como punto de partida a las personas interesadas en el software y sus necesidades, no los contratos o requisitos técnicos de la implementación.

## Entonces, ¿qué enfoque seguir? Y ¿Cómo aprender TDD a la luz de estos enfoques?

Como se decía al principio del capítulo, el aprendizaje de TDD clásico mediante katas puede ser difícil de transferir a la práctica cotidiana en un problema real de desarrollo. Sin embargo, es un aprendizaje necesario antes de introducirse al enfoque Outside-in que resulta mucho más realista en varios aspectos.

Outside-in no excluye el enfoque clásico, pero lo pone en contexto, mientras nos proporciona un enfoque de diseño dirigido por tests al que se podrían aplicar grosso modo los mismos principios de TDD: empezar con un test, escribir el mínimo código de producción para que el test pase y refactorizar la solución si hay oportunidad.

Es más difícil encontrar katas en las que se pueda usar un enfoque outside-in. Por lo general son más largas y complejas, aunque también es posible adaptar algunas katas clásicas para practicar este enfoque.

Un plan de formación en TDD podría estructurarse de la siguiente forma:

* Iniciación con katas clásicas
* Perfeccionamiento con katas en forma agile-kata
* Katas outside-in
* Perfeccionamiento con agile-kata complejas

## Referencias

* [Does TDD lead to good design](https://codurance.com/2015/05/12/does-tdd-lead-to-good-design/)
* [A case for Outside-in Development](https://codurance.com/2017/10/23/outside-in-design/)
* [Detroit School TDD](https://github.com/testdouble/contributing-tests/wiki/Detroit-school-TDD)
* [London school TDD](https://github.com/testdouble/contributing-tests/wiki/London-school-TDD)
* [Extreme programming: origins](https://en.wikipedia.org/wiki/Extreme_programming#Origins)
* [The failures of "intro to TDD"](http://blog.testdouble.com/posts/2014-01-25-the-failures-of-intro-to-tdd/)
* [Endo-Testing: Unit Testing with Mock Objects (PDF)](https://www2.ccs.neu.edu/research/demeter/related-work/extreme-programming/MockObjectsFinal.PDF)
