
# App Ejemplo

Datos: Expedientes
  - id
  - nombre
  - fecha/hora (zoned)
  - cantidad
  - descripcion
  - estado -> Enumerado (id, nombre)
  - tipo   -> Enumerado (id, nombre)
  - dni cliente

CRUD
Filtros de búsqueda

---

Modulos:
- CoreServices
  - Entidades
  - Repositorios/Persistencia (CRUD)
  - Validación: dni cliente
- BusinessLogic
  - Validación: Fecha no sea posterior a la actual
  - Persistencia (CS)
  - Notificaciones
- UI
  - Formulario / Listados

UI -> BL -> CS -> BBDD

    Alta expediente:
        - Validación
        - Persistencia
        - Notificación

---

id	nombre	fecha	cantidad	descripcion	estado	tipo	dni_cliente
1	Expediente 1	2017-04-26T15:13:12.016+01:00	1	Descripción 1	1	1	23000000T
2	Expediente 2	2017-04-26T15:13:12.016+02:00	2	Descripción 2	2	1	23000023T
3	Expediente 3	2017-04-26T15:13:12.016+00:00	3	Descripción 3	3	2	23000230T
4	Expediente 4	2017-04-26T15:13:12.016+02:00	3	Descripción 4	4	2	23002300T
5	Expediente 5	2017-04-26T15:13:12.016+05:00	2	Descripción 5	5	3	23023000T

----
# Modulo CS

API (PUBLICO) de comunicación con este módulo:
- GestionDeExpedientes:           PUBLIC
  - ExpedienteMO AltaExpediente(DatosExpedienteMO)
  - ExpedienteMO ModificarExpediente(DatosExpedienteMO)
  - boolean EliminarExpediente(id)
  - Respuesta<ExpedienteMO> ObtenerExpediente(Long id)
  - List<ExpedienteMO> BuscarExpedientes(ExpedientesFiltroMO filtro)
    - Si no hay ninguno que cumple el filtro... te doy una lista vacía... Eso no significa que la función no haya hecho su cometido.
  - List<TipoExpedienteMO> ObtenerTiposExpedientes()
  - List<EstadoEspedienteMO> ObtenerEstadosExpedientes()

Entidades:
- TipoExpediente                  PRIVATE
- EstadoEspediente                PRIVATE
- Expediente                      PRIVATE
  - New                           PRIVATE
  - Update
  - Delete
  - Get

Estructuras de datos
- CargaInicialExcel               PRIVATE
  - TipoExpediente
  - EstadoEspediente
  - Expediente
- DTOs                            PUBLIC
  - TipoExpedienteMO
  - EstadoEspedienteMO
  - ExpedienteMO

---

SOLID- 5 principio que nos ayudan a crear una app con componentes débilmente acoplados, lo que favorece el mnto futuro y evolutivo de la app.
S - Single Responsability Principle
O - Open/Closed Principle
L - Liskov Substitution Principle
I - Interface Segregation Principle <<<< Muchas interfaces especificas son mejores que UNA UNICA interfaz general
D - Dependency Inversion Principle

---

SELECT * FROM expedientes WHERE
  nombre LIKE 'expediente%' 
  AND UPPER(descripcion) LIKE UPPER('%expediente%')
  AND (fecha BETWEEN '2017-04-26T15:13:12.016+01:00' AND '2017-04-26T15:13:12.016+01:00')
  AND (cantidad >= 1) AND (cantidad <= 3)
  AND (estado IN (1,2,3))
  AND (tipo IN (1,2))
  AND (dni_cliente = '23000000T')

Solo tenemos 1 tipo de like válido: LIKE ..... AND nombre LIKE 'nombre%'
Éste tipo de LIKE funciona GENIAL en las BBDD

nombre LIKE '%expediente%' ESTE ES UN DESASTRE ABERRANTE

En esta query, me aporta algo en INDICE. NO SE USARÍA EL ÍNDICE
El crear un índice, que impacto tendría: NEGATIVO:
- Operaciones: INSERT, UPDATE, DELETE serían más lentas.
- La BBDD va a ocupar un huevo más de espacio.

Si quisieramos hacer una búsqueda del tipo LIKE %%  en SQLServer tendríamos una mejor opción que es crear un índice FULLTEXT

---

Los índices, creo los menos posibles el día 1.
Monitorizo la BBDD ... y veo que queries tira la gente... y dónde hacen falta índices... y entonces me planteo crearlos.
Ni siquiera creamos índices para todos los campos que en un momento dado podrían beneficiarse de un índice.
Si una búsqueda se hace pocas veces... que tarde... no pasa nada.
  Si te creo un índice: 
    - La BBDD va a ocupar más espacio
    - Las operaciones de INSERT, UPDATE, DELETE van a ser más lentas.

En el entorno de prod, cada dato se almacena al menos 3 veces: 10 Ggs -> 10 Gbs de almacenamiento x 3 unidades diferentes
Unidades de almacenamiento que no son el Western blue que compro para mis peliculas en casa... Me voy al Western Digital Black/ RED PRO que es más rápido y más caro.

Pero hay otra: Backups. Hacen que de cada dato se hagan otras n copias... replicadas
  - Backups diarios
  - Backups semanales
  - Backups mensuales
  - Backups anuales

INDICE en una BBDD Es una copia de los datos ordenada.
El campo descripción VARCHAR (4000) le guardo 2 veces... con lo que ocupa
En el fichero del INDICE:

El índice nos aporta en tanto y cuanto nos permite aplicar algoritmos de búsqueda binaria... en lugar de FULLSCANs

BUSQUEDA BINARIA: Buscar en un diccionario... en lugar de ir palabra a palabra... voy a la mitad... y veo si está antes o después... y así voy reduciendo el espacio de búsqueda a la mitad... y así hasta que encuentro la palabra que busco.

Las BBDD van creando unas ESTADISTICAS para conocer la distribución de los datos en un campo y la primera particion optimnizarla.

Al añadir un dato nuevo, en el fichero de la tabla, se añade al final del fichero el dato. ME jode las búsquedas ... aquí no podemos aplicar una búsqueda binaria.
Para eso creo el índice... que no es sino otro fichero, con los datos duplicados, pero escritos en orden.
Para que se puedan añadir datos rápido en el índice en su lugar correcto, las bases de datos generan un fichero lleno de espacios en blanco prereservados... para que cuando venga un dato, haya un sitio donde escribirlo. (PADDING de indexación) Quñé porcentaje de el fichero del índice se crea con espacios en blanco: 30-80% del fichero del índice.


---

public class ExpedienteRepository {

  public List<RevisorExpedienteMO> recuperarRevisoresDelExpediente(Long idExpediente) {
    // LO QUE HAYA... que ejecutará en última instancia un query
  }

  public Record<EstadosMO> recuperarEstadoDelExpediente(Long idExpediente) {
    // LO QUE HAYA... que ejecutará en última instancia un query
  }

  public List<EstadosMO> recuperarTodosLosEstados() {
    // LO QUE HAYA... que ejecutará en última instancia un query
  }

}

Tengo está función en C#.
Qué devuelve / Cómo se comporta? 
Una lista de objetos de tipo RevisorExpedienteMO. Siempre? 
Si hay... y si no hay una lista vacía...
Y si el id de expediente suministrado no existe? 
- null
- Devuelve una lista vacía
- Exception. Nunca debería usar una Exception para controlar lógica de mi app.
             A nivel computacional, una Exception es muy cara de generar (lo primero que hace falta es un volcado del stack de llamadas)

Lo primero es que la signatura (firma) de la función, no deja claro el comportamiento
Para realmente saber el comportamiento de la función... solo me queda:
- Leer el código... si lo tengo... y en serio ... en el 2023 tengo que leer un código de una función ara saber como comunicarme con ella.
- Ir a la documentación... 2023

= RIDICULO !!!!!

---


Validar un DNI
- Aplicar expresiones regulares
- Aplicar algoritmo de validación del dígito de control del DNI
  - Parte numérica // 23 y me quedo con el resto. A cada resto le corresponde una letra
    LetrasDeControl = "TRWAGMYFPDXBNJZSQVHLCKE"

    PATRON REGEX = "^[0-9]{1,8}[ -]?[A-Z]$"  --> regex101.com
