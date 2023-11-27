
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
