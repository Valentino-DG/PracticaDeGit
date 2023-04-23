# # Trabajo Práctico 3-SO2

## Alumno: Marco Valentino Di Giannantonio.
## LinkRepo: 

## Optimizacion: 
Lo primero que se va a hacer es tomar el programa tal cual y se lo va a ejecutar un total de 5 veces para sacar un promedio del tiempo de ejecucion, de forma tal de tener un punto de partida y ver como el promedio se va reducioendo a medida que voy agregando optimizaciones:<br>

### Programa original:

|            | Real    | User    | Sys     |
|------------|---------|---------|---------|
| Ejecución 1| 0,421   | 0,13    | 0,214   |
| Ejecución 2| 0,489   | 0,094   | 0,225   |
| Ejecución 3| 0,423   | 0,153   | 0,166   |
| Ejecución 4| 0,5     | 0,127   | 0,224   |
| Ejecución 5| 0,508   | 0,152   | 0,205   |
| Promedio   | 0,4682  | 0,1312  | 0,2068  |

Vemos como el promedio de tiempo de ejecucion es 0,4682. Este es nuestro punto de partida.<br>

### Optimizacion 1:

![imagen](https://user-images.githubusercontent.com/88598932/233863377-6d519144-c725-4cae-a9bf-9ae1a5ad9a55.png)

Tabla de tiempos:

|            | Real    | User    | Sys     |
|------------|---------|---------|---------|
| Ejecución 1| 0,442   | 0,164   | 0,181   |
| Ejecución 2| 0,434   | 0,138   | 0,205   |
| Ejecución 3| 0,441   | 0,175   | 0,173   |
| Ejecución 4| 0,402   | 0,132   | 0,188   |
| Ejecución 5| 0,418   | 0,137   | 0,172   |
| Promedio   | 0,4274  | 0,1492  | 0,1838  |

Por lo que vemos, hemos obtenido una mejora en cuanto al original pero es muy pequeña, hemos pasado de 0,4682 [seg] a 0,4274 [seg].

### Optimizacion 2:
