
# Pruebas

## Vocabulario en el mundo de las pruebas

- Errores       Los humanos cometemos errores (por estar cansados, 
                faltos de conocimiento, desconcentrados...)
- Defectos      Al cometer un error, podemos introducir un DEFECTO en el producto
- Fallos        Ese defecto en momento dado puede manifestarse como un FALLO al
                usar el producto

## Para qué sirven las pruebas?

- Para asegurar el cumplimiento de unos requisitos
- Para intentar localizar el máximo número posible de fallos antes del paso a producción
  Una vez identificado un fallo, hemos de arreglar el DEFECTO que lo provoca... para lo cual, lo primero es identificar el DEFECTO. 
  El procedimiento de identificar un defecto desde el fallo que lo provoca se denomina: DEPURACION o DEBUGGING
- Las pruebas deben aportar información suficiente para facilitar la identificación de los defectos que provocan los fallos del producto.
- Para intentar identificar la mayor cantidad posible de defectos en el producto, antes del paso a producción: REVISION del producto
- Haciendo un análisis de causas raíces, nos ayudan a identificar los ERRORES que dieron lugar a los defectos... para tomar acciones preventivas que eviten nuevos errores, defectos y fallos en el futuro.
- Para guiarme en el desarrollo del producto:
  - Test first:     Primero diseño las pruebas y luego escribo el código... 
                    Finalmente ejecuto las pruebas
  - TDD   Test Driven Development (Desarrollo guiado por pruebas). Pruebas unitarias.
            Test-first + Refactorización
  - BDD   Es un paso más a TDD: Pruebas de sistema
  - ATDD  Pruebas de aceptación  
- Devops y/o el uso de una met. ágil
  - Saber cómo va el proyecto
    El software funcionando es la MEDIDA principal de progreso.
    La MEDIDA principal de progreso es el software funcionando.
    La manera en la que voy a medir (INDICADOR) cómo voy con mi proyecto es "SOFTWARE FUNCIONANDO"
    SOFTWARE FUNCIONANDO: Software que funciona! 
    Quién dice que el software funciona? Desarrollo? Las pruebas!

## Clasificación de las pruebas

Cualquier prueba que defina se centra en una UNICA característica de un componente/sistema.

### Según en objeto de la prueba

  - Pruebas funcionales
  - Pruebas no funcionales
    - Pruebas de rendimiento
    - Pruebas de seguridad
    - Pruebas de usabilidad
    - Pruebas de carga
    - Pruebas de estrés

### Según el nivel de la prueba (scope)

- Pruebas unitarias         Se centra en una característica de un componente 
                            AISLADO del sistema

                            TREN:
                             Motor      Frenos      Ruedas      Asientos

           Componente WEB -> Servicio > Controlador > Servicio |> Repositorio > BBDD
                                        REST              ^          ^ CORE SERVICE
                                        SOAP              ^
                                                      altaDeExpediente(datos)
        Para aislar componentes que de forma natural están relacionados(dependen) de otros componentes usamos los test-doubles (dummies, spy, fake, stub, mocks). ESTO NO LO PODEMOS HACER EN OUTSYSTEMS

- Pruebas de integración    Se centran en la COMUNICACION de 2 componentes
                             Frenos ----> Ruedas
- Pruebas de sistema        Se centran en el COMPORTAMIENTO del sistema en su conjunto
                              TREN 
    Y si tengo todas las pruebas de sistema funcionando OK, necesito hacer pruebas unitarias y de integración? NO. Si el sistema ya funciona como debe... qué más necesito probar.. Esto implica que los componentes funcionan y se comunican bien... Si no el sistema en su conjunto no funcionaría.

    El truco está en:
    - 1º Cuando puedo ejecutar las pruebas de sistema? Cuando el sistema está terminado.
    - 2º Y si no funcionan las pruebas de sistema? donde está el defecto? NPI... a buscar

- Pruebas de aceptación     Suelen ser un subconjunto de las anteriores

### Según la forma de ejecución de la prueba:

- Pruebas dinámicas: Requieren la ejecución del producto    --> FALLOS
- Pruebas estáticas: No requieren la ejecución del producto --> DEFECTOS (Revisiones)
  - SonarQube

## DEV-->OPS

Devops es una cultura, una fisolofía, que abogan por automatizar todo lo que se pueda automatizar desde el desarrollo de software ---> Operación del software en producción.
Devops no es un perfil profesional... o al menos no lo era.


---

Dentro de OutSystems tenemos el BDDFramework que es lo que Outsystems ofrece para hacer pruebas dentro de sus apps.

Ese Framework se basa en BDD (Behavior Driven Development) que es una técnica de desarrollo de software ágil que promueve la colaboración entre desarrolladores, QA y participantes no técnicos o empresariales en un proyecto de software.

Las pruebas BDD -> Behavioral : SISTEMA

La herramienta que usa TODO el mundo hoy en día para pruebas de comportamiento es CUCUMBER (pepino)
Esta herramienta procesa ficheros GHERKIN (pepinillo)

GHERKIN realmente es un conjunto de restricciones a los lenguajes humanos... no es un lenguaje en si.

Definimos los requisitos de la aplicación en ESPAÑOL, INGLES
Asociado a cada requisito damos unos ejemplos de uso del sistema... relativos a ese requisito


---

Feature: Validador de DNIs

Scenario: Probar si soy capaz de dar por buenos DNIs correctos
    Given un DNI: 230000T                   # < PREPARACIÓN (lo que necesito para hacer la prueba)
    When lo valido                          # < Acción a probar
    Then el sistema me devuelve true        # < PRUEBA (comprobación)

Scenario: Probar si soy capaz de dar por malos DNIs malos
    Given un DNI: 230dheyrfmdc
    When lo valido
    Then el sistema me devuelve false

---
Alta de un expediente
    Dado        un expediente 
    Y           el expediente tiene por título: X
    Y           el expediente tiene por cantidad: Y
    Y           el expediente tiene por dni: Z
    Y           el expediente tiene por estado: A
    Y           el expediente tiene por tipo: B
    Cuando      solicito el Alta del expediente
    Entonces    Debo recibir 1 expediente
        Y       El expediente debe tener por ID numérico mayor que cero
        Y       El expediente debe tener por titulo el que tenga el expediente titulo
        Y       El expediente debe tener por cantidad el que tenga el expediente cantidad
        Y       El expediente debe tener por dni el que tenga el expediente dni
        Y       El expediente debe tener por estado el que tenga el expediente estado
        Y       El expediente debe tener por tipo el que tenga el expediente tipo


Recuperar un expediente
    Dado        un expediente 
    Y           el expediente tiene por título: X
    Y           el expediente tiene por cantidad: Y
    Y           el expediente tiene por dni: Z
    Y           el expediente tiene por estado: A
    Y           el expediente tiene por tipo: B
    Y           he solicitado el Alta del expediente
    Y           he obtenido un ID
    Cuando      llamamos a la función recuperarExpediente con el ID anterior
    Entonces    Debo recuperar 1 expediente
        Y       El expediente debe tener por ID el mismo que el suministrado
        Y       El expediente debe tener por titulo el que tenga el expediente titulo
        Y       El expediente debe tener por cantidad el que tenga el expediente cantidad
        Y       El expediente debe tener por dni el que tenga el expediente dni
        Y       El expediente debe tener por estado el que tenga el expediente estado
        Y       El expediente debe tener por tipo el que tenga el expediente tipo

ServerAction: ExpedienteMO RecuperarUnExpediente(id)

---

Principios SOLID de desarrollo de software
Principios FIRST de pruebas de software

F - Fast
I - Independent -> DADO
R - Repeatable ---^  El test debe poder ejecutarse 50 veces y dar siempre el mismo resultado
S - Self-validating -> THEN El entonces debe validar todo lo que ha de ocurrir cuando se ejecuta la acción a probar
T - Timely - Oportuno. Tenerlo a tiempo. La prueba aporta valor cuando aporta valor.