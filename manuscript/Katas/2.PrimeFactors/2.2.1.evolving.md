# Evolución del algoritmo mediante tests

## Construir la interfaz pública de una clase dirigida por tests

Empezaremos con un test que nos obliga a definir la clase. De momento no nos hace falta nada más:

```kotlin
package org.talkingbit.kata

import org.junit.jupiter.api.Test

class RomanNumeralsTest {

    @Test
    fun `Should convert numbers to roman` () {
        RomanNumerals()
    }
}
```

Ejecutamos el test para verlo fallar y, a continuación, escribimos la definición de la clase vacía, lo mínimo imprescindible para que el test pase.

```kotlin
package org.talkingbit.kata

class RomanNumerals {

}
```

Ya podemos pensar en el segundo test, que necesitamos para definir la interfaz pública:

```kotlin
package org.talkingbit.kata

import org.junit.jupiter.api.Test

class RomanNumeralsTest {

    @Test
    fun `It converts numbers to roman` () {
        RomanNumerals().toRoman()
    }
}
```

Estamos modificando el primer test. Ahora que tenemos algo de soltura, podemos permitirnos estas licencias, para que escribir un nuevo test nos resulte más económico. Comprobamos que falle por lo que tiene que fallar (no está definido el mensaje `toRoman`). Seguidamente escribimos el código necesario para hacerlo pasar. El IDE nos ayuda: si ejecutamos el test veremos que lanza una excepción que nos dice que el método existe pero no está implementado:

```kotlin
package org.talkingbit.kata

class RomanNumerals {
    fun toRoman() {
        TODO("Not yet implemented")
    }
}
```

De momento, lo quitamos:

```kotlin
package org.talkingbit.kata

class RomanNumerals {
    fun toRoman() {
    }
}
```

Y con esto el test pasa.

Ahora ya vamos a pensar en cómo usar este código para convertir los números arábigos ea la notación romana. Como en ella no hay cero, tenemos que empezar por el 1.

```kotlin
package org.talkingbit.kata

import org.junit.jupiter.api.Test

class RomanNumeralsTest {

    @Test
    fun `Should convert numbers to roman` () {
        RomanNumerals().toRoman(1)
    }
}
```

Al ejecutar el test vemos que falla porque la función no espera parámetro, así que lo añadimos:

```kotlin
package org.talkingbit.kata

class RomanNumerals {
    fun toRoman(decimal: Int) {
    }
}
```

Y esto hace pasar el test. De momento no hemos tenido motivos para hacer refactor, así que estamos preparadas para el siguiente test.

Hay que hacer una salvedad aquí. Esta forma de construir la clase puede no funcionar igual en otros lenguajes. En este ejemplo, Kotlin no puede ejecutar un método si le pasamos parámetros cuando no los pide, lo que nos permite hacer un test de este estilo para forzar la implementación del mismo. Sin embargo, a PHP le daría igual, de modo que tendríamos que usar otro tipo de test para forzarnos a introducir el parámetro.

El caso es que ya estamos cerca de definir toda la interfaz que necesitamos, nos queda definir cómo esperamos que sea la respuesta de este método.

Kotlin acepta esto sin problemas, por lo que el test no falla. En otros lenguajes, el compilador require que haya una respuesta.

```kotlin
package org.talkingbit.kata

import org.junit.jupiter.api.Test
import kotlin.test.assertEquals

class RomanNumeralsTest {

    @Test
    fun `Should convert numbers to roman` () {
        val roman = RomanNumerals().toRoman(1)
    }
}
```

Esto nos obliga a introducir una aserción, de forma que el test evalúe la respuesta:

```kotlin
package org.talkingbit.kata

import org.junit.jupiter.api.Test
import kotlin.test.assertEquals

class RomanNumeralsTest {

    @Test
    fun `Should convert numbers to roman` () {
        assertEquals("I", RomanNumerals().toRoman(1))
    }
}
```

En este punto la interfaz está definida por los tests. Para hacer pasar el test ya tenemos que empezar a implementar el algoritmo, que es el objetivo de este capítulo:

## Dirigir el desarrollo de un algoritmo con ejemplos

En este punto nuestro método no implementa nada, el outcome del mismo es `null`|`nil` o `void`.

### De null a constante

¿Cuál es la transformación más sencilla que podemos realizar para que el test pase? En pocas palabras se trata de pasar de no devolver nada a devolver algo, y para que el test pase, ese algo debe ser el valor "I". O sea, una constante:

```kotlin
package org.talkingbit.kata

class RomanNumerals {
    fun toRoman(decimal: Int): String {
        return "I"
    }
}
```

El test pasa. Toca refactor.

Haremos un refactor para prepararnos para lo que viene después. Cuando cambiemos el ejemplo tendrá que cambiar la respuesta. Así que vamos a hacer dos cosas: usar el parámetro y, a la vez, asegurar que este test siempre pasará:

```kotlin
package org.talkingbit.kata

class RomanNumerals {
    fun toRoman(decimal: Int): String {
        if (decimal == 1) return "I"
        return ""
    }
}
```

El test ahora sigue pasando y no hay nada que hacer ya, por lo que vamos a introducir un nuevo ejemplo:

```kotlin
package org.talkingbit.kata

import org.junit.jupiter.api.Test
import kotlin.test.assertEquals

class RomanNumeralsTest {

    @Test
    fun `Should convert numbers to roman` () {
        assertEquals("I", RomanNumerals().toRoman(1))
        assertEquals("II", RomanNumerals().toRoman(2))
    }
}
```

Al ejecutar el test comprobamos que falla porque no devuelve el `II` esperado. Una forma de hacerlo pasar es la siguiente:

```kotlin
package org.talkingbit.kata

class RomanNumerals {
    fun toRoman(decimal: Int): String {
        if (decimal == 2) return "II"
        if (decimal == 1) return "I"
        return ""
    }
}
```

Observa que, de momento, estamos devolviendo constantes en todos los casos.

Hagamos refactor, ya que estamos en verde. Primero del test, para que sea aún más compacto y fácil de leer y añadir nuevos ejemplos:

```kotlin
package org.talkingbit.kata

import org.junit.jupiter.api.Test
import kotlin.test.assertEquals

class RomanNumeralsTest {

    @Test
    fun `Should convert numbers to roman` () {
        assertConvertsToRoman(1, "I")
        assertConvertsToRoman(2, "II")
    }

    private fun assertConvertsToRoman(arabic: Int, roman: String) {
        assertEquals(roman, RomanNumerals().toRoman(arabic))
    }
}
```

Añadamos un test más. Ahora es aún más sencillo:

```kotlin
package org.talkingbit.kata

import org.junit.jupiter.api.Test
import kotlin.test.assertEquals

class RomanNumeralsTest {

    @Test
    fun `Should convert numbers to roman` () {
        assertConvertsToRoman(1, "I")
        assertConvertsToRoman(2, "II")
        assertConvertsToRoman(3, "III")
    }

    private fun assertConvertsToRoman(arabic: Int, roman: String) {
        assertEquals(roman, RomanNumerals().toRoman(arabic))
    }
}
```

Lo vemos fallar y para que pase, añadimos una nueva constante:

```kotlin
package org.talkingbit.kata

class RomanNumerals {
    fun toRoman(decimal: Int): String {
        if (decimal == 3) return "III"
        if (decimal == 2) return "II"
        if (decimal == 1) return "I"
        return ""
    }
}
```

Y ahora expresando lo mismo, pero de distinta manera y usando una única constante:

```kotlin
package org.talkingbit.kata

class RomanNumerals {
    fun toRoman(decimal: Int): String {
        if (decimal == 3) return "I" + "I" + "I"
        if (decimal == 2) return "I" + "I"
        if (decimal == 1) return "I"
        return ""
    }
}
```

Podríamos extraerla:

```kotlin
package org.talkingbit.kata

class RomanNumerals {
    fun toRoman(decimal: Int): String {
        val i = "I"
        if (decimal == 3) roman = i + i + i
        if (decimal == 2) roman = i + i
        if (decimal == 1) roman = i
        return ""
    }
}
```

Y ahora es fácil ver cómo podríamos introducir una nueva transformación.

## De constante a variable

Esta transformación consiste en usar una variable para generar la respuesta. Es decir, ahora en lugar de devolver un valor fijo para cada ejemplo, lo que haremos es calcular la respuesta que toca. Básicamente, hemos empezado a construir un algoritmo.

```kotlin
package org.talkingbit.kata

class RomanNumerals {
    fun toRoman(decimal: Int): String {
        val i = "I"
        var roman = ""
        if (decimal == 3) roman = i + i + i
        if (decimal == 2) roman = i + i
        if (decimal == 1) roman = i

        return roman
    }
}
```

Esta transformación hace evidente que al algoritmo consiste en acumular tantos I como nos indica el número decimal. Una forma de verlo es la siguiente:

```kotlin
package org.talkingbit.kata

class RomanNumerals {
    fun toRoman(decimal: Int): String {
        val i = "I"
        var roman = ""
        for (counter in 1..decimal) {
            roman += i
        }
        return roman
    }
}
```

Pero este bucle `for`, se puede expresar mejor mediante un `while`:

```kotlin
package org.talkingbit.kata

class RomanNumerals {
    fun toRoman(decimal: Int): String {
        var arabic = decimal
        var roman = ""
        while (arabic >= 1) {
            roman += "I"
            arabic -= 1
        }
        return roman
    }
}

```

Hay que hacer notar que los parámetros en kotlin son `final`, por lo que no podemos modificarlos. Por eso hemos tenido que introducir una variable que se inicializa con el valor del parámetro. Por otro lado, puesto que la constante `i` sólo se usa una vez, y como su significado es bastante evidente, la vamos a eliminar.

De este modo, hemos empezado a construir una solución más general para el algoritmo, al menos hasta el punto definido actualmente por los tests. Como sabemos, no es "legal" acumular más de 3 símbolos iguales en la notación romana, por lo que el algoritmo en su estado actual generará números romanos incorrectos si lo usamos a partir del cuatro.

Esto nos indica que necesitamos un nuevo test para poder incorporar nuevo comportamiento y desarrollar más el algoritmo, que aún es muy específico.

Pero, ¿cuál es el siguiente ejemplo que podríamos implementar?

En primer lugar tenemos el número 4, que en notación romana es `IV`. Introduce un símbolo nuevo y una combinación de símbolos, lo que implica que hay que resolver dos problemas y en TDD queremos resolver un problema cada vez. Así que nos vamos al siguiente, que es el número cinco:

```kotlin
package org.talkingbit.kata

import org.junit.jupiter.api.Test
import kotlin.test.assertEquals

class RomanNumeralsTest {

    @Test
    fun `Should convert numbers to roman` () {
        assertConvertsToRoman(1, "I")
        assertConvertsToRoman(2, "II")
        assertConvertsToRoman(3, "III")
        assertConvertsToRoman(5, "V")
    }

    private fun assertConvertsToRoman(arabic: Int, roman: String) {
        assertEquals(roman, RomanNumerals().toRoman(arabic))
    }
}
``` 

## De incondicional a condicional

Comprobamos que el test falla por las razones esperadas. Para hacerlo pasar tomaremos otro camino, introduciendo una condicional donde antes no teníamos.

```kotlin
package org.talkingbit.kata

class RomanNumerals {
    fun toRoman(decimal: Int): String {
        var arabic = decimal
        var roman = ""

        if (arabic == 5) {
            return "V"
        }

        while (arabic >= 1) {
            roman += "I"
            arabic -= 1
        }
        return roman
    }
}
```


Lo cierto es que ya habíamos usado condicionales antes, cuando nuestras respuestas eran constantes, para escoger "qué constante devolver" por así decir. Ahora introducimos la condicional para poder tratar una nueva familia de casos ya que hemos agotado la capacidad del código existente para resolver los casos nuevos que estamos introduciendo. Y dentro de esa rama de ejecución que antes no existía, volvemos a resolver mediante una constante.

Introduzcamos un nuevo test que falle para forzar un nuevo avance del algoritmo:

```kotlin
package org.talkingbit.kata

import org.junit.jupiter.api.Test
import kotlin.test.assertEquals

class RomanNumeralsTest {

    @Test
    fun `Should convert numbers to roman` () {
        assertConvertsToRoman(1, "I")
        assertConvertsToRoman(2, "II")
        assertConvertsToRoman(3, "III")
        assertConvertsToRoman(5, "V")
        assertConvertsToRoman(6, "VI")
    }

    private fun assertConvertsToRoman(arabic: Int, roman: String) {
        assertEquals(roman, RomanNumerals().toRoman(arabic))
    }
}
```

En este caso es especialmente interesante ver cómo falla:

```
expected:<[V]I> but was:<[IIIII]I>
Expected :VI
Actual   :IIIIII
```

Por una parte, necesitamos hacer que se incluya el símbolo "V", algo que podemos hacer de forma muy simple, cambiando el `==` por un `>=`.

```kotlin
package org.talkingbit.kata

class RomanNumerals {
    fun toRoman(decimal: Int): String {
        var arabic = decimal
        var roman = ""

        if (arabic >= 5) {
            return "V"
        }

        while (arabic >= 1) {
            roman += "I"
            arabic -= 1
        }
        return roman
    }
}
```

Obviamente el test sigue fallando, pero ahora de manera distinta:

```
expected:<V[I]> but was:<V[]>
Expected :VI
Actual   :V
```

Y esa forma es reveladora ya que nos dice que no podemos salir por esa rama, sino que tenemos que volver al flujo principal, acumulando el resultado parcial en la variable `roman`.

```kotlin
package org.talkingbit.kata

class RomanNumerals {
    fun toRoman(decimal: Int): String {
        var arabic = decimal
        var roman = ""

        if (arabic >= 5) {
            roman += "V"
        }

        while (arabic >= 1) {
            roman += "I"
            arabic -= 1
        }
        return roman
    }
}
```

De nuevo, es obvio que el test fallará, ya que no hemos descontado de `arabic` el `5` que ya habíamos convertido:

```
expected:<V[]> but was:<V[IIIII]>
Expected :V
Actual   :VIIIII
```

No tenemos más que añadir una línea:

```kotlin
package org.talkingbit.kata

class RomanNumerals {
    fun toRoman(decimal: Int): String {
        var arabic = decimal
        var roman = ""

        if (arabic >= 5) {
            roman += "V"
            arabic -= 5
        }

        while (arabic >= 1) {
            roman += "I"
            arabic -= 1
        }
        return roman
    }
}
```

Estos cambios nos han permitido avanzar el algoritmo y cubrir varios casos más. Si hacemos tests para el 7 y el 8, veremos que pasan sin ningún cambio. Con eso llegamos al 9 que nos plantea un problema parecido al del 4 que, por cierto, aún no hemos tocado. En este caso vamos a posponer el problema por los mismos motivos. Es más fácil introducir un símbolo nuevo si es el único problema que hay que resolver en esta iteración.

```kotlin
package org.talkingbit.kata

import org.junit.jupiter.api.Test
import kotlin.test.assertEquals

class RomanNumeralsTest {

    @Test
    fun `Should convert numbers to roman` () {
        assertConvertsToRoman(1, "I")
        assertConvertsToRoman(2, "II")
        assertConvertsToRoman(3, "III")
        assertConvertsToRoman(5, "V")
        assertConvertsToRoman(6, "VI")
        assertConvertsToRoman(10, "X")
    }

    private fun assertConvertsToRoman(arabic: Int, roman: String) {
        assertEquals(roman, RomanNumerals().toRoman(arabic))
    }
}
```

Como era de esperar, este test no pasa. Para hacerlo pasar, podemos utilizar la misma estrategia que antes: introducir una condicional que nos abra una nueva línea de ejecución y hacer pasar el test.

```kotlin
package org.talkingbit.kata

class RomanNumerals {
    fun toRoman(decimal: Int): String {
        var arabic = decimal
        var roman = ""

        if (arabic == 10) {
            return "X"
        }

        if (arabic >= 5) {
            roman += "V"
            arabic -= 5
        }

        while (arabic >= 1) {
            roman += "I"
            arabic -= 1
        }
        return roman
    }
}
```

Lo cierto es que se empieza a ver un patrón, pero necesitamos algún caso más que nos fuerce a implementar los cambios necesarios. ¿Qué pasaría con el número 11?

```kotlin
package org.talkingbit.kata

import org.junit.jupiter.api.Test
import kotlin.test.assertEquals

class RomanNumeralsTest {

    @Test
    fun `Should convert numbers to roman` () {
        assertConvertsToRoman(1, "I")
        assertConvertsToRoman(2, "II")
        assertConvertsToRoman(3, "III")
        assertConvertsToRoman(5, "V")
        assertConvertsToRoman(6, "VI")
        assertConvertsToRoman(10, "X")
        assertConvertsToRoman(11, "XI")
    }

    private fun assertConvertsToRoman(arabic: Int, roman: String) {
        assertEquals(roman, RomanNumerals().toRoman(arabic))
    }
}
```

Pues que el test falla de una manera similar a la que habíamos visto antes con el 6. 

```
expected:<[X]I> but was:<[VIIIII]I>
Expected :XI
Actual   :VIIIIII
```

El cambio que tenemos que hacer ahora mismo es cambiar un `==` por un `>=` en primer lugar:

```kotlin
package org.talkingbit.kata

class RomanNumerals {
    fun toRoman(decimal: Int): String {
        var arabic = decimal
        var roman = ""

        if (arabic >= 10) {
            return "X"
        }

        if (arabic >= 5) {
            roman += "V"
            arabic -= 5
        }

        while (arabic >= 1) {
            roman += "I"
            arabic -= 1
        }
        return roman
    }
}
```

Y después, descontar el 10 y permitir que la ejecución siga, cambiando la respuesta constante por una variable:

```kotlin
package org.talkingbit.kata

class RomanNumerals {
    fun toRoman(decimal: Int): String {
        var arabic = decimal
        var roman = ""

        if (arabic >= 10) {
            roman += "X"
            arabic -= 10
        }

        if (arabic >= 5) {
            roman += "V"
            arabic -= 5
        }

        while (arabic >= 1) {
            roman += "I"
            arabic -= 1
        }
        return roman
    }
}
```

Con esto, volvemos a verde y confirmamos un patrón en la forma de manejar los distintos símbolos. Ciertos valores arábigos tienen un equivalente en un símbolo romano único, si el arábigo actual es mayor o igual se añade el símbolo y se continúa con el siguiente.

Sin embargo, necesitamos que un test nuevo nos permita avanzar un poco más. "X" es uno de los símbolos que admite repetición, por lo que vamos a introducir un caso que nos obligue a darle soporte:

```kotlin
package org.talkingbit.kata

import org.junit.jupiter.api.Test
import kotlin.test.assertEquals

class RomanNumeralsTest {

    @Test
    fun `Should convert numbers to roman` () {
        assertConvertsToRoman(1, "I")
        assertConvertsToRoman(2, "II")
        assertConvertsToRoman(3, "III")
        assertConvertsToRoman(5, "V")
        assertConvertsToRoman(6, "VI")
        assertConvertsToRoman(10, "X")
        assertConvertsToRoman(11, "XI")
        assertConvertsToRoman(20, "XX")
    }

    private fun assertConvertsToRoman(arabic: Int, roman: String) {
        assertEquals(roman, RomanNumerals().toRoman(arabic))
    }
}
```

Obviamente falla.

## Cambiando de if a while

Para poder manejar este caso, lo más sencillo es cambiar el `if` a `while`. `while` es una estructura que es a la vez condicional y a la vez un bucle. Mientras que `if` sólo ejecuta la rama condicionada una vez, `while` lo hace mientras la condición se siga cumpliendo.

```kotlin
package org.talkingbit.kata

class RomanNumerals {
    fun toRoman(decimal: Int): String {
        var arabic = decimal
        var roman = ""

        while (arabic >= 10) {
            roman += "X"
            arabic -= 10
        }

        if (arabic >= 5) {
            roman += "V"
            arabic -= 5
        }

        while (arabic >= 1) {
            roman += "I"
            arabic -= 1
        }
        return roman
    }
}
```
## Referencias

* [Applying Transformation Priority Premise to Roman Numerals Kata](https://codurance.com/2015/05/18/applying-transformation-priority-premise-to-roman-numerals-kata/)
* [The Transformation Priority Premise](http://blog.cleancoder.com/uncle-bob/2013/05/27/TheTransformationPriorityPremise.html)
* [The Transformation Priority Premise (TPP)](https://medium.com/@elliotchance/the-transformation-priority-premise-tpp-3e5dc08d445e)
