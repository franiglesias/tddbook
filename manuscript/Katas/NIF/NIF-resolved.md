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

Por otro lado, el testing en Go es también un poco particular.

En este ejemplo vamos a crear un tipo de dato Nif que será un string y una función factoría NewNif que nos permitirá construir Nif validados a partir de un string que le pasemos.

### Disclaimer

Basar tests en mensajes de error no es una buena práctica, porque los mensajes de error pueden cambiar con facilidad haciendo fallar los tests aunque no haya realmente una alteración de la funcionalidad.

Sin embargo, en esta kata vamos a usar los mensajes de error como una especie de comodín temporal en el que apoyarnos. Al acabar el ejercicio manejaremos únicamente dos errores posibles.

## Primer test: creando la función constructora

En esta kata nos interesa empezar centrándonos en los sad paths, los casos en los que que no vamos a poder usar el argumento pasado a la función constructora. De todas las innumerables combinaciones de string que la función podría recibir vamos primero a dar una respuesta a las que sabemos con seguridad que no nos van a servir porque no se ajustan a los requisitos. Esta respuesta será un error.

Empezaremos rechazando aquellas cadenas que sean demasiado largas, las que tienen más de  nueve caracteres. Esto lo podemos describir con este test:

```go
// nif/nif_test.go
package nif

import "testing"

func TestShouldFailWhenStringIsTooLong(t *testing.T) {
	NewNif("01234567891011")

```

De momento vamos a ignorar las respuestas de la función simplemente para forzarnos a implementar el mínimo de código.

Como es de suponer el test fallará porque no compila. Así que implementamos el código mínimo necesario, que puede ser tan pequeño como éste:

```go
package nif

func NewNif(candidate string) {

}
```

Ahora podemos avanzar un paso más. La función debería devolver o bien el nif cuando el que hemos pasado es válido o un error en caso de no se pueda. En Go una función puede devolver varios valores y, por convención, se devuelven también los errores como último valor devuelto.

Esto proporciona una flexibilidad que no es habitual encontrar en otros lenguajes y nos deja jugar con algunas ideas que son como mínimo curiosas. Por ejemplo, nosotras vamos a ignorar de momento la respuesta de la función y centrarnos sólo en los errores. Nuestro próximo test va a pedir que la función devuelva sólo el error sin hacer nada con él. El if es, de momento, para que el compilador no proteste.

```go
package nif

import "testing"

func TestShouldFailWhenStringIsTooLong(t *testing.T) {
	err := NewNif("01234567891011")

	if err != nil {}
}
```

Este test nos indica que debemos devolver algo, así que por ahora indicamos que vamos a devolver un error, que puede ser `nil`.

```go
package nif

func NewNif(candidate string) error {
	return nil
}
```

Avancemos un paso más esperando que se produzca un error determinado cuando se cumple la circunstancia definida por el test, que la cadena es demasiado larga, y con lo que tendremos ya un primer test en condiciones:

```go
package nif

import "testing"

func TestShouldFailWhenStringIsTooLong(t *testing.T) {
	err := NewNif("01234567891011")

	if err.Error() != "too long" {
		t.Errorf("Expected too long, got %s", err.Error())
	}
}
```

De nuevo fallará el test y para hacerlo pasar devolvemos incondicionalmente el error:

```go
package nif

import "errors"

func NewNif(candidate string) error {
	return errors.New("too long")
}
```

Y con esto ya hemos hecho que nuestro primer test esté completo y pase. Podríamos ser un poco más estrictas en el manejo de la respuesta para contemplar el caso de que `err` sea `nil`, pero de momento es algo que no nos tiene que afectar.

En este punto me gustaría llamar tu atención al hecho de que no estamos resolviendo nada todavía: el error se devuelve de manera incondicional, por lo que estamos retrasando esta validación para más tarde.

## Segundo test: implementar la primera validación

Nuestro segundo test tiene como objetivo forzar la implementación de la validación que acabamos de posponer. Puede sonar un poco extraño, pero nos muestra que uno de los grandes beneficios de TDD es el hecho de poder posponer decisiones. Al hacerlo, tendremos un poco más de información, lo que siempre es una ventaja.

El test es muy similar al anterior:

```go
package nif

import "testing"

func TestShouldFailWhenStringIsTooLong(t *testing.T) {
	err := NewNif("01234567891011")

	if err.Error() != "too long" {
		t.Errorf("Expected too long, got %s", err.Error())
	}
}

func TestShouldFailWhenStringIsTooShort(t *testing.T) {
	err := NewNif("0123456")

	if err.Error() != "too short" {
		t.Errorf("Expected too short, got %s", err.Error())
	}
}
```

Este test ya nos obliga a actuar de manera diferente en cada caso, así que vamos a implementar la validación que limita los string demasiado largos:

```go
package nif

import "errors"

func NewNif(candidate string) error {
	if len(candidate) > 9{
		return errors.New("too long")
	}

	return errors.New("too short")
}
```

Vuelvo a señalar que en este momento lo que dice el test no lo estamos implementando ahora. Lo haremos en el siguiente ciclo, pero el test se cumple al devolver el error esperado de forma incondicional.

No hay mucho más que podamos hacer en le código de producción, pero al fijarnos en los tests podemos ver que sería posible unificar un poco su estructura. Al fin y al cabo vamos a hacer una serie de ellos en los que pasamos un valor y esperamos un error determinado como respuesta.

## Primer refactor: un test para dominarlos a todos

En Go existe una estructura de test similar a la que en otros lenguajes nos proporciona el uso de Data Providers, `Table Test`.

```go
package nif

import "testing"

func TestShouldFailIfCandidateIsInvalid(t *testing.T) {
	tests := []struct {
		name string
		example string
		expected string
	}{
		{"should fail if too long", "01234567891011", "too long"},
		{"should fail if too short", "01234", "too short"},
	}
	for _, test := range tests {
		t.Run(test.name, func(t *testing.T) {
			err := NewNif(test.example)

			if err.Error() != test.expected {
				t.Errorf("Expected %s, got %s", test.expected, err.Error())
			}
		})
	}
}
``` 


Con esto conseguimos que ahora sea muy fácil y rápido añadir tests, sobre todo si son de la misma familia, como en este caso en el que pasamos cadenas candidatas inválidas y chequeamos por el error. Además, si hacemos cambios en la interfaz de la constructora sólo tenemos un lugar en donde aplicarlos.

Con esto, tendríamos ya todo preparado para seguir desarrollando:

## Tercer test: completar la validación de la longitud y empezar a examinar la estructura

Con los dos tests anteriores verificamos si la cadena que vamos a examinar cumple la especificación de tener exactamente nueve caracteres, aunque de momento eso no está implementado. Lo haremos ahora.

Sin embargo, puede que te preguntes por qué no testear simplemente que la función rechaza los `strings` que no la cumplan, algo que podríamos hacer en un único test.

La razón es que en realidad hay dos posibles formas de que no se cumpla la especificación: el `string` tiene más de 9 caracteres o el `string` tiene menos. Si hacemos un sólo test elegiremos uno de los dos casos, con lo cual no estamos garantizando que se cumpla el otro.

En este ejemplo concreto en que hay un único valor que nos interesa podríamos plantear la disyuntiva entre `strings` con longitud nueve y `strings` con longitud distinta de nueve. Sin embargo, es frecuente que tengamos que trabajar con intervalos de valores que, además, pueden estar abiertos o cerrados. En esa situación la estrategia de dos, o incluso más tests, es muchísimo más segura.

En cualquier caso, en el punto en el que estamos, para mover el desarrollo necesitamos añadir otro requisito en forma de test. Los dos tests existentes nos define la longitud válida del `string`. El siguiente test pregunta por su estructura.

Y con el refactor, añadir un test es tremendamente sencillo.

Empezaremos por el principio. Los Nif válidos comienzan con un número, excepto un subconjunto de ellos que lo hace por alguna de las letras `X`, `Y` y `Z`. Una forma de definir el test es de la siguiente forma:

```go
package nif

import "testing"

func TestShouldFailIfCandidateIsInvalid(t *testing.T) {
	tests := []struct {
		name string
		example string
		expected string
	}{
		{"should fail if too long", "01234567891011", "too long"},
		{"should fail if too short", "01234", "too short"},
		{"should fail if starts with a letter other than X, Y, Z", "A12345678", "bad start format"},
	}
	for _, test := range tests {
		t.Run(test.name, func(t *testing.T) {
			err := NewNif(test.example)

			if err.Error() != test.expected {
				t.Errorf("Expected %s, got %s", test.expected, err.Error())
			}
		})
	}
}
```

Para hacer pasar el test, resolvemos primero el problema pendiente del anterior:

```go
package nif

import "errors"

func NewNif(candidate string) error {
	if len(candidate) > 9{
		return errors.New("too long")
	}

	if len(candidate) < 9{
		return errors.New("too short")
	}

	return errors.New("bad start format")
}
```

Aquí tenemos una oportunidad de refactor bastante clara que consistiría en unir las condicionales que evalúan la longitud del `string`. Sin embargo, esa va a hacer que el test falle ya que al menos tendríamos que cambiar un mensaje de error.

### La vía no muy limpia de cambiar a la vez test y código de producción

Una posibilidad es "saltarnos" temporalmente la condición de que el refactor sea con los tests en verde y hacer cambios a la vez en prod y test. Veamos.

Lo primero sería cambiar el test para esperar un mensaje de error distinto, que será más genérico e igual para todos los casos que queremos consolidar en este paso:

```go
package nif

import "testing"

func TestShouldFailIfCandidateIsInvalid(t *testing.T) {
	tests := []struct {
		name string
		example string
		expected string
	}{
		{"should fail if too long", "01234567891011", "bad format"},
		{"should fail if too short", "01234", "bad format"},
		{"should fail if starts with a letter other than X, Y, Z", "A12345678", "bad start format"},
	}
	for _, test := range tests {
		t.Run(test.name, func(t *testing.T) {
			err := NewNif(test.example)

			if err.Error() != test.expected {
				t.Errorf("Expected %s, got %s", test.expected, err.Error())
			}
		})
	}
}
```

Esto hará fallar el test. Cosa que se puede arreglar cambiando el código de producción del mismo modo:

```go
package nif

import "errors"

func NewNif(candidate string) error {
	if len(candidate) > 9{
		return errors.New("bad format")
	}

	if len(candidate) < 9{
		return errors.New("bad format")
	}

	return errors.New("bad start format")
}
```

El test vuelve a pasar y estamos listas para el refactor. Pero no vamos a hacer eso.

### La vía más segura

Otra opción es hacer un refactor temporal en el test para hacerlo más tolerante. Simplemente hacemos que sea posible devolver un error más genérico aparte del error específico.

```go
package nif

import "testing"

func TestShouldFailIfCandidateIsInvalid(t *testing.T) {
	tests := []struct {
		name string
		example string
		expected string
	}{
		{"should fail if too long", "01234567891011", "too long"},
		{"should fail if too short", "01234", "too short"},
		{"should fail if starts with a letter other than X, Y, Z", "A12345678", "bad start format"},
	}
	for _, test := range tests {
		t.Run(test.name, func(t *testing.T) {
			err := NewNif(test.example)

			if err.Error() != test.expected && err.Error() != "bad format" {
				t.Errorf("Expected %s, got %s", test.expected, err.Error())
			}
		})
	}
}
```

Este cambio nos permite ahora hacer el cambio en producción sin romper nada:

```go
package nif

import "errors"

func NewNif(candidate string) error {
	if len(candidate) > 9{
		return errors.New("bad format")
	}

	if len(candidate) < 9{
		return errors.New("bad format")
	}

	return errors.New("bad end format")
}
```

El test sigue pasando y ahora sí podemos hacer el refactor.

## Segundo refactor: unificar la validación por longitud del `string`


Unificar las condicionales es fácil en este momento. Este es el primer paso, que incluyo aquí para tener un modelo de cómo hacer esto en caso de que fuese un intervalo de longitudes válidas.

```go
package nif

import "errors"

func NewNif(candidate string) error {
	if len(candidate) > 9 || len(candidate) < 9 {
		return errors.New("bad format")
	}
	
	return errors.New("bad start format")
}
```

Pero se puede hacer mejor:

```go
package nif

import "errors"

func NewNif(candidate string) error {
	if len(candidate) != 9 {
		return errors.New("bad format")
	}

	return errors.New("bad start format")
}
```

Y un poco más expresivo:

```go
package nif

import "errors"

func NewNif(candidate string) error {
	const maxlength = 9
	
	if len(candidate) != maxlength {
		return errors.New("bad format")
	}

	return errors.New("bad start format")
}
```

Finalmente, un nuevo refactor del test para contemplar estos cambios. Retiramos nuestro cambio temporal aunque es posible que tengamos que volver a utilizarlo en el futuro.

```go
package nif

import "testing"

func TestShouldFailIfCandidateIsInvalid(t *testing.T) {
	tests := []struct {
		name string
		example string
		expected string
	}{
		{"should fail if too long", "01234567891011", "bad format"},
		{"should fail if too short", "01234", "bad format"},
		{"should fail if starts with a letter other than X, Y, Z", "A12345678", "bad start format"},
	}
	for _, test := range tests {
		t.Run(test.name, func(t *testing.T) {
			err := NewNif(test.example)

			if err.Error() != test.expected {
				t.Errorf("Expected %s, got %s", test.expected, err.Error())
			}
		})
	}
}

```

Fíjate que hemos podido hacer todos estos cambios sin que fallaran los tests.

## Cuarto test: avanzando en la estructura

El código está bastante compacto, así que vamos a añadir un nuevo test que nos permita avanzar con la validez de la estructura. El fragmento central del Nif está compuesto sólo por números, exactamente siete:

```go
package nif

import "testing"

func TestShouldFailIfCandidateIsInvalid(t *testing.T) {
	tests := []struct {
		name string
		example string
		expected string
	}{
		{"should fail if too long", "01234567891011", "bad format"},
		{"should fail if too short", "01234", "bad format"},
		{"should fail if starts with a letter other than X, Y, Z", "A12345678", "bad start format"},
		{"should fail if doesn't have 7 digit in the middle", "0123X567R", "bad middle format"},
	}
	for _, test := range tests {
		t.Run(test.name, func(t *testing.T) {
			err := NewNif(test.example)

			if err.Error() != test.expected {
				t.Errorf("Expected %s, got %s", test.expected, err.Error())
			}
		})
	}
}
```

Lo lanzamos para asegurarnos de que falla por la razón adecuada. Para hacer pasar el test tenemos que resolver primero el test anterior, por lo que añadiremos código para verificar que el primer símbolo es un número o una letra en el conjunto `X`, `Y` y `Z`. Lo haremos con una expresión regular:

```go
package nif

import (
	"errors"
	"regexp"
)

func NewNif(candidate string) error {
	const maxlength = 9

	if len(candidate) != maxlength {
		return errors.New("bad format")
	}

	invalid := regexp.MustCompile(`(?i)^[^0-9XYZ].*`)

	if invalid.MatchString(candidate) {
		return errors.New("bad start format")
	}
	
	return errors.New("bad middle format")
}
```

Con este código hacemos pasar el test, pero vamos a hacer un refactor.

## Tercer refactor: invertir la condicional

Tiene sentido que en vez de hacer match contra una expresión regular que excluya los string no válidos, hagamos match contra una expresión que los detecte. Con eso tendremos que invertir la condicional. Lo cierto es que el cambio es bastante pequeño:

```go
package nif

import (
	"errors"
	"regexp"
)

func NewNif(candidate string) error {
	const maxlength = 9

	if len(candidate) != maxlength {
		return errors.New("bad format")
	}

	valid := regexp.MustCompile(`(?i)^[0-9XYZ].*`)

	if !valid.MatchString(candidate) {
		return errors.New("bad start format")
	}
	
	return errors.New("bad middle format")
}
```

## Quinto test: el final de la estructura

Nos estamos acercando al final de la validación estructural del Nif, necesitamos un test que nos diga cuáles rechazar en función de su último símbolo, lo que nos llevará a resolver el problema que quedaba pendiente del test anterior:

```go
package nif

import "testing"

func TestShouldFailIfCandidateIsInvalid(t *testing.T) {
	tests := []struct {
		name string
		example string
		expected string
	}{
		{"should fail if too long", "01234567891011", "bad format"},
		{"should fail if too short", "01234", "bad format"},
		{"should fail if starts with a letter other than X, Y, Z", "A12345678", "bad start format"},
		{"should fail if doesn't have 7 digit in the middle", "0123X567R", "bad middle format"},
		{"should fail if doesn't end with a valid control letter", "01234567U", "invalid end format"},

	}
	for _, test := range tests {
		t.Run(test.name, func(t *testing.T) {
			err := NewNif(test.example)

			if err.Error() != test.expected {
				t.Errorf("Expected %s, got %s", test.expected, err.Error())
			}
		})
	}
}
```

De las cuatro letras no válidas tomamos la `U` como ejemplo, pero podrían ser la `I`, la `Ñ` y la `O`.

Sin embargo, para hacer pasar el test, lo que hacemos es asegurarnos que el anterior se va a cumplir. Lo más fácil es implementar eso de forma separada:

```go
package nif

import (
	"errors"
	"regexp"
)

func NewNif(candidate string) error {
	const maxlength = 9

	if len(candidate) != maxlength {
		return errors.New("bad format")
	}

	valid := regexp.MustCompile(`(?i)^[0-9XYZ].*`)

	if !valid.MatchString(candidate) {
		return errors.New("bad start format")
	}

	valid = regexp.MustCompile(`(?i)^.\d{7}.*`)

	if !valid.MatchString(candidate) {
		return errors.New("bad middle format")
	}

	return errors.New("invalid end format")
}
```

## Cuarto refactor: compactando el algoritmo

Con esto pasa el test y nos encontramos con una situación familiar que ya hemos resuelto antes: tenemos que convertir los errores en más genéricos con la ayuda temporal de un control extra en el test:

```go
package nif

import "testing"

func TestShouldFailIfCandidateIsInvalid(t *testing.T) {
	tests := []struct {
		name string
		example string
		expected string
	}{
		{"should fail if too long", "01234567891011", "bad format"},
		{"should fail if too short", "01234", "bad format"},
		{"should fail if starts with a letter other than X, Y, Z", "A12345678", "bad start format"},
		{"should fail if doesn't have 7 digit in the middle", "0123X567R", "bad middle format"},
		{"should fail if doesn't end with a valid control letter", "01234567U", "invalid end format"},

	}
	for _, test := range tests {
		t.Run(test.name, func(t *testing.T) {
			err := NewNif(test.example)

			if err.Error() != test.expected && err.Error() != "bad format" {
				t.Errorf("Expected %s, got %s", test.expected, err.Error())
			}
		})
	}
}
```

Hacemos el cambio en los mensaje de error en el código de producción:

```go
package nif

import (
	"errors"
	"regexp"
)

func NewNif(candidate string) error {
	const maxlength = 9

	if len(candidate) != maxlength {
		return errors.New("bad format")
	}

	valid := regexp.MustCompile(`(?i)^[0-9XYZ].*`)

	if !valid.MatchString(candidate) {
		return errors.New("bad format")
	}

	valid = regexp.MustCompile(`(?i)^.\d{7}.*`)

	if !valid.MatchString(candidate) {
		return errors.New("bad format")
	}

	return errors.New("invalid end format")
}
```

Ahora unificamos la expresión regular y las condicionales:

```go
package nif

import (
	"errors"
	"regexp"
)

func NewNif(candidate string) error {
	const maxlength = 9

	if len(candidate) != maxlength {
		return errors.New("bad format")
	}

	valid := regexp.MustCompile(`(?i)^[0-9XYZ]\d{7}.*`)

	if !valid.MatchString(candidate) {
		return errors.New("bad format")
	}
	
	return errors.New("invalid end format")
}
```

Todavía podemos hacer un cambio pequeño pero importante. La última parte de la expresión regular `.*` está para cumplir el requisito de que se haga match de toda la cadena, pero realmente no necesitamos el cuantificador ya que nos basta un carácter:

```go
package nif

import (
	"errors"
	"regexp"
)

func NewNif(candidate string) error {
	const maxlength = 9

	if len(candidate) != maxlength {
		return errors.New("bad format")
	}

	valid := regexp.MustCompile(`(?i)^[0-9XYZ]\d{7}.$`)

	if !valid.MatchString(candidate) {
		return errors.New("bad format")
	}

	return errors.New("invalid end format")
}
```

Y esto nos revela un detalle, la expresión regular hace match en cadenas de exactamente nueve caracteres, por lo que la validación inicial de la longitud ahora es innecesaria:

```
package nif

import (
	"errors"
	"regexp"
)

func NewNif(candidate string) error {
	valid := regexp.MustCompile(`(?i)^[0-9XYZ]\d{7}.$`)

	if !valid.MatchString(candidate) {
		return errors.New("bad format")
	}

	return errors.New("invalid end format")
}
```

Tanto camino recorrido para desandarlo. Sin embargo, al principio no sabíamos esto y es ahí donde está el valor del proceso.

Por último, cambiamos el test para reflejar los cambios y volver a quitar nuestro apoyo temporal:

```go
package nif

import "testing"

func TestShouldFailIfCandidateIsInvalid(t *testing.T) {
	tests := []struct {
		name string
		example string
		expected string
	}{
		{"should fail if too long", "01234567891011", "bad format"},
		{"should fail if too short", "01234", "bad format"},
		{"should fail if starts with a letter other than X, Y, Z", "A12345678", "bad format"},
		{"should fail if doesn't have 7 digit in the middle", "0123X567R", "bad format"},
		{"should fail if doesn't end with a valid control letter", "01234567U", "invalid end format"},

	}
	for _, test := range tests {
		t.Run(test.name, func(t *testing.T) {
			err := NewNif(test.example)

			if err.Error() != test.expected {
				t.Errorf("Expected %s, got %s", test.expected, err.Error())
			}
		})
	}
}
```

## Sexto test: terminando la validación estructural

Necesitamos un nuevo test para terminar la parte de la validación estructural. Los tests existentes nos garantizarían la corrección de los `strings`, pero la siguiente validación ya implica el algoritmo para calcular la letra de control. 

El test que necesitamos ahora controla que no podemos usar un Nif estructuralmente válido, pero en el que la letra de control sea incorrecta. Al enunciar la kata pusimos algunos ejemplos, como `00000000S`. Este es el test:

```go
package nif

import "testing"

func TestShouldFailIfCandidateIsInvalid(t *testing.T) {
	tests := []struct {
		name string
		example string
		expected string
	}{
		{"should fail if too long", "01234567891011", "bad format"},
		{"should fail if too short", "01234", "bad format"},
		{"should fail if starts with a letter other than X, Y, Z", "A12345678", "bad format"},
		{"should fail if doesn't have 7 digit in the middle", "0123X567R", "bad format"},
		{"should fail if doesn't end with a valid control letter", "01234567U", "invalid end format"},
		{"should fail if doesn't end with the right control letter", "00000000S", "bad control letter"},

	}
	for _, test := range tests {
		t.Run(test.name, func(t *testing.T) {
			err := NewNif(test.example)

			if err.Error() != test.expected {
				t.Errorf("Expected %s, got %s", test.expected, err.Error())
			}
		})
	}
}
```

Y he aquí el código que lo hace pasar:

```go
package nif

import (
	"errors"
	"regexp"
)

func NewNif(candidate string) error {
	valid := regexp.MustCompile(`(?i)^[0-9XYZ]\d{7}.$`)

	if !valid.MatchString(candidate) {
		return errors.New("bad format")
	}

	valid = regexp.MustCompile(`(?i).*[^UIOÑ0-9]$`)

	if !valid.MatchString(candidate) {
		return errors.New("invalid end format")
	}
	
	return errors.New("bad control letter")
}
```

Y, cómo no, toca refactorizar.

## Quinto refactor: compactando la validación

Este refactor es bastante obvio, pero tenemos que volver a proteger el test temporalmente:

```go
package nif

import "testing"

func TestShouldFailIfCandidateIsInvalid(t *testing.T) {
	tests := []struct {
		name string
		example string
		expected string
	}{
		{"should fail if too long", "01234567891011", "bad format"},
		{"should fail if too short", "01234", "bad format"},
		{"should fail if starts with a letter other than X, Y, Z", "A12345678", "bad format"},
		{"should fail if doesn't have 7 digit in the middle", "0123X567R", "bad format"},
		{"should fail if doesn't end with a valid control letter", "01234567U", "invalid end format"},
		{"should fail if doesn't end with the right control letter", "00000000S", "bad control letter"},

	}
	for _, test := range tests {
		t.Run(test.name, func(t *testing.T) {
			err := NewNif(test.example)

			if err.Error() != test.expected  && err.Error() != "bad format" {
				t.Errorf("Expected %s, got %s", test.expected, err.Error())
			}
		})
	}
}
```

Hacemos más general el error para poder unificar las expresiones regulares y las condicionales:

```go
package nif

import (
	"errors"
	"regexp"
)

func NewNif(candidate string) error {
	valid := regexp.MustCompile(`(?i)^[0-9XYZ]\d{7}.$`)

	if !valid.MatchString(candidate) {
		return errors.New("bad format")
	}

	valid = regexp.MustCompile(`(?i).*[^UIOÑ0-9]$`)

	if !valid.MatchString(candidate) {
		return errors.New("bad format")
	}

	return errors.New("bad control letter")
}
```

Y hacemos ahora la unificación, mientras los tests siguen pasando:

```go
package nif

import (
	"errors"
	"regexp"
)

func NewNif(candidate string) error {
	valid := regexp.MustCompile(`(?i)^[0-9XYZ]\d{7}[^UIOÑ0-9]$`)

	if !valid.MatchString(candidate) {
		return errors.New("bad format")
	}

	return errors.New("bad control letter")
}
```

Con esto terminamos la validación de la estructura y nos quedaría implementar el algoritmo mod 23. Peo para eso necesitamos un pequeño cambio de enfoque.

## Séptimo test: seamos optimistas

El algoritmo es, de hecho, muy sencillo: obtener un resto (mod 23) y buscar con el resultado en una tabla. Es fácil de implementar en una sola iteración. Sin embargo, vamos a hacerlo más lentamente.

 
Hasta ahora nuestros tests eran pesimistas porque esperaban ejemplos incorrectos de Nif para poder pasar. Nuestros tests ahora tienen que ser optimistas, es decir, van a esperar que le pasemos ejemplos de NIF válidos.


## Referencias

* [Is Go object oriented?](https://flaviocopes.com/golang-is-go-object-oriented/)
