
# React

WebComponents: Es un estandar del W3C para crear componentes reutilizables en la web.
Antiguamente este estandar no existía... y surgen frameworks como Angular, React, Vue, etc... para crear componentes reutilizables en la web.

En un momento dado surgen los WebComponents... y los frameworks se adaptan a este estandar.

Los componentes Web básicamente son marcas HTML customizadas que nosotros creamos.
A esa marca le asocio por un lado una lógica de representación
Y por otro lado una lógica de comportamiento.

Nuestra aplicación web estará formada por componentes web.

Cada bloque de los que ponéis en OutSystems... es un componente web.

Los componentes web van cambiando dinámicamente en función de los datos que le pasemos.
Pero además, podemos sustituir un componente web por otro dinámicamente. ---> SPA

SPA : Single Page Application

Tengo una página: DOM

DOM
    html
        head
        body
            <h1>Titulo</h1>
            <cabecera>
                <usuario id="18272"/>
            </cabecera>
            <menu>
                <item> Formulario </item>
                <item> Listado </item>
            </menu>
            <contenido>
                <formulario>
                    <filtro campo="nombre" tipo="texto" valor=""/> <!--Valor por defecto-->
                    <filtro campo="descripcion" tipo="texto" valor=""/>
                    <button>Buscar</button>
                    <button>Borrar</button>
                </formulario>
                <listado filtros="Filtros"> inicializacion / Cuando se reciben nuevas propiedades
                </listado>
            </contenido>

Lo más complejo con los componentes web es comunicarlos entre si.
Estrategias de comunicación entre componentes web:
    OPCION 1: Más básica y ESTANDAR
    - De padre a hijo: A través de propiedades (atributos HTML)
    - De hijo a padre: A través de eventos
    OPCION 2: En outsystems se me dificultan
        Montar un servicio a nivel de cliente (en el navegador) que se encargue de la comunicación entre componentes web. 

Un problema grande en las comunicaciones entre componentes web, es cuando los componentes no tienen entre si una relación de padre-hijo / jerárquica.

El código HTML hoy en día se genera en cliente (en el navegador).
Antiguamente el código HTML se generaba en el servidor (.asp, .php, .jsp, .aspx, etc...)

Los componentes web tiene un ciclo de vida asociado:
    - Creación
    - Renderiza
    - Recibir propiedades
    - Destrucción

La idea de los componentes web es que sean componentes reutilizables en distintos sitios. Y DEBEN TENER UN API de comunicación muy definido:
    - Propiedades
    - Eventos

PAGINA

    Componente onNuevosFiltros="inyecta ese valor al hijo: LISTADO"
        Formulario onMeHanTocadoElValor="mando un nuevo evento: NuevosFiltros"
            ^
            Filtros (eventos: onClick, onFocus, onBlur, onKeyDown, onKeyUp, onChange, onSubmit, etc...)
                        MeHanTocadoElValor
            ^
        Listado / Tabla propiedad (atributo HTML)
        Navegador de Resultados
    


Como dar persistencia en frontal a los filtros de búsqueda

Estado: Alta
Se va
Y luego vuelve.. y que siga ahí alta


---

# Pantalla de búsqueda

  PANTALLA
        < eventos
            FORMULARIO
                                            FILTRO1 
                        <eventos            FILTRO2 
                                            FILTRO3
                                            BOTONES
        propiedades > initialize / onPropChanges
            LISTADO

## Comunicaciones basadas en propiedades (padre-> hijo) y eventos (hijo-> padre)

  PANTALLA                                  ServicioGestiónDelFormulario (clase.js)
    FORMULARIO*                                   * subscribirmeAlFormulario()
        FILTRO1 +*                                + nuevoValorDeFiltro(campo, valor)
        FILTRO2 +*                                - restablecerFiltros()
        FILTRO3 +*                                var Filtros={}
        BOTONES *-
    LISTADO*

REDUX es una librería que nos ofrece una generalización/estandarización de esta forma de comunicarse

La cuestión con esta forma de comunicación es que necesito en el navegador unas variables en memoria que tengan un scope mayor que el de las funciones (subscribirmeAlFormulario, nuevoValorDeFiltro, restablecerFiltros, etc...)
Además, necesitamos de una librería que nos permite suscribirnos a datos: RxJS / Redux. 
    Implementación de un patrón Observable.

Outsystems no me da ninguna de estas 2 cosas.

Esto lo podemos montar en OutSystems... pero con calzador/trucos rastreros.
- Los eventos /propiedades tienen que estar haciendo el transporte de los datos. (1)

La única opción que tengo en OutSystems para esos datos globales: Client 
    Pero están muy limitados: Solo tipos simples y tienen persistencia en el cliente (navegador)
                                            ^                  ^
                                            JSON            En ocasiones esto es un problema (Seguridad)

Otro tema es que no podemos suscribirnos a cambios en esas variables, necesito seguir trabajando con eventos, para lanzar notificaciones de cambios.... me evito el tener que estar mandando los datos. (1)


## Problemilla: 

Cada letra que escriba lanza una petición al servidor... y eso es un problema de rendimiento.
Lo resolvemos con JS:

if(ClientVariable)
    clearTimeout( ClientVariable )

id = setTimeout( ClientActionRefresco, 300 )
ClientVariable =  id


----
ClientAction:
ASSIGN :        ClienteVariable = undefined
REFRESH DATA :  ActualizarBusqueda

----

Queries paginadas:

Se hace en 2 fases:
Primera query: LA que quiero hacer
Segunda query, sobre la anterior
                y la cuenta de campos de la anterior

```sql
-- OPCION 1
declare @GridFilterClient varchar(50) = '0000000008';
declare @SkipRows int = 0;
declare @PageSize int = 25;

With query as (
    --- SOLO TENGO QUE CAMBIAR ESTO ENTRE PANTALLAS
	SELECT distinct
		ClientId AS 'ClientId',  
		ClientName AS 'ClientName'  
	FROM  [OSClientesNoRentables].[dbo].[V_Grid_Clients] 
	Where (@GridFilterClient ='' OR [ClientId] like @GridFilterClient+'%') 
    --- HASTA AQUI !
)

Select 
    query.*,Total
From 
    query 
    cross join ( select count(*) as Total from query ) as Cuenta
ORDER BY 1  
OFFSET (@SkipRows) ROWS  
FETCH NEXT @PageSize ROWS ONLY
```
----

Tipos de joins:
    Joins normales: Inner / outer (left, right, full)
    Cross Join:     Producto cartesiano

    | Horas de consulta |
    | 9:00              |
    | 10:00             |
    | 11:00             |

    | Medicos           |
    | Dr. House         |
    | Dr. Strange       |
    | Dr. Who           |

    Producto cartesiano:
    | Horas de consulta | Medicos           |
    | 9:00              | Dr. House         |
    | 9:00              | Dr. Strange       |
    | 9:00              | Dr. Who           |
    | 10:00             | Dr. House         |
    | 10:00             | Dr. Strange       |
    | 10:00             | Dr. Who           |
    | 11:00             | Dr. House         |
    | 11:00             | Dr. Strange       |
    | 11:00             | Dr. Who           |



