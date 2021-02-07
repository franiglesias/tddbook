# Course kata resuelta

## Outside in

El enfoque *outside in* es una buena forma de aplicar la disciplina TDD al desarrollo de features en software. Nos proporciona una metodología que nos permite trabajar con seguridad, sin necesidad de recurrir al *big upfront design*, y posponiendo muchas decisiones de implementación hasta tener una idea más clara de qué necesitamos.


## Enunciado

Como hemos visto en la definición de la kata, el objetivo es crear un programa que genere el informe académico de las alumnas de un supuesto curso de TDD realizado a partir de este libro. 

Lo que distingue al enfoque outside-in es que vamos a ir del output deseado (el informe), escribiendo el código hacia adentro, por decirlo así. En cada paso, iremos descubriendo los componentes que necesitamos.

Para el ejercicio partiremos del nivel de Use Case. En un proyecto real lo haríamos desde el propio endpoint que entrega los resultados. Esto supondría montar todo lo necesario para tener un controlador que pueda responder a una petición HTTP, pero probablemente añadiría demasiado ruido a la explicación del ejercicio. Así que nos lo saltaremos. Además, en una aplicación bien diseñada, el controlador no añadiría nada significativo a la resolución del problema.

Una de las cosas que se deriva de usar el enfoque *outside-in* es que tenemos que definir bien los contratos: qué información va a recibir la unidad bajo test y cuál será su output. Esto genera unos beneficios interesantes en la práctica ya que al hacerlo así nos permite desarrollar componentes en paralelo. Por ejemplo, cuando desarrollamos un endpoint, podemos acordar el contrato del endpoint y trabajar por separado y en paralelo tanto el backend como el consumidor de ese endpoint.

Cuando decimos que el enfoque *outside-in* nos permite prescindir del *big upfront design*, no queremos decir que sea necesario algo de diseño. Al fin y al cabo tenemos que pensar en cómo se usará el componente y en qué contexto.

En cualquier caso, para empezar asumiremos que los datos están en el sistema. Y necesitaremos decidir qué ejemplo de datos producirán el output esperado.

Nosotros hemos planteado dos ejemplos:

Estudiante A

| Tarea | Tipo | Calificación |
|:--|:--|:--:|
| Test 1 | Test | 6 |
| Kata 1 | Kata | 7 |
| Kata 2 | Kata | 8 |
| Test 2 | Test | 5 |
| Ensayo | Ensayo | 7 |
| Kata 3 | Kata | 7 |

Estudiante B

| Tarea | Tipo | Calificación |
|:--|:--|:--:|
| Test 1 | Test | 6 |
| Kata 1 | Kata | 7 |
| Kata 2 | Kata | 8 |
| Test 2 | Test | 5 |
| Ensayo | Ensayo | -- |
| Kata 3 | Kata | 7 |

Así que nuestro primer problema es decidir la forma en que se va a generar el informe. El cual, en el caso de Estudiante A tendrá estos resultados:

| Tipo | Entregas | Calificación | % | Pond |
|:--|:--:|:--:|:--:|:--:|
| Test | 2 | 5.5 | 20 | 1.1 |
| Kata | 3 | 7.3 | 70 | 5.1 |
| Ensayo | 1 | 7 | 10 | 0.7 |
| **Media del curso** | | | | **6.9** |
|  | | |  | **Apta** |

Y en el caso de Estudiante B

| Tipo | Entregas | Calificación | % | Pond |
|:--|:--:|:--:|:--:|:--:|
| Test | 2 | 5.5 | 20 | 1.1 |
| Kata | 3 | 7.3 | 70 | 5.1 |
| Ensayo | 0 | 0 | 10 | 0 |
| **Media del curso** | | | | **6.2** |
|  | | |  | **No Apta** |

## Definiendo el outcome

Nos interesa definir el output concreto que vamos a obtener. Así, por ejemplo, podríamos querer un objeto JSON para representar los resultados del informe de una forma fácilmente utilizable por sus potenciales consumidores.

Algo así:

```json
{
  "student": "Estudiante A",
  "assessment": [
    {
      "type": "test",
      "tasks": 2,
      "average": 5.5,
      "pct": 20,
      "weighted": 1.1
    },
    {
      "type": "kata",
      "tasks": 3,
      "average": 7.3,
      "pct": 70,
      "weighted": 5.1
    },
    {
      "type": "essay",
      "tasks": 1,
      "average": 7,
      "pct": 10,
      "weighted": 0.7
    }
  ],
  "average": 6.9,
  "qualification": "pass"
}
```

Una vez que tenemos esto definido, podemos empezar a desarrollar nuestro caso de uso. Nuestro primer test ya no tan minimalista como acostumbramos en la metodología clásica. Literalmente esperamos que el caso de uso `GenerateStudentReport` nos devuelva el informe.

```ruby
describe 'GenerateStudentReport' do
  it 'should generate student report' do
    expected_report = %{
{
  "student": "Estudiante A",
  "assessment": [
    {
      "type": "test",
      "tasks": 2,
      "average": 5.5,
      "pct": 20,
      "weighted": 1.1
    },
    {
      "type": "kata",
      "tasks": 3,
      "average": 7.3,
      "pct": 70,
      "weighted": 5.1
    },
    {
      "type": "essay",
      "tasks": 1,
      "average": 7,
      "pct": 10,
      "weighted": 0.7
    }
  ],
  "average": 6.9,
  "qualification": "pass"
}
}
    generate_report = GenerateStudentReport.new
    report = generate_report.of_student('Estudiante A')
    expect(report).to eq(expected_report)
  end

end
```

Para hacer pasar este primer test, haremos que el caso de uso devuelva el mismo informe incondicionalmente.

```ruby
class GenerateStudentReport
  def of_student(student)
   %{
{
  "student": "Estudiante A",
  "assessment": [
    {
      "type": "test",
      "tasks": 2,
      "average": 5.5,
      "pct": 20,
      "weighted": 1.1
    },
    {
      "type": "kata",
      "tasks": 3,
      "average": 7.3,
      "pct": 70,
      "weighted": 5.1
    },
    {
      "type": "essay",
      "tasks": 1,
      "average": 7,
      "pct": 10,
      "weighted": 0.7
    }
  ],
  "average": 6.9,
  "qualification": "pass"
}
}
  end
end
```

Es buen momento para refactorizar un poco el test y que sea más cómodo de leer:

```ruby
# frozen_string_literal: true

require_relative '../src/generate_student_report'

describe 'GenerateStudentReport' do
  it 'should generate student report' do
    generate_report = GenerateStudentReport.new
    report = generate_report.of_student('Estudiante A')
    expect(report).to eq(expected_report)
  end

end

def expected_report
  %{
{
  "student": "Estudiante A",
  "assessment": [
    {
      "type": "test",
      "tasks": 2,
      "average": 5.5,
      "pct": 20,
      "weighted": 1.1
    },
    {
      "type": "kata",
      "tasks": 3,
      "average": 7.3,
      "pct": 70,
      "weighted": 5.1
    },
    {
      "type": "essay",
      "tasks": 1,
      "average": 7,
      "pct": 10,
      "weighted": 0.7
    }
  ],
  "average": 6.9,
  "qualification": "pass"
}
}
end
```

En este momento podemos considerar este test como un test de aceptación de la feature "generar un informe de una estudiante".

Lo que tenemos ahora es un stub del caso de uso. Es decir un doble de test que devuelve un valor prefijado. Esta metodología se conoce también como enfoque "mockista" por esta razón. Cada nivel de abstracción de la implementación se define primero mediante un doble de test, con el objetivo de definir su interfaz.

En este punto señalaría que en este caso los dobles o mocks los voy a hacer sin utilizar una librería. Esto hace que la solución que presento sea menos purista, pero en el fondo no veo muchos motivos para usar librerías específicas de mocking. Sencillamente se van introduciendo implementaciones inflexibles que, poco a poco, van evolucionando hacia implementaciones completas en el contexto del desarrollo de la feature.

Para desarrollar la implementación concreta lo que haremos será ir diseñando/descubriendo los servicios que necesitamos en cada nivel de abstracción.

Así, en este ejemplo, tenemos estos grandes pasos:

* Obtener la información de las tareas entregadas por la estudiante identificada por su id.
* Calcular la nota media y decidir si es apta o no.
* Devolver el informe en el formato solicitado.

Esto nos sugiere que tendremos algún servicio del cual obtendremos las tareas, con ellas montaremos el informe y devolveremos éste en el formato deseado.

Podríamos expresarlo así en líneas generales:

```ruby
class GenerateStudentReport
  def of_student(student)
    tasks = @tasks_repository.find_all_of_student(student)
    report = @generate_report.with(tasks)
  end
end
```

Podemos observar que tenemos un diseño básico de nuestro caso de uso, con los colaboradores que esperamos que necesite (servicios de dominio) y también con varias entidades que debe manejar.

* `student`, en este punto será un id.
* `task`, cada una de las tareas cumplimentadas por nuestra estudiante
* `report`, el informe generado a partir de las tareas.

Los servicios:

* `tasks_repository`, será un repositorio de las tareas
* `generate_report`, es un servicio factoría que creará el informe a partir de la información enviada

De momento no sabemos cómo se implementarán estos servicios, por lo que los doblaremos, pero sí sabemos cómo usarlos.

Otra consideración es la entrega del resultado dado que el test espera un JSON y el caso de uso devolverá un objeto. Lo suyo sería que el consumidor del caso de uso espere una cierta representación serializable (JSON en este caso) y no el objeto de dominio tal cual.

Para no complicar el ejercicio vamos a hacerlo así:

```ruby
class GenerateStudentReport
  def of_student(student)
    tasks = @tasks_repository.find_all_of_student(student)
    report = @generate_report.with(tasks)
    report.to_json
  end
end
```

## Introduciendo dobles

Lo que nos interesa ahora es introducir los servicios que hemos definido y que de momento doblaremos.

Ahora mismo el test no se ejecutará, así que necesitamos tener implementaciones mínimas de estos servicios y conseguir que el test se ejecute.

Primer paso:

```ruby
class GenerateStudentReport
  def initialize
    @tasks_repository = TaskRepository.new
    @generate_report = GenerateReport.new
  end
  
  def of_student(student)
    tasks = @tasks_repository.find_all_of_student(student)
    report = @generate_report.with(tasks)
    report.to_json
  end
end
```

Ahora generamos las clases `TaskRepository`:

```ruby
class TaskRepository
  def find_all_of_student(student)

  end
end
```

y `GenerateReport`:

```ruby
class GenerateReport
  def with(tasks)

  end
end
```

Y las requerimos:

```ruby
# frozen_string_literal: true

require_relative 'domain/generate_report'
require_relative 'domain/task_repository'

class GenerateStudentReport
  def initialize
    @tasks_repository = TaskRepository.new
    @generate_report = GenerateReport.new
  end

  def of_student(student)
    tasks = @tasks_repository.find_all_of_student(student)
    report = @generate_report.with(tasks)
    report.to_json
  end
end
```

Necesitamos todavía un objeto Report que tenga el método `to_json`.

```ruby
class Report
  def to_json

  end
end
```

De hecho, este objeto es el que devolverá la representación JSON que queremos así que vamos a hacer un stub del método para hacer que el test pueda empezar a pasar:

```ruby
class Report
  def to_json
    %{
{
  "student": "Estudiante A",
  "assessment": [
    {
      "type": "test",
      "tasks": 2,
      "average": 5.5,
      "pct": 20,
      "weighted": 1.1
    },
    {
      "type": "kata",
      "tasks": 3,
      "average": 7.3,
      "pct": 70,
      "weighted": 5.1
    },
    {
      "type": "essay",
      "tasks": 1,
      "average": 7,
      "pct": 10,
      "weighted": 0.7
    }
  ],
  "average": 6.9,
  "qualification": "pass"
}
}
  end
end
```

El servicio que entrega una instancia de `Report` es `GenerateReport`, así que también vamos a hacer un stub con el método `with` que devuelva un `Report`, que ahora mismo no hace nada más que contener un reporte fijo.

```ruby
require_relative 'report'

class GenerateReport
  def with(tasks)
    Report.new
  end
end
```

Con estos cambios, nuestro test ya pasa. Obviamente no está implementada la funcionalidad, pero ya tenemos el caso de uso definido. Lo que viene ahora es bajar un nivel de abstracción y empezar a implementar los servicios y entidades.

## Empezando a implementar

El siguiente paso consiste en comenzar a implementar el comportamiento de los servicios. Una cosa que tenemos que determinar es qué servicio nos conviene implementar y cuál mantener todavía en estado de doble o *mock*.

En nuestro ejemplo puede tener sentido empezar implementando `GenerateReport`. Este servicio contiene la lógica core del caso de uso. Empezar a escribirlo nos aportará información que necesitamos para decidir qué debería entregar `TaskRepository`, por una parte, y nos obligará a implementar el comportamiento de `Report`.

```ruby
require_relative 'report'

class GenerateReport
  def with(tasks)
    report = Report.new tasks
  end
end
```

En este punto, si nos fijamos en el formato del informe, nos falta obtener información del estudiante. Es decir, el servicio debería ser más bien:

```ruby
require_relative 'report'

class GenerateReport
  def with(student, tasks)
    report = Report.new student, tasks
  end
end
```

Y por tanto tendremos que modificar la llamada en el caso de uso:

```ruby
require_relative 'domain/generate_report'
require_relative 'domain/task_repository'

class GenerateStudentReport
  def initialize
    @tasks_repository = TaskRepository.new
    @generate_report = GenerateReport.new
  end

  def of_student(student)
    tasks = @tasks_repository.find_all_of_student(student)
    report = @generate_report.with(student, tasks)
    report.to_json
  end
end
```

Una vez resuelto esto, podemos comprobar que el test deja de pasar porque necesitamos implementar una constructora en `Report` que admita los parámetros. En Ruby esto lanza un error, pero en PHP, por ejemplo, el test pasaría igualmente.

```ruby
class Report
  def initialize(student, tasks)
    @student = student
    @tasks = tasks
  end
  
  def to_json
    %{
{
  "student": "Estudiante A",
  "assessment": [
    {
      "type": "test",
      "tasks": 2,
      "average": 5.5,
      "pct": 20,
      "weighted": 1.1
    },
    {
      "type": "kata",
      "tasks": 3,
      "average": 7.3,
      "pct": 70,
      "weighted": 5.1
    },
    {
      "type": "essay",
      "tasks": 1,
      "average": 7,
      "pct": 10,
      "weighted": 0.7
    }
  ],
  "average": 6.9,
  "qualification": "pass"
}
}
  end
end
```

Ahora querríamos que `Report` construyese la respuesta a partir de la información que se le pasa en la construcción. Es decir, que tendría que recibir una colección de `Tasks` y realizar varios cálculos internamente para agregar las evaluaciones y generar el informe.

Por lo que podemos ver, necesitamos disponer de algunas propiedades más en `Report`, como `average` y `qualification`. Además necesitaremos también algún objeto que agregue las evaluaciones de los distintos tipos de tareas. Vamos a ver cómo lo logramos.

Esto es más o menos lo que queremos:

```ruby
require 'json'

class Report
  def initialize(student, tasks)
    @student = student
    @tasks = tasks
    @average = 0
    @qualification = 'n/a'
    @assessments = []
  end

  def to_json
    report = {
        student: @student,
        average: @average,
        qualification: @qualification
    }

    @assessments.each do |assessment|
      report[assessment].push(assessment)
    end

    report.to_json
  end
end
```

Si ejecutamos el test veremos que falla, como era de esperar ya que no tenemos los datos aún. Sin embargo, también vemos que no coincide exactamente el aspecto del JSON generado con el que espera, ya que este lo hemos formateado para que fuese fácil de leer.

Para tener esto en cuenta, lo arreglamos en el test, añadiendo una línea `JSON.parse(expected).to_json` que corrige ese inconveniente.

```ruby
# frozen_string_literal: true

require_relative '../src/generate_student_report'

describe 'GenerateStudentReport' do
  it 'should generate student report' do
    generate_report = GenerateStudentReport.new
    report = generate_report.of_student('Estudiante A')
    expect(report).to eq(expected_report)
  end
end

def expected_report
  expected = %{
{
  "student": "Estudiante A",
  "assessment": [
    {
      "type": "test",
      "tasks": 2,
      "average": 5.5,
      "pct": 20,
      "weighted": 1.1
    },
    {
      "type": "kata",
      "tasks": 3,
      "average": 7.3,
      "pct": 70,
      "weighted": 5.1
    },
    {
      "type": "essay",
      "tasks": 1,
      "average": 7,
      "pct": 10,
      "weighted": 0.7
    }
  ],
  "average": 6.9,
  "qualification": "pass"
}
}
  JSON.parse(expected).to_json
end
```


Un problema que podríamos estar teniendo es que quizá estamos asumiendo que `Report` tendrá la responsabilidad de generar los `Assessment` de cada tipo de `Task`. También estamos asumiendo que contendrá de hecho todas las `Task` cuando realmente no se necesitan más que para generar los `Assessment`.

Pienso que necesitamos hacer algunos cambios antes de seguir:

```ruby
require 'json'

class Report
  def initialize(student, assessments)
    @student = student
    @average = 0
    @qualification = 'n/a'
    @assessments = assessments
  end

  def to_json
    assessment = []
    assessments.each do |assess|
      assessment.append(assess.to_hash)
    end

    report = {
        :student => student,
        :assessment => assessment,
        :average => average,
        :qualification => qualification,
    }

    report.to_json
  end

  private

  attr_reader :student, :average, :qualification, :assessments
end
```

En el servicio:

```ruby
require_relative 'report'

class GenerateReport
  def with(student, tasks)
    report = Report.new student, assessments(tasks)
  end
  
  def assessments(tasks)
    []
  end
end
```

En este punto nos encontramos con varias cuestiones. 

Para empezar, necesitaríamos recibir la colección de `Tasks` en `GenerateReport`. Dado que `TaskRepository` es la encargada de proporcionarlas, podemos hacer un *stub*.

Recordemos los datos de 'Estudiante A':

| Tarea | Tipo | Calificación |
|:--|:--|:--:|
| Test 1 | Test | 6 |
| Kata 1 | Kata | 7 |
| Kata 2 | Kata | 8 |
| Test 2 | Test | 5 |
| Ensayo | Ensayo | 7 |
| Kata 3 | Kata | 7 |

`Task` tiene al menos tres campos, más un campo que lo relacione con la estudiante a la que pertenecen. Por otra parte, también tenemos que considerar la ponderación, que va asociada al tipo.

Básicamente queremos poder hacer esto (por el momento)

```ruby
require_relative 'task'

class TaskRepository
  def initialize
    @tasks = []
    @tasks.append Task.new 'Estudiante A', 'Test 1', 'Test', 6
    @tasks.append Task.new 'Estudiante A', 'Kata 1', 'Kata', 7
    @tasks.append Task.new 'Estudiante A', 'Kata 2', 'Kata', 8
    @tasks.append Task.new 'Estudiante A', 'Test 2', 'Test', 5
    @tasks.append Task.new 'Estudiante A', 'Ensayo 1', 'Essay', 7
    @tasks.append Task.new 'Estudiante A', 'Kata 3', 'Kata', 7
  end

  def find_all_of_student(student)
    @tasks.select do |task|
      task.belongs_to? student
    end
  end
end
```

Es decir, hacer que el repositorio sea un *stub* que nos devuelva las tareas calificadas. Por el momento, con esto es suficiente.

Lo siguiente será implementar una constructora en `Task` y un método `belongs_to?` que nos diga si la tarea pertenece o no al estudiante.

```ruby
class Task
  def initialize(student, task, type, qualification )
    @student = student
    @name = task
    @type = type
    @qualification = qualification
  end
  
  def belongs_to?(student)
    @student == student
  end
end
```

Lo que nos queda es implementar la agregación de los resultados. Podríamos modelarlo así para empezar:

```ruby
require_relative 'report'

class GenerateReport
  def with(student, tasks)
    report = Report.new student, assessments(tasks)
  end

  def assessments(tasks)
    [
        Assessment.new(type: 'Essay', tasks: tasks, weight: 10, minimum: 1),
        Assessment.new(type: 'Kata', tasks: tasks, weight: 70, minimum: 3),
        Assessment.new(type: 'Test', tasks: tasks, weight: 20, minimum: 2)
    ]
  end
end
```

Ahora tocaría implementar la lógica en `Assessment`.  Se trataría de seleccionar las `Task` por su `type` y aplicar las reglas al resultado.

```ruby
class Assessment

  def initialize(type:, tasks:, weight:, minimum:)
    @type = type
    @tasks = tasks.select { |task| task.is_type? type }
    @weight = weight
    @minimum = minimum
  end

  def to_hash
    {
        :type => type.downcase,
        :tasks => number_of_tasks,
        :average => average,
        :pct => weight,
        :weighted => weighted
    }
  end

  def average
    tasks.reduce(0) { |sum, task| sum+=task.qualification}.fdiv(tasks.size)
  end

  def weighted
    average * weight / 100
  end

  private

  def number_of_tasks
    @tasks.size
  end

  attr_reader :type, :tasks, :weight, :minimum
end
```

`Report` también necesita algunos cambios ya que no se hacen los cálculos todavía:

```ruby
require 'json'

class Report
  def initialize(student, assessments)
    @student = student
    @average = 0
    @qualification = 'n/a'
    @assessments = assessments
  end

  def to_json
    assessment = []
    assessments.each do |assess|
      assessment.append(assess.to_hash)
    end

    report = {
        :student => student,
        :assessment => assessment,
        :average => ('%.1f' % average).to_f,
        :qualification => qualification,
    }

    report.to_json
  end

  def average
    assessments.reduce(0) { |sum, assessment| sum+= assessment.weighted }
  end

  def qualification
    average >= 5 ? 'pass' : 'no pass'
  end

  private

  attr_reader :student, :assessments
end
```

Nuestro test, sin embargo, presenta algunos inconvenientes. Esperamos un JSON y ahora mismo lo estamos evaluando como `string`, lo que puede introducir distorsiones si tenemos un espacio de más o de menos.

En ese caso es mejor montar el ejemplo usando una estructura del lenguaje que podamos convertir a JSON al vuelo. Por ejemplo, en Ruby es ideal usar un Hash.

```ruby
# frozen_string_literal: true

require_relative '../src/generate_student_report'

describe 'GenerateStudentReport' do
  it 'should generate student report' do
    generate_report = GenerateStudentReport.new
    report = generate_report.of_student('Estudiante A')
    expect(JSON.parse(report)).to eq(JSON.parse(expected_report))
  end

end

def expected_report
  expected = {
      :student => "Estudiante A",
      :assessment => [
          {
              :type => "test",
              :tasks => 2,
              :average => 5.5,
              :pct => 20,
              :weighted => 1.1
          },
          {
              :type => "kata",
              :tasks => 3,
              :average => 7.3,
              :pct => 70,
              :weighted => 5.1
          },
          {
              :type => "essay",
              :tasks => 1,
              :average => 7,
              :pct => 10,
              :weighted => 0.7
          }
      ],
      :average => 6.9,
      :qualification => "pass"
  }

  expected.to_json
end
```

Con estos cambios el test pasa, con lo que tenemos un generador de informes básico implementado.

## ¿Podemos hacer refactor?

Los tests en verde nos permiten hacer refactor. En el modelo de TDD clásica el refactor tiene como objetivo mejorar el diseño. En cambio, en el enfoque *outside in* el diseño no espera a la fase de refactor y se va construyendo incluso con los tests en rojo.

Sin embargo, siempre tenemos oportunidades para refactorizar pues a medida que avanzamos iremos obteniendo nueva información y generando nuevas ideas. Por otro lado, todavía tenemos que avanzar en la implementación y es conveniente preparar el código para los cambios inmediatos.

Aquí veo un candidato a refactor:

```ruby
  def assessments(tasks)
    [
        Assessment.new(type: 'Test', tasks: tasks, weight: 20, minimum: 2),
        Assessment.new(type: 'Kata', tasks: tasks, weight: 70, minimum: 3),
        Assessment.new(type: 'Essay', tasks: tasks, weight: 10, minimum: 1),
    ]
  end
```

Cuando creo los assessments veo que tres de los cuatro parámetros están relacionados. El tipo de tarea se asocia con un peso y un número requerido de entregas.

```
class TaskType
  def initialize(type:, weight:, minimum:)
    @type = type
    @weight = weight
    @minimum = minimum
  end
end
```

Podemos usar métodos factoría ya que los valores permitidos son realmente pocos:

```ruby
class TaskType
  attr_reader type, weight, minimum
  
  def initialize(type:, weight:, minimum:)
    @type = type
    @weight = weight
    @minimum = minimum
  end

  class << self
    def test
      TaskType.new type: 'Test', weight: 20, minimum: 2
    end

    def kata
      TaskType.new type: 'Kata', weight: 70, minimum: 3
    end

    def essay
      TaskType.new type: 'Essay', weight: 10, minimum: 1
    end
  end
end
```

También usaremos un método factoría en `Assessment`, de modo que podamos exponer una interfaz que use el nuevo `TaskType` sin afectar en nada al código en los tests:

```ruby
class Assessment


  def initialize(type:, tasks:, weight:, minimum:)
    @type = type
    @tasks = select_tasks_of_type(tasks, type)
    @weight = weight
    @minimum = minimum
  end

  def self.of_tasks_of_type(type:, tasks:)
    Assessment.new type: type.type, tasks:tasks, weight: type.weight, minimum: type.minimum
  end

  def to_hash
    {
        :type => type.downcase,
        :tasks => number_of_tasks,
        :average => ('%.1f' % average).to_f,
        :pct => weight,
        :weighted => ('%.1f' % weighted).to_f
    }
  end

  def average
    tasks.reduce(0) do |sum, task|
      sum + task.qualification
    end.fdiv(tasks.size)
  end

  def weighted
    average * weight / 100
  end

  private

  def select_tasks_of_type(tasks, type)
    tasks.select do |task|
      task.is_type? type
    end
  end

  def number_of_tasks
    @tasks.size
  end

  attr_reader :type, :tasks, :weight, :minimum
end
```

Y ahora podremos usar este nuevo método factoría:

```ruby
require_relative 'report'
require_relative 'assessment'
require_relative 'task_type'

class GenerateReport
  def with(student, tasks)
    Report.new student, assessments(tasks)
  end

  def assessments(tasks)
    [
      Assessment.of_tasks_of_type(type: TaskType.test, tasks: tasks),
      Assessment.of_tasks_of_type(type: TaskType.kata, tasks: tasks),
      Assessment.of_tasks_of_type(type: TaskType.essay, tasks: tasks),
    ]
  end
end
```

Un detalle a tener en cuenta es que voy introduciendo parámetros con nombre en las interfaces de los métodos. Esto contribuye a un código más fácil de entender y que, además, no está acoplado al orden de los parámetros.

