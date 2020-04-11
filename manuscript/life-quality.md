# TDD y calidad de vida (la tuya)

Ya sea como empleados o freelance vendemos nuestro tiempo y trabajo a empresas y clientes. Una cosa que distingue nuestra profesión de ortas es el hecho de que vendemos trabajo intelectual. Incluso, a veces, trabajo intelectual de alto nivel.

Así que cuidar de nuestra mente e inteligencia parece ser una actividad razonable que deberíamos practicar con frecuencia.

Hay mucha gente en el mundo del desarrollo de software que piensa, o incluso afirma, que hacer testing es duro o caro, y eso sin mencionar el Test Driven Development. Pero lo que queremos demostrar es que TDD es el camino más recomendable si quieres tener una vida más sana en el campo del desarrollo de software.

Pero primero, veamos un par de cuestiones acerca de cómo funciona nuestro cerebro.

## Conocimiento en el mundo, conocimiento en la cabeza

¿Sabes utilizar una puerta? ¿Seguro? ¿Has visto alguna vez una puerta con manual de instrucciones? Yo sí: todas esas que tienen letreros indicando si se debe empujar o tirar.

¿Te has visto alguna vez ante una puerta cerrada sin saber cómo abrirla? Yo sí. De hecho hay montones de ella por ahí.

El caso es que una puerta debería ser algo fácil de usar y no siempre sucede. Cómo se usa una puerta debería ser obvio, ¿no?

¿Y qué tal si hablamos de interruptores? Me refiero a esos paneles de interruptores cuya disposición no está relacionada con la de las luces que controlan. A veces están situados en lugares en donde no se pueden ver las lámparas y necesitas probar varias veces has encontrar la combinación secreta que enciende la luz que quieres.

La relación entre un interruptor y la lámpara que controla debería ser obvia, ¿no?

Cuando hablamos de algo obvio, nos referimos a conocimiento que no deberíamos tener que buscar en nuestra cabeza. El conocimiento está ahí, en el mundo. Sólo tenemos que usarlo mientras hacemos otras cosas. Queremos poder abrir puertas y encender luces sin tener que pensar en ello.

Por eso, cuando nos vemos obligadas a pensar en cosas que deberían ser obvias, estamos desperdiciando parte de nuestro recursos mentales, utilizando espacio en nuestra memoria de trabajo que preferiríamos estar empleando en otros propósitos.

El conocimiento está en el mundo cuando todas las pistas que necesitamos para usar o interactuar con un objeto están presentes en el objeto mismo. Por eso no necesitamos preocuparnos, razonar o recordar cómo utilizarlos. Cuando necesitamos hacerlo, es decir cuando necesitamos razonar o recordar instrucciones, tenemos que poner el conocimiento en nuestra cabeza para poder alcanzar nuestro objetivo.

Por esto, si disponemos de más conocimiento en el mundo cuando ejecutamos una tarea, necesitamos menos conocimiento en nuestra cabeza, dejando espacio libre en ella que podemos usar para pensar mejor acerca de lo que estamos haciendo.

Cuanto menos tengamos que pensar en cómo usar las herramientas, más podemos pensar el lo que estamos haciendo con ellas.

Pero, ¿de cuánto espacio disponemos en nuestra memoria de trabajo?

Bueno…, el caso es que no mucho.

## La capacidad de nuestra memoria de trabajo

Disponemos de una capacidad de almacenamiento prácticamente infinita es nuestra memoria. Piensa en ella como un enorme e inteligente disco duro que puede guardar recuerdos y datos durante años. No se trata de un almacén pasivo. De hecho, reconstruye nuestra memoria para guardar y recuperar cosas. Esto es importante, porque cuando lo hacemos, necesitamos usar nuestra memoria de trabajo para mantener los datos que estamos usando. Muy parecido a un ordenador.

Sin embargo, nuestra memoria de trabajo es bastante diferente de nuestra memoria a largo plazo. Hay quien la llama "memoria a corto plazo" y hay quien "memoria de trabajo". Creo que podemos verla como un procesador, con  algunos registros que pueden almacenar una cantidad limitada de unidades de información llamadas *chunks* mientras trabaja. Los *chunks* pueden tener un tamaño variable, pero son unidades significativas.

¿Puedes recordar un número de teléfono? Me apuesto a que ha agrupado los dígitos de modo que sólo tines dos o tres números que retener.

Esto es porque nuestro procesador puede manejar un número limitado de chunks. Este número es aproximadamente 7 (más o menos dos). Es algo que varía con la edad y entre los individuos, pero es una aproximación muy buena. Por tanto, intentamos ahorrar tantos registros como podemos, agrupando la información en *chunks*, y manteniendo alguno de los registros libre.

¿Qué sucede ti llenamos todos los registros? Pues la precisión y la velocidad al realizar la tarea disminuyen, aumentando los errores. En general, el desempeño es peor si tratamos de mantener muchas cosas en nuestra memoria de trabajo al mismo tiempo.
 
Por supuesto, se trata de una sobre simplificación. Sin embargo, creo que puedes hacerte una idea. Podemos reducir la sobrecarga si ponemos el conocimiento el mundo, en lugar de mantenerlo en nuestra cabeza, con lo que nuestro desempeño será mejor en cualquier tarea.

Puedes poner conocimiento fuera de la memoria de trabajo mediante la práctica. Eso es lo que ocurre cuando introducimos una nueva técnica, una novedad del lenguaje o una nueva herramienta. Al principio vamos lentos y cometemos errores. Necesitamos tiempo para automatizar cosas en nuestra mente mientras ponemos conocimiento en el mundo.

En nuestro trabajo necesitamos gestionar tareas que no pueden ser automáticas y que son nuevas cada vez.

Pero ahora, es hora de volver al objetivo principal de este artículo. Hablemos acerca de nuestra vida como desarrolladora.

## Un día en la vida

Analicemos por un momento qué sucede cuando programamos sin tests.

De hecho, en realidad siempre hacemos test, pero tendemos a que sean manuales. Lo llamamos "depurar". Usamos un proceso de prueba y error: ¿Esto funciona? ¿No...? Prueba otra vez. ¿Sí...? Sigue adelante.

Intentamos escribir código y verificar que funciona al mismo tiempo que lo escribimos, hasta que nos parece que está terminado. Tras eso, intentamos verificar que el código funciona como un todo. Entonces descubrimos que habíamos olvidado algunos detalles... Tras eso, desplegamos y descubrimos nuevos detalles que no funciona, así que necesitamos corregirlos.

Al final del día, nos encontramos con grandes dolores de cabeza y bajo la impresión de habernos perdido algo.

Esto sucede porque intentamos mantener toda la información en la cabeza al mismo tiempo (recuerda que es limitada). Nos sobrecargamos nosotras mismas. La mejor estrategia es hacer una lista de metas y tareas, ayudándonos con estos soportes externos.

Por ejemplo: escribir un simple endpoint para una API necesita un montón de cosas:

1. Una acción en un controlador
2. Una ruta a ese controlador
3. Un use case o comando que ejecute la acción
4. Probablemente una o más entidades y su repositorio
5. La definición en el contenedor de dependencias
5. Posiblemente algún servicio
6. La definición den el contenedor de dependencias
7. Un objeto de respuesta
8. etc.

Con esto nuestra memoria se sobrecarga. Esto explica por qué nos sentimos cansadas y estresadas, con el sentimiento de que podríamos haber olvidado algo. E inseguras sobre los que estamos haciendo o si nos hemos dejado atrás algo importante.

Así que, echemos una mirada a cómo ejecutaríamos el mismo proceso, esta vez con testing al final.

En realidad es caso lo mismo, pero ahora hay tests al final del proceso. La clase de tests que automatizamos.

El resultado final es mejor, porque ahora tenemos más confianza con el código. Pero seguimos teniendo ese dolor de cabeza al final del día.

Sí. Hemos hecho la misma cantidad de trabajo, con la misma sobrecarga mental y con el añadido de tener que escribir una suite de test, mientras nuestro cerebro nos grita: "¡Hey! ¡Pero si el trabajo está terminado! ¿Qué estás haciendo?"

En estas condiciones, puede que nuestros tests no sean los mejores tests del mundo.

De hecho, ya estamos cansadas cuando empezamos la fase de testing. Esto explica por qué mucha gente piensa que el testing es duro y hasta que es doloroso.

Así que los test mejoran nuestra confianza en el código, al cose de un montón de trabajo extra. Nuestra vida no es mejor con tests, incluso si dormimos mejor por la noche. Entonces, ¿qué es lo que está mal?

Para mejorar tu vida deberías probar una aproximación diferente. Debería probar Test Driven Development.

Este es el ciclo TDD: una cosa cada vez y posponer decisiones.

* Un test sencillo que falle (no escribas código mientras no tengas un test)
* Añade código que haga pasar el test (no escribas ni más ni menos de lo necesario)
* Revisa el código para mejorar cosas, pero no implementes nada nuevo manteniendo los tests existentes pasando.

Veamos el proceso desde el punto de vista de nuestro modelo de la memoria de trabajo. Cuando escribimos el primer test que falla estamos enfocándonos en este test. Por tanto, no tenemos que poner atención a nada más fuera de eso. Escribir el test también significa que ponemos el conocimiento que necesitamos en el mundo. Nuestra memoria está casi desocupada.

A continuación, nos enfocamos en escribir el código necesario para hacer el test pasar. El conocimiento que necesitamos está en el test, no en nuestra cabeza, y es lo que necesitamos para conseguir nuestro objetivo.

Solo tenemos que pensar en una forma de hacer que el test pase. Si es el primer test sólo necesitamos escribir la implementación más obvia que sea posible. Incluso si esa implementación es tan simple como devolver la respuesta esperada por el test.

Y una vez que el test ha pasado, podemos echar un vistazo al código y ver si podemos hacer mejoras mediante refactoring. No tenemos que añadir prestaciones. Debemos mantener el test pasando mientras ordenamos las cosas, eliminando duplicación indeseada, etc.

Repetiremos el ciclo hasta tener la funcionalidad completamente implementada. No necesitamos escribir tests extras, no tenemos el riesgo de haber olvidado nada. Nuestra cabeza no duele. Hemos usado el cerebro para pensar evitando la sobrecarga.

No es magia, es TDD. Por supuesto, para lograr esto se necesita entrenamiento. TDD es una herramienta intelectual, y el uso de una herramienta debe automatizarse. Por tanto, deberías hacer ejercicios, como las katas, tanto individualmente, como con la ayuda de colegas, en una comunidad de práctica, de la forma que mejor te convenga a ti o a tu equipo. Practicar, practicar y practicar. Una vez que seas capaz de proceder paso a paso, descubrirás que estás más feliz y menos estresada en el medio y largo plazo.

## Un consejo final

Almacena la mayor cantidad de conocimiento que necesites en el mundo: usa un backlog, usa post-its, escribe una lista de tareas, dibujas esquemas, modelos, mapas conceptuales… Liberta tu cabeza y deja espacio para trabajar en una cosa cada vez.

TDD es más que escribir tests. Es poner el conocimiento que necesitas en el código y liberar tu mente. Es posponer decisiones hasta el momento en que estés lista para tomarlas.

De verdad, prueba TDD, tu vida como desarrolladora mejorará.
