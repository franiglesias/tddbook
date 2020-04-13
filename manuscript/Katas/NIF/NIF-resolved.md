# Kata NIF resuelta

## Enunciado de la kata

Crear un clase Nif, que será un Value Object para representar el Número de Identificación Fiscal de España. Es una combinación de una cadena de ocho caracteres numéricos, con una letra final que actúa como carácter de control.

Esta letra de control se obtiene calculando el resto de dividir la parte numérica del NIF entre 23 (mod 23). El resultado nos indica la fila en la que consultar la letra de control de la siguiente tabla. En esta tabla he incluido también ejemplos sencillos de NIF válidos para que los puedas usar en los tests.

| Parte numérica | Resto | Letra | Ejemplo NIF válido |
|----|------|-------|----|
| 00000000 | 0 | T | 00000000T |
| 00000001 | 1 | R | 00000001R |
| 00000002 | 2 | W | 00000002W |
| 00000003 | 3 | A | 00000003A |
| 00000004 | 4 | G | 00000004G |
| 00000005 | 5 | M | 00000005M |
| 00000006 | 6 | Y | 00000006Y |
| 00000007 | 7 | F | 00000007F |
| 00000008 | 8 | P | 00000008P |
| 00000009 | 9 | D | 00000009D |
| 00000010 | 10 | X | 00000010X |
| 00000011 | 11 | B | 00000011B |
| 00000012 | 12 | N | 00000012N |
| 00000013 | 13 | J | 00000013J |
| 00000014 | 14 | Z | 00000014Z |
| 00000015 | 15 | S | 00000015S |
| 00000016 | 16 | Q | 00000016Q |
| 00000017 | 17 | V | 00000017V |
| 00000018 | 18 | H | 00000018H |
| 00000019 | 19 | L | 00000019L |
| 00000020 | 20 | C | 00000020C |
| 00000021 | 21 | K | 00000021K |
| 00000022 | 22 | E | 00000022E |

Puedes crear NIF inválidos simplemente escogiendo una parte numérica y la letra que no le corresponde.

| Ejemplo inválido |
|----|
| 00000000S |
| 00000001M |
| 00000002H |
| 00000003Q |
| 00000004E |

Hay una excepción: los NIF para extranjeras (o NIE) pueden empezar por las letras X, Y o Z, que en los cálculos se reemplazan por los números 0, 1 y 2, respectivamente. En ese caso X0000000T equivale a 00000000T.

Para evitar confusiones se han excluído las letras `U`, `I`, `O` y `Ñ`.

Un cadena que empieza por una letra distinta de X, Y, Z, o que contenga caracteres alfabéticos en las posiciones centrales también es inválida.


## Lenguaje y enfoque

Esta kata la vamos a resolver en Go, por lo que vamos a matizar un poco su resultado. 

Go es un lenguaje orientado a objetos pero de una manera diferente a la que puedas estar acostumbrada si vienes de Java o PHP, aunque te resultará vagamente familiar si vienes de JS. Se ha optado por admitir algunas cosas mientras que se han rechazado otras. Go es también un lenguaje funcional, con funciones que son *first class citizens*.

Los métodos no se definen explícitamente en los tipos, sino que son funciones que se asocian a los tipos deseados. Al definir una función se le puede asociar un objeto de un tipo o *receiver*, de modo que la puedes usar como si fuese un método usando *dot notation*.

El caso más llamativo es la herencia, que en Go no existe y, por tanto, sólo admite la composición de objetos. Las interfaces se implementan de forma implícita: si un tipo tiene los métodos de una interfaz entonces es que la implementa.

En este ejemplo vamos a crear un tipo de dato Nif que será un string y una función factoría NewNif que nos permitirá construir Nif validados a partir de un string que le pasemos.

## Referencias

* [Is Go object oriented?](https://flaviocopes.com/golang-is-go-object-oriented/)

