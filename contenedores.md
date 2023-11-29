
# Instalaciones

## Procedimiento tradicional 

    App1 + App2 + App3
-------------------------
            SO
-------------------------
     Máquina - Hierro

## Máquinas virtuales

    App1   | App2 + App3
-------------------------
    SO 1   |   SO 2
-------------------------
    MV 1   |   MV 2
-------------------------
   Hipervisor: Citrix,
    vmware, hyperV
-------------------------
            SO
-------------------------
     Máquina - Hierro

## Contenedores

    App1   | App2 + App3
-------------------------
    C 1    |   C 2
-------------------------
  Gestor de contenedores
  Docker, Podman, CRIO
-------------------------
    SO - Kernel Linux
-------------------------
     Máquina - Hierro


# Indices fulltext

Tabla Recetas de cocina
| id | titulo                           |...|
|----|----------------------------------|---|
| 1  | Tortilla de patatas              |...| 
| 2  | Tortilla de patatas y cebolla    |...|
| 3  | Corderito asado                  |...|
| 4  | Salmón encebollado               |...|
| 5  | tortillita de camarones          |...|
| 6  | Receta de la abula: tortilla de pimientos    |...|
| 7  | Tortilla de patatas              |...|
| 8  | bacalao al pil pil               |...|

BÚSQUEDA SIN INDICE: WHERE titulo LIKE "%Tortilla%"    -> 1, 2, 7       FULLSCAN

# Indice normal
                                                Ubicación
bacalao al pil pil                              8
Corderito asado                                 3

Receta de la abula: tortilla de pimientos       6

Salmón encebollado                              4

Tortilla de patatas                             1, 7

Tortilla de patatas y cebolla                   2

tortillita de camarones                         5


BUSQUEDA: WHERE titulo = "Salmón encebollado"    -> 4               BUSQUEDA BINARIA INDICE
BUSQUEDA: WHERE titulo LIKE "Tortilla%"          -> 1,2,7           BUSQUEDA BINARIA INDICE
BUSQUEDA: WHERE titulo LIKE "%Tortilla%"         -> 1,2,7           FULLSCAN INDICE

# Indices fulltext

bacalao|-|pil|pil                              8
corderito|asado                                3
receta|-|-|abuela|tortilla|-|pimientos         6
salmon|encebollado                             4
tortilla|-|patatas                             1, 7
tortilla|-|patatas|-|cebolla                   2
tortillita|-|camarones                         5


abuel                      6(4)
asad                       3(2)

bacalao                    8(1)

camar                      5(3)
ceboll                     2(5) ***

corder                     3(1)
enceboll                   4(2)

pimient                    6(6)

receta                     6(1)
salmon                     4(1)
tortill                    1(1) 2(1) 7(1) 6(5) 5(1) ****
patat                      1(3) 7(3) 2(3)
pil                        8(3,4)


"Tortilla" -> "tortill"
"TORTILLA CON CEBOLLA" -> "tortill|ceboll"