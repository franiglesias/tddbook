# To-do list

## Outside in mockist

Outside-in TDD, también llamado *mockist* o *London school*, es una aproximación al desarrollo dirigido por test que busca implementar *features* en el software partiendo de un test de aceptación y procediendo hacia el interior del software.

En lugar de diseñar el sistema en la fase de refactoring, como hace el enfoque clásico, la aproximación outside-in lo hace durante la fase *en rojo*, es decir, cuando el test de aceptación todavía no está fallando. 

Así por ejemplo, en el desarrollo de un API, primero se escribiría un test de aceptación contra el API, como si el test fuese un consumidor más de ese API. El siguiente paso sería diseñar y testear el controlador, luego el caso de uso, y luego los servicios y entidades manejados por ese caso de uso, hasta llegar al dominio de la aplicación. En todos los casos haríamos mocks de las dependencias, de modo que estaríamos testeando los mensajes entre objetos de la aplicación.

Para hacerlo, la metodología se basa en dos ciclos:

* **Ciclo test de aceptación**. Se trata de un test que describe la feature completa en el nivel *end to end*, usando implementaciones reales de los componentes del sistema, excepto aquellas que definen límites del mismo. Los fallos de los test en este nivel nos sirven como guía para saber qué es lo próximo que tenemos que desarrollar.
* **Ciclo de tests unitarios**. Una vez que tenemos un fallo en el test de aceptación que nos indica qué tenemos que desarrollar, daremos un paso hacia el interior del sistema y usaremos tests unitarios para desarrollar el componente correspondiente, *mockeando* aquellos colaboradores o dependencias que éste pueda necesitar. Cuando terminamos, volvemos al ciclo del test de aceptación para encontrar cual será nuestro próximo objetivo.

## Enunciado

El ejercicio consiste en desarrollar un API para una aplicación de listas to-do. Básicamente queremos implementar los siguientes funcionalidades:

### US 1

* As a User
* I want to add tasks to a to-do list
* So that, I can organize my task

### US 2

* As a User
* I want to see the task in my to-do list
* So that, I can know what I have to do next

### US 3

* As a User
* I want to check a task when it is done
* So that, I can see my progress

### TO-DO List examples for acceptance test

1. Write a test that fails (done)
2. Write Production code that makes the test pass
3. Refactor if there is opportunity

```
POST /api/todo
[task:Write a test that fails]

POST /api/todo
[task:Write Production code that makes the test pass]

POST /api/todo
[task:Refactor if there is opportunity]

PATCH /api/todo/1
[done:true]

GET /api/todo
[√] 1. Write a test that fails
[ ] 2. Write Production code that makes the test pass
[ ] 3. Refactor if there is opportunity
```

To simplify the test we are expecting that the list of tasks will be a string with all data needed.

We will use the `App\Lib\FileStorageEngine` as persistence mechanism.

### Technicalities

Endpoints:

* `POST /api/todo` (201 created)
* `GET /api/todo` (200)
* `PATCH /api/todo/{taskId}` (200)

## Desarrollo

En esta ocasión vamos a desarrollar la kata en PHP, usando [este repositorio](https://github.com/franiglesias/tb), ya que contiene una instalación preparada de PHP y Symfony, lo que nos proporciona un framework HTTP con el que empezar a desarrollar:

`https://github.com/franiglesias/tb`

En el repositorio ya tenemos un test básico que utilizaremos como punto de partida:

```php
<?php

declare(strict_types=1);

namespace App\Tests\Katas\TodoList;

use Symfony\Bundle\FrameworkBundle\Test\WebTestCase;

class TodoListAcceptanceTest extends WebTestCase
{

    protected function setUp(): void
    {
        $this->resetRepositoryData();
    }

    protected function tearDown(): void
    {
        $this->resetRepositoryData();
    }

    private function resetRepositoryData(): void
    {
        if (file_exists('repository.data')) {
            unlink('repository.data');
        }
    }
}
```

Los métodos setUp y tearDown gestionan un archivo de datos necesario para la implementación de un sistema muy básico de persistencia (FileStorageEngine) que necesitaremos para poder implementar la feature completa.

## Diseñando el test de aceptación

Necesitamos un test de aceptación que describa cómo tiene que funcionar la aplicación. Para ello tenemos un ejemplo. Estas son las tareas que vamos a poner en nuestra lista:

```
1. Write a test that fails (done)
2. Write Production code that makes the test pass
3. Refactor if there is opportunity
```

Los pasos que el test tiene que ejecutar, por tanto, son anotar las tres tareas, marcar la primera como hecha y ser capaz de mostrarnos la lista. Estas operaciones son:

```
POST /api/todo
payload: [task:Write a test that fails]

POST /api/todo
payload: [task:Write Production code that makes the test pass]

POST /api/todo
payload: [task:Refactor if there is opportunity]

PATCH /api/todo/1
payload: [done:true]

GET /api/todo
Response:
[√] 1. Write a test that fails
[ ] 2. Write Production code that makes the test pass
[ ] 3. Refactor if there is opportunity
```

Para simplificar, la respuesta será una representación de cada tarea en una línea de texto con el formato que se puede ver arriba.

### Empezando por el final: cuál será el resultado esperado

Para empezar a diseñar nuestro test, comenzamos por el final, es decir, por la llamada para recuperar la lista de tareas. A partir de ahí iremos reproduciendo los pasos previos necesarios para llegar a ese estado.

```php
    /** @test */
    public function shouldAllowAddingTaskCompleteAndRetrieveTheList(): void
    {
        $expectedList = [
            '[√] 1. Write a test that fails',
            '[ ] 2. Write Production code that makes the test pass',
            '[ ] 3. Refactor if there is opportunity',
        ];

        $client = self::createClient();
        $client->request('GET', '/api/todo');
        $response = $client->getResponse();
        $list = json_decode($response->getContents(), true);
        
        self::assertEquals(Response::HTTP_OK, $response->getStatusCode());
        self::assertEquals($list, $expectedList);
    }
```

Para llegar a este punto, necesitaríamos haber hecho una petición a la API por cada tarea y una petición más para marcar una tarea como completada. De este modo, el test quedará completo:

```php
    /** @test */
    public function shouldAllowAddingTaskCompleteAndRetrieveTheList(): void
    {
        $expectedList = [
            '[√] 1. Write a test that fails',
            '[ ] 2. Write Production code that makes the test pass',
            '[ ] 3. Refactor if there is opportunity',
        ];

        $client = self::createClient();

        $taskDescription = 'Write a test that fails';
        $client->request(
            'POST',
            '/api/todo',
            [],
            [],
            ['CONTENT-TYPE' => 'json/application'],
            json_encode(['task' => $taskDescription])
        );

        $taskDescription = 'Write Production code that makes the test pass';
        $client->request(
            'POST',
            '/api/todo',
            [],
            [],
            ['CONTENT-TYPE' => 'json/application'],
            json_encode(['task' => $taskDescription])
        );

        $taskDescription = 'Refactor if there is opportunity';
        $client->request(
            'POST',
            '/api/todo',
            [],
            [],
            ['CONTENT-TYPE' => 'json/application'],
            json_encode(['task' => $taskDescription])
        );

        $taskId = 1;
        $client->request(
            'PATCH',
            '/api/todo/'.$taskId,
            [],
            [],
            ['CONTENT-TYPE' => 'json/application'],
            json_encode(['done' => true])
        );

        $client->request('GET', '/api/todo');
        $response = $client->getResponse();
        $list = json_decode($response->getContent(), true);

        self::assertEquals($list, $expectedList);
    }
```

Este es el test completo. Si lo ejecutamos empezaremos a ver fallos acerca de problemas de la configuración del framework. Lo primero que tenemos que hacer es conseguir que el test falle por el motivo correcto, que no es otro sino que al pedir la lista de tareas la respuesta `$list` no sea la que esperamos.

### Resolviendo los detalles necesarios en el framework

El primer error nos dice que no hay ningún controlador en la ubicación esperada por el framework. En nuestro caso, además de eso, queremos montar una solución con una arquitectura limpia. Según eso, los controladores del API deberían estar en la capa de Infraestructura, por lo que vamos a cambiar la configuración de services.yaml de Symfony para que espere encontrar los controladores en otra ruta. En concreto, yo prefiero ponerlos en:

`src/Infrastructure/EntryPoint/Api/Controller`

Por tanto, **services.yaml** quedará así:

```yaml
    # controllers are imported separately to make sure services can be injected
    # as action arguments even if you don't extend any base controller class
    App\Infrastructure\EntryPoint\Api\Controller\:
        resource: '../src/Infrastructure/EntryPoint/Api/Controller'
        tags: ['controller.service_arguments']

```

Si ejecutamos el test de nuevo, veremos que el mensaje de error ha cambiado, lo cual indica que hemos intervenido de manera correcta. Ahora nos indica que no hay controladores en el nuevo lugar definido, así que vamos a crear una clase `TodoListController` en la ubicación: `\App\Infrastructure\EntryPoint\Api\Controller\TodoListController`.

```
<?php

declare(strict_types=1);

namespace App\Infrastructure\EntryPoint\Api\Controller;


class TodoListController
{

}
```

Y de momento, la dejamos así. Ejecutamos el test para ver qué nos dice. Tenemos dos tipos de mensajes. Por una parte, varias excepciones que os indican que no se encuentras las rutas de los endpoints, las cuales no hemos definido todavía. 

Por otra parte, el test nos indica que la llamada al endpoint devuelve NULL y, por tanto, no tenemos todavía la lista de tareas.

Así que necesitamos que nuestro controlador sea capaz de manejar estas rutas antes de nada. La primera ruta que no encuentra es la de `POST /api/todo`, en la que añadimos tareas a la lista. Para ello, añadiremos una entrada en el archivo **routes.yaml**.

```yaml
api_add_task:
  path: /api/todo
  controller: App\Infrastructure\EntryPoint\Api\Controller\TodoListController::addTask
  methods: ['POST']

```

Una vez añadida la ruta, ejecutamos de nuevo el test de aceptación. Lo adecuado es lanzar el test con cada cambio para confirmar que falla por el motivo esperado. En este caso, esperamos que nos diga que no tenemos un método `addTask` en `TodoListController`, y lo tenemos que añadir para avanzar.

```php
<?php

declare(strict_types=1);

namespace App\Infrastructure\EntryPoint\Api\Controller;


class TodoListController
{

    public function addTask()
    {
        throw new \RuntimeException(sprintf('Implement %s::%s', __CLASS__, __METHOD__));
    }
}
```

Como puedes ver, en el método lanzo una excepción que me permitirá ver cuando se está llamando al controlador real. De este modo, sabré con seguridad si es lo que tengo que implementar a continuación. Esta técnica se la he visto a Sandro Mancuso en su vídeo sobre *Outside-in* y me parece muy útil. Aunque muchas veces el lenguaje te indicará lo mismo, hay ocasiones en las que no es tan evidente.

Al relanzar el test, el primer error nos dice literalmente que hay que implementar el `addTask`.

Y esto nos lleva al ciclo de tests unitarios.

## Primer test unitario

El primer test unitario nos lleva un paso hacia el interior de la aplicación. El test de aceptación nos situaba *fuera* de la aplicación, mientras que el controlador se encuentra en la capa de Infraestructura. Lo que vamos a hacer es desarrollar el controlador con un test unitario, pero en lugar de usar el enfoque clásico, implementar una solución y luego usar la etapa de refactor para diseñar los componentes, empezaremos por esto punto.

Es decir, lo que queremos hacer es diseñar qué componentes queremos que use el controlador para devolver una respuesta, mockearlos en el test e implementar sólo el código del controlador.

En este ejemplo, voy a suponer que cada controlador invoca un UseCase en la capa de aplicación. Para que sea todo se entienda mejor no usaré un bus de comandos como haría en una aplicación real, sino que invocaré directamente los use case.

Este es mi primer test unitario:

```php
<?php

declare(strict_types=1);

namespace App\Tests\Infrastructure\EntryPoint\Api\Controller;

use App\Infrastructure\EntryPoint\Api\Controller\TodoListController;
use PHPUnit\Framework\TestCase;
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;

class TodoListControllerTest extends TestCase
{

    /** @test */
    public function shouldAddTask(): void
    {
        $addTask = $this->createMock(AddTaskHandler::class);
        $addTask
            ->expects(self::once())
            ->method('execute')
            ->with('Task Description');
            
        $todoListController = new TodoListController($addTask);

        $request = new Request(
            [],
            [],
            [],
            [],
            [],
            ['CONTENT-TYPE' => 'json/application'],
            json_encode(['task' => 'Task Description'], JSON_THROW_ON_ERROR)
        );
        
        $response = $todoListController->addTask($request);
        
        self::assertEquals(Response::HTTP_CREATED, $response->getStatusCode());
    }
}
```

Por un lado, en el test simulamos una request con un payload JSON, que será la que nos proporcione los datos necesario. El mock de AddTaskHandler simula que simplemente llamamos a su método execute pasándole como parámetro la descripción de la tarea proporcionada en la llamada al endpoint.

Gracias al uso de Mocks no tenemos que preocuparnos de qué pasa más adentro en la aplicación. Lo que estamos testeando es el modo en el que el controlador obtiene los datos relevantes y se los pasa al Caso de Uso para que éste haga lo que tenga que hacer. Si no hay ningún problema, el controlador retornará una respuesta 201, indicando que el recurso ha sido creado. No nos vamos a ocupar en este ejemplo de todos los posibles fallos que podrían ocurrir, pero puedes hacerte una idea de cómo se gestionaría.

Ahora ejecutamos el test `TodoListController` para asegurar que falla por las razones esperadas: que no se llama a `AddTaskHandler` y que no se devuelve el código HTTP 201.

En este caso, el primer error es que no tenemos una clase `AddTaskHandler` que mockear, así que la creamos. La vamos a poner en `App\Application`.

```
<?php

declare(strict_types=1);

namespace App\Application;


class AddTaskHandler
{

}
```

Tiramos de nuevo el test, que nos indicará que no existe un método execute que se pueda mockear. Lo añadimos, pero dejamos que lance una excepción para decirnos que no está implementado. Veremos la utilidad de ello dentro de un rato.

```php
<?php

declare(strict_types=1);

namespace App\Application;


class AddTaskHandler
{
    public function execute(string $taskDescription)
    {
        throw new \RuntimeException(sprintf('Implement %s::%s', __CLASS__, __METHOD__));
    }
}
```

Esto no afectará al test actual porque nunca llega a ejecutarse el método real, sino su doble. En cambio, si todo ha ido bien, en este punto el test nos pedirá que implementemos el método `addTask` del controlador.

```php
<?php

declare(strict_types=1);

namespace App\Infrastructure\EntryPoint\Api\Controller;


use App\Application\AddTaskHandler;
use Symfony\Component\HttpFoundation\JsonResponse;
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;

class TodoListController
{

    private AddTaskHandler $addTask;

    public function __construct(AddTaskHandler $addTask)
    {
        $this->addTask = $addTask;
    }

    public function addTask(Request $request): Response
    {
        $body = json_decode($request->getContent(), true);

        $this->addTask->execute($body['task']);

        return new JsonResponse('', Response::HTTP_CREATED);
    }
}
```

Este código hace pasar el test. Puesto que es relativamente sencillo no vamos a hacerlo en pasos muy pequeños.

Vamos a aprovechar que el test está en verde para refactorizarlo un poco. Sabemos que tendremos que añadir más test en este TestCase y que habrá que instanciar el controlador varias veces, así que vamos a hacernos la vida un poco más fácil para el futuro próximo. Tras asegurarnos de que sigue pasando, el test queda así:

```php
<?php

declare(strict_types=1);

namespace App\Tests\Infrastructure\EntryPoint\Api\Controller;

use App\Application\AddTaskHandler;
use App\Infrastructure\EntryPoint\Api\Controller\TodoListController;
use PHPUnit\Framework\TestCase;
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;

class TodoListControllerTest extends TestCase
{
    private AddTaskHandler $addTaskHandler;
    private TodoListController $todoListController;


    protected function setUp(): void
    {
        $this->addTaskHandler = $this->createMock(AddTaskHandler::class);
        $this->todoListController = new TodoListController($this->addTaskHandler);
    }
    
    /** @test */
    public function shouldAddTask(): void
    {
        $this->addTaskHandler
            ->expects(self::once())
            ->method('execute')
            ->with('Task Description');

        $request = new Request(
            [],
            [],
            [],
            [],
            [],
            ['CONTENT-TYPE' => 'json/application'],
            json_encode(['task' => 'Task Description'], JSON_THROW_ON_ERROR)
        );

        $response = $this->todoListController->addTask($request);

        self::assertEquals(Response::HTTP_CREATED, $response->getStatusCode());
    }
}
```

Es momento de volver a ejecutar el test de aceptación.

## De vuelta en el ciclo de aceptación

Al hacer que el test TodoListController esté pasando, no tenemos más trabajo que hacer en este nivel, así que volvemos al test de aceptación para ver si sigue fallando algo y qué es lo que falla.

En este punto, lo que nos dice es que `AddTaskHandler::execute` no está implementada. ¿Recuerdas la excepción que pusimos antes? Pues eso nos dice que tenemos que movernos un nivel más adentro y ponernos en la capa de Aplicación para desarrollar el caso de uso. Por supuesto, con un test unitario.

Como hemos dicho antes, en Outside-in diseñamos en la fase de test en rojo y mockeamos los componentes que la unidad actual pueda utilizar como colaboradores. Normalmente no haremos dobles de entidades. En este caso, lo que esperamos del use case es:

* Que cree una nueva tarea, modelada con una entidad de dominio Task
* Que la persista en un repositorio
* La tarea tiene que adquirir un Id, el cual será proporcionado por el repositorio.

Esto indica que el caso de uso tendrá una dependencia, el Repositorio, y que empezaremos a modelar las tareas con una entidad Task. Este es el test.

```php
<?php

declare(strict_types=1);

namespace App\Tests\Application;

use App\Application\AddTaskHandler;
use PHPUnit\Framework\TestCase;

class AddTaskHandlerTest extends TestCase
{

    /** @test */
    public function shouldAddATaskToRepository(): void
    {
        $taskRepository = $this->createMock(TaskRepository::class);
        $task = new Task(1, 'Task Description');
        
        $taskRepository
            ->method('nextId')
            ->willReturn(1);
        
        $taskRepository
            ->expects(self::once())
            ->method('store')
            ->with($task);
        
        
        $addTaskHandler = new AddTaskHandler($taskRepository);
        
        $addTaskHandler->execute('Task Description');
    }
}
```

Lo ejecutamos y nos irá diciendo qué tenemos que hacer.

Lo primero será crear `TaskRepository` para poder mockearlo. En este caso, un repositorio se define como interfaz en la capa de dominio, como ya sabemos. Así que empezamos por ahí.

```php
<?php

declare(strict_types=1);

namespace App\Domain;


interface TaskRepository
{

}
```

Lo siguiente, será la entidad `Task`, que también está en el dominio.

```php
<?php

declare(strict_types=1);

namespace App\Domain;


class Task
{

    private int $id;
    private string $description;

    public function __construct(int $id, string $description)
    {
        $this->id = $id;
        $this->description = $description;
    }
}
```

De momento, me limito a crear lo básico, ya veremos lo que el desarrollo nos va pidiendo.

El siguiente error nos indica que no tenemos un método `nextId` en `TaskRepository`, así que lo introducimos en la interfaz.

```php
<?php

declare(strict_types=1);

namespace App\Domain;


interface TaskRepository
{
    public function nextId(): int;
}
```

Y tampoco tenemos un método `store`. Lo mismo:

```php
<?php

declare(strict_types=1);

namespace App\Domain;


interface TaskRepository
{
    public function nextId(): int;

    public function store(Task $task): void;
}
```

Por último, al invocar el método `execute`, nos lanza la consabida excepción, indicando que tenemos todo lo necesario hasta ahora, así que vamos a implementar por fin.

```php
<?php

declare(strict_types=1);

namespace App\Application;


use App\Domain\Task;
use App\Domain\TaskRepository;

class AddTaskHandler
{
    private TaskRepository $taskRepository;

    public function __construct(TaskRepository $taskRepository)
    {
        $this->taskRepository = $taskRepository;
    }

    public function execute(string $taskDescription): void
    {
        $id = $this->taskRepository->nextId();

        $task = new Task($id, $taskDescription);

        $this->taskRepository->store($task);
    }
}
```

Con este código, el test pasa. Ya no tenemos nada más que hacer aquí, salvo ver si podemos refactorizar alguna cosa. En el test vemos algunos detalles que se pueden mejorar, para hacerlo todo más fácil de entender:

```php
<?php

declare(strict_types=1);

namespace App\Tests\Application;

use App\Application\AddTaskHandler;
use App\Domain\Task;
use App\Domain\TaskRepository;
use PHPUnit\Framework\TestCase;

class AddTaskHandlerTest extends TestCase
{
    private const NEW_TASK_ID = 1;
    private const NEW_TASK_DESCRIPTION = 'Task Description';

    /** @test */
    public function shouldAddATaskToRepository(): void
    {
        $task = new Task(self::NEW_TASK_ID, self::NEW_TASK_DESCRIPTION);
        
        $taskRepository = $this->createMock(TaskRepository::class);
        $taskRepository
            ->method('nextId')
            ->willReturn(self::NEW_TASK_ID);
        $taskRepository
            ->expects(self::once())
            ->method('store')
            ->with($task);
        
        $addTaskHandler = new AddTaskHandler($taskRepository);

        $addTaskHandler->execute(self::NEW_TASK_DESCRIPTION);
    }
}
```

Volvamos al test de aceptación, a ver qué ocurre.

## Nueva visita al test de aceptación

Al ejecutar de nuevo el test de aceptación, nos indica que aunque tenemos una interfaz para `TaskRepository` no hemos definido ninguna implementación concreta, por lo que el test no se ejecuta. Es hora de desarrollar una.

Teniendo en cuenta que estamos creando un API REST necesitamos que las tareas que almacenemos persistan entre llamadas, por lo que en principio un repositorio en memoria no nos valdrá. En nuestro caso usaremos un *vendor*, que se encuentra en el repositorio que estamos usando como base para este desarrollo. Se trata de la clase `FileStorageEngine`. Simplemente guarda los objetos en un archivo, de modo que simulamos una base de datos real, cuya persistencia es suficiente para ejecutar el test.

```php
<?php

declare(strict_types=1);

namespace App\Lib;


class FileStorageEngine
{
    private string $filePath;

    public function __construct($filePath)
    {
        $this->filePath = $filePath;
    }

    public function loadObjects(string $class): array
    {
        if (!file_exists($this->filePath)) {
            return [];
        }

        $file = fopen($this->filePath, 'rb');
        $objects = unserialize(fgets($file), ['allowed_classes' => [$class]]);
        fclose($file);

        return $objects;
    }

    public function persistObjects(array $objects): void
    {
        $file = fopen($this->filePath, 'wb');
        fwrite($file, serialize($objects));
        fclose($file);
    }

}
```

Vamos entonces a escribir tests unitarios para desarrollar un repositorio de tareas que utilice `FileStorageEngine`.

```php
<?php

declare(strict_types=1);

namespace App\Tests\Infrastructure\Persistence;

use App\Domain\Task;
use App\Infrastructure\Persistence\FileTaskRepository;
use App\Lib\FileStorageEngine;
use PHPUnit\Framework\TestCase;

class FileTaskRepositoryTest extends TestCase
{
    /** @test */
    public function shouldBeAbleToStoreTasks(): void
    {
        $task = new Task(1, 'TaskDescription');
        $storageEngine = $this->createMock(FileStorageEngine::class);
        $storageEngine
            ->method('loadObjects')
            ->with(Task::class)
            ->willReturn([]);
        $storageEngine
            ->expects(self::once())
            ->method('persistObjects')
            ->with(['1' => $task]);

        $taskRepository = new FileTaskRepository($storageEngine);
        $taskRepository->store($task);
    }
}

```

Al ejecutar el test, nos dice que no tenemos un `FileTaskRepository`, así que empezamos a construirlo. Al fallar, el test nos irá indicando qué tenemos que hacer. Y este es el resultado:

```php
<?php

declare(strict_types=1);

namespace App\Infrastructure\Persistence;


use App\Domain\Task;
use App\Domain\TaskRepository;
use App\Lib\FileStorageEngine;

class FileTaskRepository implements TaskRepository
{
    private FileStorageEngine $storageEngine;

    public function __construct(FileStorageEngine $storageEngine)
    {
        $this->storageEngine = $storageEngine;
    }

    public function store(Task $task): void
    {
        $tasks = $this->storageEngine->loadObjects(Task::class);
        $tasks[$task->id()] = $task;
        $this->storageEngine->persistObjects($tasks);
    }

    public function nextId(): int
    {
        throw new \RuntimeException('Implement nextId() method.');
    }
}
```

De nuevo, nos hemos saltado algunos baby steps para llegar a la implementación deseada. Una vez que el test pasa, volveremos al test de aceptación. 

El test ahora nos indica que nos falta por implementar el método `nextId` en `FileTaskRepository`. Así que volveremos al test unitario.

En principio lo que vamos a hacer es simplemente devolver como nuevo Id el número de tareas guardadas más uno. Esto no funcionará bien en el caso de que lleguemos a borrar tareas, pero por el momento será suficiente. Este es el test:

```php
    /** @test */
    public function shouldProvideNextIdentity(): void
    {
        $storageEngine = $this->createMock(FileStorageEngine::class);
        $storageEngine
            ->method('loadObjects')
            ->with(Task::class)
            ->willReturn([]);

        $taskRepository = new FileTaskRepository($storageEngine);
        $id = $taskRepository->nextId();
        self::assertEquals(1, $id);
    }
```

Y esta la implementación:

```php
    public function nextId(): int
    {
        $tasks = $this->storageEngine->loadObjects(Task::class);

        return count($tasks) + 1;
    }
```

Sería necesario añadir un par de casos más para verificarlo, pero lo dejaremos así para avanzar más rápido ahora.

## Finalizando la primera historia de usuario

Si lanzamos ahora el test de aceptación, veremos que el error que aparece es que no tenemos ruta para el endpoint en el que marcamos una tarea como completada. Esto quiere decir que la primera de nuestras User Stories está terminada: ya se pueden añadir tareas en la lista.

Hemos ido desde el exterior de la aplicación hasta los detalles de implementación y cada paso estaba cubierto por tests. Lo cierto es que hemos podido completar mucho trabajo, pero aún nos queda camino por delante.

Y el primer paso debería sonarnos familiar. Tenemos que definir la ruta al endpoint, el controlador, un nuevo caso de uso y la interacción con el repositorio de tareas. En **routes.yaml** añadimos la ruta:

```yaml
api_add_task:
  path: /api/todo
  controller: App\Infrastructure\EntryPoint\Api\Controller\TodoListController::addTask
  methods: ['POST']

api_mark_task_completed:
  path: /api/todo/{taskid}
  controller: App\Infrastructure\EntryPoint\Api\Controller\TodoListController::markTaskCompleted
  methods: ['PATCH']
```

Añadimos un método a `TodoListController`:

```php
<?php

declare(strict_types=1);

namespace App\Infrastructure\EntryPoint\Api\Controller;


use App\Application\AddTaskHandler;
use Symfony\Component\HttpFoundation\JsonResponse;
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;

class TodoListController
{

    private AddTaskHandler $addTask;

    public function __construct(AddTaskHandler $addTask)
    {
        $this->addTask = $addTask;
    }

    public function addTask(Request $request): Response
    {
        $payload = json_decode($request->getContent(), true);

        $this->addTask->execute($payload['task']);

        return new JsonResponse('', Response::HTTP_CREATED);
    }

    public function markTaskCompleted(int $taskid, Request $request): Response
    {
        throw new \RuntimeException(sprintf('Implement %s::%s', __CLASS__, __METHOD__));
    }
}
```

Al añadir este código y ejecutar el test de aceptación el mensaje de error nos pide implementar el nuevo método. Así que nos vamos a `TodoListControllerTest` y añadimos el siguiente test:

```php
<?php

declare(strict_types=1);

namespace App\Tests\Infrastructure\EntryPoint\Api\Controller;

use App\Application\AddTaskHandler;
use App\Infrastructure\EntryPoint\Api\Controller\TodoListController;
use PHPUnit\Framework\TestCase;
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;

class TodoListControllerTest extends TestCase
{
    private AddTaskHandler $addTaskHandler;
    private TodoListController $todoListController;
    private MarkTaskCompletedHandler $markTaskCompletedHandler;


    protected function setUp(): void
    {
        $this->addTaskHandler = $this->createMock(AddTaskHandler::class);
        $this->markTaskCompletedHandler = $this->createMock(MarkTaskCompletedHandler::class);
        $this->todoListController = new TodoListController(
            $this->addTaskHandler,
            $this->markTaskCompletedHandler
        );
    }

    /** @test */
    public function shouldAddTask(): void
    {
        $this->addTaskHandler
            ->expects(self::once())
            ->method('execute')
            ->with('Task Description');

        $request = new Request(
            [],
            [],
            [],
            [],
            [],
            ['CONTENT-TYPE' => 'json/application'],
            json_encode(['task' => 'Task Description'], JSON_THROW_ON_ERROR)
        );

        $response = $this->todoListController->addTask($request);

        self::assertEquals(Response::HTTP_CREATED, $response->getStatusCode());
    }

    /** @test */
    public function shouldMarkATaskCompleted(): void
    {
        $this->markTaskCompletedHandler
            ->expects(self::once())
            ->method('execute')
            ->with(1);

        $request = new Request(
            [],
            [],
            [],
            [],
            [],
            ['CONTENT-TYPE' => 'json/application'],
            json_encode(['done' => true], JSON_THROW_ON_ERROR)
        );

        $taskId = 1;
        $response = $this->todoListController->markTaskCompleted($taskId, $request);

        self::assertEquals(Response::HTTP_OK, $response->getStatusCode());
    }


}
```

Este test fallará porque no hemos definido `MarkTaskCompletedHandler`, así que iremos ejecutando el test y respondiendo a los distintos errores hasta que falle por las razones correctas y, posteriormente, implementar lo necesario para que pase.

```php
<?php

declare(strict_types=1);

namespace App\Application;


class MarkTaskCompletedHandler
{
    public function execute(int $taskId, bool $done): void
    {
        throw new \RuntimeException(sprintf('Implement %s::%s', __CLASS__, __METHOD__));
    }
}
```

Una vez que hemos añadido el código básico del caso de uso, podemos empezar a implementar el controlador, que quedará así:

```php
<?php

declare(strict_types=1);

namespace App\Infrastructure\EntryPoint\Api\Controller;


use App\Application\AddTaskHandler;
use App\Application\MarkTaskCompletedHandler;
use Symfony\Component\HttpFoundation\JsonResponse;
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;

class TodoListController
{

    private AddTaskHandler $addTask;
    private MarkTaskCompletedHandler $markTaskCompleted;

    public function __construct(
        AddTaskHandler $addTask,
        MarkTaskCompletedHandler $markTaskCompleted
    )
    {
        $this->addTask = $addTask;
        $this->markTaskCompleted = $markTaskCompleted;
    }

    public function addTask(Request $request): Response
    {
        $payload = json_decode($request->getContent(), true);

        $this->addTask->execute($payload['task']);

        return new JsonResponse('', Response::HTTP_CREATED);
    }

    public function markTaskCompleted(int $taskid, Request $request): Response
    {
        $payload = json_decode($request->getContent(), true);

        $done = $payload['done'];

        $this->markTaskCompleted->execute($taskid, $done);

        return new JsonResponse('', Response::HTTP_OK);
    }
}
```

Y con esto hacemos pasar el test `TodoListControllerTest`. Es momento de lanzar de nuevo el test de aceptación para que nos diga qué tenemos que hacer ahora.

Y básicamente lo que nos dice es que debemos implementar `MarkTaskCompletedHandler`, que no tiene código todavía. Para eso necesitaremos un test unitario.

El caso de uso necesitará el repositorio para obtener la tarea deseada y actualizarla. Eso será lo que vamos a mockear.

```php
<?php

declare(strict_types=1);

namespace App\Tests\Application;

use App\Application\MarkTaskCompletedHandler;
use App\Domain\Task;
use App\Domain\TaskRepository;
use PHPUnit\Framework\TestCase;

class MarkTaskCompletedHandlerTest extends TestCase
{
    private const TASK_ID = 1;

    /** @test */
    public function shouldMarkTaskAsComplete(): void
    {
        $task = $this->createMock(Task::class);
        $task
            ->expects(self::once())
            ->method('markCompleted');
        
        $taskRepository = $this->createMock(TaskRepository::class);
        $taskRepository
            ->method('retrieve')
            ->with(self::TASK_ID)
            ->willReturn($task);
        $taskRepository
            ->expects(self::once())
            ->method('store')
            ->with($task);

        $markTaskAsCompleted = new MarkTaskCompletedHandler($taskRepository);

        $markTaskAsCompleted->execute(self::TASK_ID, true);
    }
}
```

Como detalle llamativo señalar que vamos a mockear una entidad. Esto es necesario para poder testear que pase algo que nos interesa: que llamamos a su método `markCompleted`. Esto nos obligará a implementarlo. Normalmente evitaría mockear entidades.

Al ejecutar el test, nos pide un método `retrieve`, que aún no tenemos en el repositorio.

```php
<?php

declare(strict_types=1);

namespace App\Domain;


interface TaskRepository
{
    public function nextId(): int;

    public function store(Task $task): void;

    public function retrieve(int $taskId): Task;
}
```

Así como `markCompleted` en `Task`:

```php
<?php

declare(strict_types=1);

namespace App\Domain;


class Task
{

    private int $id;
    private string $description;

    public function __construct(int $id, string $description)
    {
        $this->id = $id;
        $this->description = $description;
    }

    public function id(): int
    {
        return $this->id;
    }

    public function markCompleted(): void
    {
        throw new \RuntimeException(sprintf('Implement %s::%s', __CLASS__, __METHOD__));
    }
}
```

Finalmente, tenemos que implementar el método `execute` del caso de uso, que quedará así:

```php
<?php

declare(strict_types=1);

namespace App\Application;


use App\Domain\TaskRepository;

class MarkTaskCompletedHandler
{
    private TaskRepository $taskRepository;

    public function __construct(TaskRepository $taskRepository)
    {
        $this->taskRepository = $taskRepository;
    }

    public function execute(int $taskId, bool $done): void
    {
        $task = $this->taskRepository->retrieve($taskId);

        $task->markCompleted();

        $this->taskRepository->store($task);
    }
}
```

Y, de momento, estamos listas por aquí.

Ejecutaremos de nuevo el test de aceptación. A ver qué nos dice.

Lo primero que nos indica es que no tenemos método `retrieve` en el repositorio `FileTaskRepository`. Tenemos que implementarlo para poder seguir. Para ello, podemos usar `FileTaskRepositoryTestCase` que ya habíamos comenzado.

```php
    /** @test */
    public function shouldRetrieveTasksById(): void
    {
        $storageEngine = $this->createMock(FileStorageEngine::class);
        $task1 = new Task(1, 'Task 1');
        $task2 = new Task(2, 'Task 2');
        $storageEngine
            ->method('loadObjects')
            ->with(Task::class)
            ->willReturn([1 => $task1, 2 => $task2]);

        $taskRepository = new FileTaskRepository($storageEngine);
        $task = $taskRepository->retrieve(2);

        self::assertEquals($task2, $task);
    }
```

Nos pedirá implementar `retrieve`. Nos bastaría con esto:

```php
<?php

declare(strict_types=1);

namespace App\Infrastructure\Persistence;


use App\Domain\Task;
use App\Domain\TaskRepository;
use App\Lib\FileStorageEngine;

class FileTaskRepository implements TaskRepository
{
    private FileStorageEngine $storageEngine;

    public function __construct(FileStorageEngine $storageEngine)
    {
        $this->storageEngine = $storageEngine;
    }

    public function store(Task $task): void
    {
        $tasks = $this->storageEngine->loadObjects(Task::class);
        $tasks[$task->id()] = $task;
        $this->storageEngine->persistObjects($tasks);
    }

    public function nextId(): int
    {
        $tasks = $this->storageEngine->loadObjects(Task::class);

        return count($tasks) + 1;
    }

    public function retrieve(int $taskId): Task
    {
        $tasks = $this->storageEngine->loadObjects(Task::class);

        return $tasks[$taskId];
    }
}
```

Y efectivamente nos llega. Ahora que estamos en verde, podemos aprovechar para arreglar un poquito el test.

```php
<?php

declare(strict_types=1);

namespace App\Tests\Infrastructure\Persistence;

use App\Domain\Task;
use App\Infrastructure\Persistence\FileTaskRepository;
use App\Lib\FileStorageEngine;
use PHPUnit\Framework\TestCase;

class FileTaskRepositoryTest extends TestCase
{
    private FileStorageEngine $storageEngine;
    private FileTaskRepository $taskRepository;
    
    protected function setUp(): void
    {
        $this->storageEngine = $this->createMock(FileStorageEngine::class);
        $this->taskRepository = new FileTaskRepository($this->storageEngine);
    }


    /** @test */
    public function shouldBeAbleToStoreTasks(): void
    {
        $task = new Task(1, 'TaskDescription');
        $this->storageEngine
            ->method('loadObjects')
            ->with(Task::class)
            ->willReturn([]);
        $this->storageEngine
            ->expects(self::once())
            ->method('persistObjects')
            ->with([1 => $task]);

        $this->taskRepository->store($task);
    }

    /** @test */
    public function shouldProvideNextIdentity(): void
    {
        $this->storageEngine
            ->method('loadObjects')
            ->with(Task::class)
            ->willReturn([]);
        
        $id = $this->taskRepository->nextId();
        self::assertEquals(1, $id);
    }

    /** @test */
    public function shouldRetrieveTasksById(): void
    {
        $task1 = new Task(1, 'Task 1');
        $task2 = new Task(2, 'Task 2');
        $this->storageEngine
            ->method('loadObjects')
            ->with(Task::class)
            ->willReturn([1 => $task1, 2 => $task2]);

        $task = $this->taskRepository->retrieve(2);

        self::assertEquals($task2, $task);
    }
}
```

Una vez hecho esto, podemos lanzar de nuevo el test de aceptación y ver dónde hemos llegado.

Al hacerlo, nos salta la excepción que habíamos dejado en `Task::markCompleted`. De momento la vamos a implementar sin hacer nada. Esperaremos a que otros tests nos obliguen ya que no tenemos realmente forma de verificarlo sin crear un método sólo para poder revisar su estado en un test.

```php
<?php

declare(strict_types=1);

namespace App\Domain;


class Task
{

    private int $id;
    private string $description;

    public function __construct(int $id, string $description)
    {
        $this->id = $id;
        $this->description = $description;
    }

    public function id(): int
    {
        return $this->id;
    }

    public function markCompleted(): void
    {
    }
}
```

Esto hace que el test pueda llegar al siguiente punto interesante: no tenemos una ruta para recuperar la lista de tareas. Así que vamos a ello.

En **routes.yaml** añadimos la definición:

```yaml
api_add_task:
  path: /api/todo
  controller: App\Infrastructure\EntryPoint\Api\Controller\TodoListController::addTask
  methods: ['POST']

api_mark_task_completed:
  path: /api/todo/{taskid}
  controller: App\Infrastructure\EntryPoint\Api\Controller\TodoListController::markTaskCompleted
  methods: ['PATCH']

api_get_tasks_list:
  path: /api/todo
  controller: App\Infrastructure\EntryPoint\Api\Controller\TodoListController::getTasksList
  methods: ['GET']
```

Lanzamos el test de aceptación para ver que ya no pide la ruta, sino la implementación de un controlador. Y añadimos un esqueleto en `TodoListController`.

```php
<?php

declare(strict_types=1);

namespace App\Infrastructure\EntryPoint\Api\Controller;


use App\Application\AddTaskHandler;
use App\Application\MarkTaskCompletedHandler;
use Symfony\Component\HttpFoundation\JsonResponse;
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;

class TodoListController
{

    private AddTaskHandler $addTask;
    private MarkTaskCompletedHandler $markTaskCompleted;

    public function __construct(
        AddTaskHandler $addTask,
        MarkTaskCompletedHandler $markTaskCompleted
    )
    {
        $this->addTask = $addTask;
        $this->markTaskCompleted = $markTaskCompleted;
    }

    public function addTask(Request $request): Response
    {
        $payload = json_decode($request->getContent(), true);

        $this->addTask->execute($payload['task']);

        return new JsonResponse('', Response::HTTP_CREATED);
    }

    public function markTaskCompleted(int $taskid, Request $request): Response
    {
        $payload = json_decode($request->getContent(), true);

        $done = $payload['done'];

        $this->markTaskCompleted->execute($taskid, $done);

        return new JsonResponse('', Response::HTTP_OK);
    }

    public function getTasksList(): Response
    {
        throw new \RuntimeException(sprintf('Implement %s::%s', __CLASS__, __METHOD__));
    }
}
```

Así que hay volver a `TodoListControllerTestCase` para desarrollar este método:

```php
<?php

declare(strict_types=1);

namespace App\Tests\Infrastructure\EntryPoint\Api\Controller;

use App\Application\AddTaskHandler;
use App\Application\MarkTaskCompletedHandler;
use App\Domain\Task;
use App\Infrastructure\EntryPoint\Api\Controller\TodoListController;
use PHPUnit\Framework\TestCase;
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;

class TodoListControllerTest extends TestCase
{
    private AddTaskHandler $addTaskHandler;
    private TodoListController $todoListController;
    private MarkTaskCompletedHandler $markTaskCompletedHandler;
    private GetTasksListHandler $getTasksListHandler;
    
    protected function setUp(): void
    {
        $this->addTaskHandler = $this->createMock(AddTaskHandler::class);
        $this->markTaskCompletedHandler = $this->createMock(MarkTaskCompletedHandler::class);
        $this->getTasksListHandler = $this->createMock(GetTasksListHandler::class)

        $this->todoListController = new TodoListController(
            $this->addTaskHandler,
            $this->markTaskCompletedHandler,
            $this->getTasksListHandler
        );
    }

    /** @test */
    public function shouldAddTask(): void
    {
        $this->addTaskHandler
            ->expects(self::once())
            ->method('execute')
            ->with('Task Description');

        $request = new Request(
            [],
            [],
            [],
            [],
            [],
            ['CONTENT-TYPE' => 'json/application'],
            json_encode(['task' => 'Task Description'], JSON_THROW_ON_ERROR)
        );

        $response = $this->todoListController->addTask($request);

        self::assertEquals(Response::HTTP_CREATED, $response->getStatusCode());
    }

    /** @test */
    public function shouldMarkATaskCompleted(): void
    {
        $this->markTaskCompletedHandler
            ->expects(self::once())
            ->method('execute')
            ->with(1);

        $request = new Request(
            [],
            [],
            [],
            [],
            [],
            ['CONTENT-TYPE' => 'json/application'],
            json_encode(['done' => true], JSON_THROW_ON_ERROR)
        );

        $taskId = 1;
        $response = $this->todoListController->markTaskCompleted($taskId, $request);

        self::assertEquals(Response::HTTP_OK, $response->getStatusCode());
    }

    /** @test */
    public function shouldGetListOfTasks(): void
    {
        $task1 = new Task(1, 'Task 1');
        $task1->markCompleted();

        $task2 = new Task(2, 'Task 2');

        $expectedList = ['[√] Task 1', '[ ] Task 2'];

        $this->getTasksListHandler
            ->expects(self::once())
            ->method('execute')
            ->willReturn([$task1, $task2]);
        
        $request = new Request();
        
        $response = $this->todoListController->getTasksList($request);
        
        $list = json_decode($response->getContent(), true);
        
        self::assertEquals(Response::HTTP_OK, $response->getStatusCode());
        self::assertEquals($expectedList, $list);
    }
}
```

El test fallará ya que necesitamos implementar `GetTasksListHandler`.

```php
<?php

declare(strict_types=1);

namespace App\Application;


class GetTasksListHandler
{
    public function execute(): array
    {
        throw new \RuntimeException(sprintf('Implement %s::%s', __CLASS__, __METHOD__));
    }
}
```

Cuando podemos ejecutar todo el test, empezamos a implementar. Esta es nuestra tentativa:

```php
<?php

declare(strict_types=1);

namespace App\Infrastructure\EntryPoint\Api\Controller;


use App\Application\AddTaskHandler;
use App\Application\GetTasksListHandler;
use App\Application\MarkTaskCompletedHandler;
use Symfony\Component\HttpFoundation\JsonResponse;
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;

class TodoListController
{

    private AddTaskHandler $addTask;
    private MarkTaskCompletedHandler $markTaskCompleted;
    private GetTasksListHandler $getTasksList;

    public function __construct(
        AddTaskHandler $addTask,
        MarkTaskCompletedHandler $markTaskCompleted,
        GetTasksListHandler $getTasksList
    )
    {
        $this->addTask = $addTask;
        $this->markTaskCompleted = $markTaskCompleted;
        $this->getTasksList = $getTasksList;
    }

    public function addTask(Request $request): Response
    {
        $payload = json_decode($request->getContent(), true);

        $this->addTask->execute($payload['task']);

        return new JsonResponse('', Response::HTTP_CREATED);
    }

    public function markTaskCompleted(int $taskid, Request $request): Response
    {
        $payload = json_decode($request->getContent(), true);

        $done = $payload['done'];

        $this->markTaskCompleted->execute($taskid, $done);

        return new JsonResponse('', Response::HTTP_OK);
    }

    public function getTasksList(Request $request): Response
    {
        $list = $this->getTasksList->execute();

        return new JsonResponse($list, Response::HTTP_OK);
    }
}
```

El problema aquí es que tenemos que introducir una forma de convertir la lista tal como la devuelve el caso de uso `GetTaskListHandler` al formato requerido por el consumidor del endpoint. Se trata de una representación de la tarea en forma de cadena de texto.

Hay varias formas de resolver esto, y todas requieren que `Task` pueda darnos algún tipo de representación utilizable:

* La más sencilla sería hacer la conversión en el propio controlador, recorriendo la lista de tareas y generando su representación. Para ello nos hará falta un método que se encargue.
* Otra consistiría en crear un servicio que haga la conversión. Sería una dependencia del controlador.
* Y una tercera alternativa sería usar ese último servicio, pero pasándolo a `GetTaskListHandler` como estrategia. De este modo el controlador decide cómo quiere obtener la lista, aunque sea `GetTaskListHandler` quien la prepara.

Esta última opción es la que vamos a usar. Pero para eso tendremos que cambiar tests. No mucho, por suerte, tan sólo `TodoListControllerTest` necesita cambios realmente.

```php
<?php

declare(strict_types=1);

namespace App\Tests\Infrastructure\EntryPoint\Api\Controller;

use App\Application\AddTaskHandler;
use App\Application\GetTasksListHandler;
use App\Application\MarkTaskCompletedHandler;
use App\Domain\Task;
use App\Infrastructure\EntryPoint\Api\Controller\TodoListController;
use App\Tests\Infrastructure\EntryPoint\Api\Formatter\TaskListFormatter;
use PHPUnit\Framework\TestCase;
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;

class TodoListControllerTest extends TestCase
{
    private AddTaskHandler $addTaskHandler;
    private TodoListController $todoListController;
    private MarkTaskCompletedHandler $markTaskCompletedHandler;
    private GetTasksListHandler $getTasksListHandler;
    private TaskListFormatter $taskListFormatter;

    protected function setUp(): void
    {
        $this->addTaskHandler = $this->createMock(AddTaskHandler::class);
        $this->markTaskCompletedHandler = $this->createMock(MarkTaskCompletedHandler::class);
        $this->getTasksListHandler = $this->createMock(GetTasksListHandler::class);
        $this->taskListFormatter = $this->createMock(TaskListFormatter::class);

        $this->todoListController = new TodoListController(
            $this->addTaskHandler,
            $this->markTaskCompletedHandler,
            $this->getTasksListHandler,
            $this->taskListFormatter
        );
    }

    /** @test */
    public function shouldAddTask(): void
    {
        $this->addTaskHandler
            ->expects(self::once())
            ->method('execute')
            ->with('Task Description');

        $request = new Request(
            [],
            [],
            [],
            [],
            [],
            ['CONTENT-TYPE' => 'json/application'],
            json_encode(['task' => 'Task Description'], JSON_THROW_ON_ERROR)
        );

        $response = $this->todoListController->addTask($request);

        self::assertEquals(Response::HTTP_CREATED, $response->getStatusCode());
    }

    /** @test */
    public function shouldMarkATaskCompleted(): void
    {
        $this->markTaskCompletedHandler
            ->expects(self::once())
            ->method('execute')
            ->with(1);

        $request = new Request(
            [],
            [],
            [],
            [],
            [],
            ['CONTENT-TYPE' => 'json/application'],
            json_encode(['done' => true], JSON_THROW_ON_ERROR)
        );

        $taskId = 1;
        $response = $this->todoListController->markTaskCompleted($taskId, $request);

        self::assertEquals(Response::HTTP_OK, $response->getStatusCode());
    }

    /** @test */
    public function shouldGetListOfTasks(): void
    {
        $expectedList = ['[√] Task 1', '[ ] Task 2'];

        $this->getTasksListHandler
            ->expects(self::once())
            ->method('execute')
            ->with($this->taskListFormatter)
            ->willReturn($expectedList);

        $request = new Request();

        $response = $this->todoListController->getTasksList($request);

        $list = json_decode($response->getContent(), true);

        self::assertEquals(Response::HTTP_OK, $response->getStatusCode());
        self::assertEquals($expectedList, $list);
    }
}
```

Y el controlador quedará así:

```php
<?php

declare(strict_types=1);

namespace App\Infrastructure\EntryPoint\Api\Controller;


use App\Application\AddTaskHandler;
use App\Application\GetTasksListHandler;
use App\Application\MarkTaskCompletedHandler;
use App\Infrastructure\EntryPoint\Api\Formatter\TaskListFormatter;
use Symfony\Component\HttpFoundation\JsonResponse;
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;

class TodoListController
{

    private AddTaskHandler $addTask;
    private MarkTaskCompletedHandler $markTaskCompleted;
    private GetTasksListHandler $getTasksList;
    /** @var TaskListFormatter */
    private TaskListFormatter $taskListFormatter;


    public function __construct(
        AddTaskHandler $addTask,
        MarkTaskCompletedHandler $markTaskCompleted,
        GetTasksListHandler $getTasksList,
        TaskListFormatter $taskListFormatter
    )
    {
        $this->addTask = $addTask;
        $this->markTaskCompleted = $markTaskCompleted;
        $this->getTasksList = $getTasksList;
        $this->taskListFormatter = $taskListFormatter;
    }

    public function addTask(Request $request): Response
    {
        $payload = json_decode($request->getContent(), true);

        $this->addTask->execute($payload['task']);

        return new JsonResponse('', Response::HTTP_CREATED);
    }

    public function markTaskCompleted(int $taskid, Request $request): Response
    {
        $payload = json_decode($request->getContent(), true);

        $done = $payload['done'];

        $this->markTaskCompleted->execute($taskid, $done);

        return new JsonResponse('', Response::HTTP_OK);
    }

    public function getTasksList(Request $request): Response
    {
        $list = $this->getTasksList->execute($this->taskListFormatter);

        return new JsonResponse($list, Response::HTTP_OK);
    }
}
```

Y el caso de uso será este:

```php
<?php

declare(strict_types=1);

namespace App\Application;


use App\Infrastructure\EntryPoint\Api\Formatter\TaskListFormatter;

class GetTasksListHandler
{
    public function execute(TaskListFormatter $taskListFormatter): array
    {
        throw new \RuntimeException(sprintf('Implement %s::%s', __CLASS__, __METHOD__));
    }
}
```

Y, de momento, la implementación que tenemos del formateador sería así:

```php
<?php

declare(strict_types=1);

namespace App\Infrastructure\EntryPoint\Api\Formatter;


class TaskListFormatter
{
    public function format(array $tasks): array
    {
        throw new \RuntimeException(sprintf('Implement %s::%s', __CLASS__, __METHOD__));
    }
}
```

Hemos vuelto a verde, y en este caso, como veremos, significa que ya hemos acabado con `TodoListController`. Veamos qué dice el test de aceptación.

El test de aceptación nos pide implementar el caso de uso. Así que tenemos que crear un nuevo test unitario.

```php
<?php

declare(strict_types=1);

namespace App\Tests\Application;

use App\Application\GetTasksListHandler;
use App\Domain\Task;
use App\Domain\TaskRepository;
use App\Infrastructure\EntryPoint\Api\Formatter\TaskListFormatter;
use PHPUnit\Framework\TestCase;

class GetTasksListHandlerTest extends TestCase
{

    /** @test */
    public function shouldGetTheListOfTasks(): void
    {
        $tasks = [
            new Task(1, 'Task 1'),
            new Task(2, 'Task 2')
        ];

        $expectedList = ['[√] Task 1', '[ ] Task 2'];

        $tasksRepository = $this->createMock(TaskRepository::class);
        $tasksRepository->method('findAll')->willReturn($tasks);
        
        $formatter = $this->createMock(TaskListFormatter::class);
        $formatter
            ->expects(self::once())
            ->method('format')
            ->with($tasks)
            ->willReturn($expectedList);

        $getTaskListHandler = new GetTasksListHandler($tasksRepository);
        $list = $getTaskListHandler->execute($formatter);

        self::assertEquals($expectedList, $list);
    }
}
```

Ejecutar el test nos revela la necesidad de implementar un método `findAll` en el repositorio. Una vez subsanado esto, nos tocará implementar el método `execute` del caso de uso:

```php
<?php

declare(strict_types=1);

namespace App\Application;


use App\Domain\TaskRepository;
use App\Infrastructure\EntryPoint\Api\Formatter\TaskListFormatter;

class GetTasksListHandler
{
    private TaskRepository $taskRepository;

    public function __construct(TaskRepository $taskRepository)
    {
        $this->taskRepository = $taskRepository;
    }

    public function execute(TaskListFormatter $taskListFormatter): array
    {
        $tasks = $this->taskRepository->findAll();

        return $taskListFormatter->format($tasks);
    }
}
```

Esta sencilla implementación nos lleva a verde y podemos volver a lanzar el test de aceptación. Estamos muy cerca ya del final. Pero tenemos que añadir el método `findAll` al repositorio concreto. Primero el test:

```php
<?php

declare(strict_types=1);

namespace App\Tests\Infrastructure\Persistence;

use App\Domain\Task;
use App\Infrastructure\Persistence\FileTaskRepository;
use App\Lib\FileStorageEngine;
use PHPUnit\Framework\TestCase;

class FileTaskRepositoryTest extends TestCase
{
    private FileStorageEngine $storageEngine;
    private FileTaskRepository $taskRepository;

    protected function setUp(): void
    {
        $this->storageEngine = $this->createMock(FileStorageEngine::class);
        $this->taskRepository = new FileTaskRepository($this->storageEngine);
    }


    /** @test */
    public function shouldBeAbleToStoreTasks(): void
    {
        $task = new Task(1, 'TaskDescription');
        $this->storageEngine
            ->method('loadObjects')
            ->with(Task::class)
            ->willReturn([]);
        $this->storageEngine
            ->expects(self::once())
            ->method('persistObjects')
            ->with([1 => $task]);

        $this->taskRepository->store($task);
    }

    /** @test */
    public function shouldProvideNextIdentity(): void
    {
        $this->storageEngine
            ->method('loadObjects')
            ->with(Task::class)
            ->willReturn([]);

        $id = $this->taskRepository->nextId();
        self::assertEquals(1, $id);
    }

    /** @test */
    public function shouldRetrieveTasksById(): void
    {
        $task1 = new Task(1, 'Task 1');
        $task2 = new Task(2, 'Task 2');
        $this->storageEngine
            ->method('loadObjects')
            ->with(Task::class)
            ->willReturn([1 => $task1, 2 => $task2]);

        $task = $this->taskRepository->retrieve(2);

        self::assertEquals($task2, $task);
    }

    /** @test */
    public function shouldRetrieveAllTasks(): void
    {
        $expectedTasks = [
            1 => new Task(1, 'Task 1'),
            2 => new Task(2, 'Task 2'),
        ];

        $this->storageEngine
            ->method('loadObjects')
            ->with(Task::class)
            ->willReturn($expectedTasks);

        $tasks = $this->taskRepository->findAll();

        self::assertEquals($expectedTasks, $tasks);
    }
}
```

Test que se resuelve rápidamente con:

```php
<?php

declare(strict_types=1);

namespace App\Infrastructure\Persistence;


use App\Domain\Task;
use App\Domain\TaskRepository;
use App\Lib\FileStorageEngine;

class FileTaskRepository implements TaskRepository
{
    private FileStorageEngine $storageEngine;

    public function __construct(FileStorageEngine $storageEngine)
    {
        $this->storageEngine = $storageEngine;
    }

    public function store(Task $task): void
    {
        $tasks = $this->storageEngine->loadObjects(Task::class);
        $tasks[$task->id()] = $task;
        $this->storageEngine->persistObjects($tasks);
    }

    public function nextId(): int
    {
        $tasks = $this->storageEngine->loadObjects(Task::class);

        return count($tasks) + 1;
    }

    public function retrieve(int $taskId): Task
    {
        $tasks = $this->storageEngine->loadObjects(Task::class);

        return $tasks[$taskId];
    }

    public function findAll(): array
    {
        return $this->storageEngine->loadObjects(Task::class);
    }
}
```

Y volvemos a lanzar el test de aceptación para ver por dónde seguir. En esta ocasión el test nos dice que tenemos que implementar el método `TaskListFormatter::format`. Realmente estamos a dos pasos, pero tenemos que crear un test unitario.

En este punto podríamos plantear diversos diseños que eviten tratar temas de presentación en una entidad de dominio, pero para simplificar haremos que `Task` sea capaz de proporcionar su representación en forma de texto añadiendo un método `asString`.

Cabe preguntarse si aquí sería adecuado usar un doble de `Task`, algo que ya hicimos en otro test y esperar a que el test de aceptación nos pida desarrollar Tasks, o si sería preferible usar la entidad tal cual y que el tests nos fuerce a introducir los métodos necesarios.

En la práctica, llegadas a este punto creo que todo depende de la complejidad que pueda suponer. En este ejercicio, el comportamiento de `Task` es bastante trivial, por lo que podríamos avanzar con la entidad sin más complicaciones. Pero si el comportamiento es complejo, posiblemente sea mejor ir despacio, trabajar con el Mock y dedicarle el tiempo necesario después.

Así que aquí también usaremos mocks para eso.


```php
<?php

declare(strict_types=1);

namespace App\Tests\Infrastructure\EntryPoint\Api\Formatter;

use App\Domain\Task;
use App\Infrastructure\EntryPoint\Api\Formatter\TaskListFormatter;
use PHPUnit\Framework\TestCase;

class TaskListFormatterTest extends TestCase
{

    /** @test */
    public function shouldFormatAListOfTasks(): void
    {
        $expected = [
            '[√] 1. Task 1',
            '[ ] 2. Task 2'
        ];

        $task1 = $this->createMock(Task::class);
        $task1->method('asString')->willReturn('[√] 1. Task 1');

        $task2 = $this->createMock(Task::class);
        $task2->method('asString')->willReturn('[ ] 2. Task 2');

        $formatter = new TaskListFormatter();
        $formattedList = $formatter->format([$task1, $task2]);

        self::assertEquals($expected, $formattedList);
    }
}
```

Lanzamos el test para ver que falla porque no tenemos el método `asString` en `Task`. Así que lo introducimos. Fíjate que todavía no hemos implementado `markCompleted`.

```php
<?php

declare(strict_types=1);

namespace App\Domain;


class Task
{

    private int $id;
    private string $description;

    public function __construct(int $id, string $description)
    {
        $this->id = $id;
        $this->description = $description;
    }

    public function id(): int
    {
        return $this->id;
    }

    public function markCompleted(): void
    {
    }

    public function asString(): string
    {
        throw new \RuntimeException(sprintf('Implement %s::%s', __CLASS__, __METHOD__));
    }
}
```

Al relanzar el test ya protesta porque no está implementado el método `format`, así que vamos a ello:

```php
<?php

declare(strict_types=1);

namespace App\Infrastructure\EntryPoint\Api\Formatter;


class TaskListFormatter
{
    public function format(array $tasks): array
    {
        $formatted = [];

        foreach ($tasks as $task) {
            $formatted[] = $task->asString();
        }

        return $formatted;
    }
}
```

Y ya estamos en verde. Turno de volver al bucle del test de aceptación.

## Últimos pasos

El test de aceptación, como cabía esperar, falla porque `Task::asString` no está implementado. También habíamos dejado `Task:markCompleted` sin implementar y no haciendo nada. Podría ser buena idea dejar que se queje de nuevo y así asegurarnos de que se llama y no olvidarnos de gestionarlo también.

```php
<?php

declare(strict_types=1);

namespace App\Domain;


class Task
{

    private int $id;
    private string $description;

    public function __construct(int $id, string $description)
    {
        $this->id = $id;
        $this->description = $description;
    }

    public function id(): int
    {
        return $this->id;
    }

    public function markCompleted(): void
    {
        throw new \RuntimeException(sprintf('Implement %s::%s', __CLASS__, __METHOD__));
    }

    public function asString(): string
    {
        throw new \RuntimeException(sprintf('Implement %s::%s', __CLASS__, __METHOD__));
    }
}
```

Y al volver a lanzar el test de aceptación vemos que se queja de eso exactamente y ahí es donde queríamos estar ahora.

Tenemos que seguir con el desarrollo de `Task`, usando un test unitario. Como no queremos añadir métodos, de momento, para verificar el estado de `done`, lo haremos a través de `asString`.

```php
<?php

declare(strict_types=1);

namespace App\Tests\Domain;

use App\Domain\Task;
use PHPUnit\Framework\TestCase;

class TaskTest extends TestCase
{

    /** @test */
    public function shouldHaveTextualRepresentation(): void
    {
        $task = new Task(1, 'Task Description');
        
        $formatted = $task->asString();
        
        self::assertEquals('[ ] 1. Task Description', $formatted);
    }
}
```

Este test pasa. Por lo que hay que volver al test de aceptación.

Ahora el mensaje del test ha cambiado. Nos pide implementar `markCompleted` en `Task`, pero el test en sí ahora falla porque las respuestas no coinciden. Espera esto:

```
Array (
    0 => '[√] 1. Write a test that fails'
    1 => '[ ] 2. Write Production code ...t pass'
    2 => '[ ] 3. Refactor if there is o...tunity'
)
``` 

y obtiene esto:

```
Array (
    0 => '[ ] 1. Write a test that fails'
    1 => '[ ] 2. Write Production code ...t pass'
    2 => '[ ] 3. Refactor if there is o...tunity'
)
```

A estas alturas, el motivo es obvio. No hay nada implementado en `Task` que se ocupe de mantener el estado de done.

Añadamos un caso más al test:

```php
<?php

declare(strict_types=1);

namespace App\Tests\Domain;

use App\Domain\Task;
use PHPUnit\Framework\TestCase;

class TaskTest extends TestCase
{

    /** @test */
    public function shouldHaveTextualRepresentation(): void
    {
        $task = new Task(1, 'Task Description');

        $formatted = $task->asString();

        self::assertEquals('[ ] 1. Task Description', $formatted);
    }

    /** @test */
    public function shouldHaveTextualRepresentationWhenDone(): void
    {
        $task = new Task(1, 'Task Description');
        $task->markCompleted();
        
        $formatted = $task->asString();

        self::assertEquals('[√] 1. Task Description', $formatted);
    }
}
```

Ahora lo implementamos:

```php
<?php

declare(strict_types=1);

namespace App\Domain;


class Task
{

    private int $id;
    private string $description;
    private bool $done;

    public function __construct(int $id, string $description)
    {
        $this->id = $id;
        $this->description = $description;
        $this->done = false;
    }

    public function id(): int
    {
        return $this->id;
    }

    public function markCompleted(): void
    {
        $this->done = true;
    }

    public function asString(): string
    {
        $done = $this->done ? '√' : ' ';
        return sprintf('[%s] %s. %s', $done, $this->id, $this->description);
    }
}
```

Con el test en verde, volvemos a lanzar el test de aceptación y... ¡Si! El test pasa sin ningún problema más: hemos terminado el desarrollo de nuestra aplicación.

