
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