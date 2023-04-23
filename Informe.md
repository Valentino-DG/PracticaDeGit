# # Trabajo Práctico 3-SO2

## Alumno: Marco Valentino Di Giannantonio.
## LinkRepo: 

## Optimizacion: 
Lo primero que se va a hacer es tomar el programa tal cual y se lo va a ejecutar un total de 5 veces para sacar un promedio del tiempo de ejecucion, de forma tal de tener un punto de partida y ver como el promedio se va reducioendo a medida que voy agregando optimizaciones:<br>

### Programa original:

|            | Real    | User    | Sys     |
|------------|---------|---------|---------|
| Ejecución 1| 0,431   | 0,151   | 0,191   |
| Ejecución 2| 0,444   | 0,134   | 0,212   |
| Ejecución 3| 0,457   | 0,144   | 0,204   |
| Ejecución 4| 0,496   | 0,122   | 0,196   |
| Ejecución 5| 0,494   | 0,126   | 0,233   |
| Promedio   | 0,4644  | 0,1354  | 0,2072  |

Vemos como el promedio de tiempo de ejecucion es 0,4644. Este es nuestro punto de partida.<br>

### Optimizacion 1:
La primer optimizacion se llevó a cabo a la funcion "alloc_matrix". Lo que se hizo basicamente fue que en vez de alocar memoria a a cada columna de la matriz y luego recorrerla para setearla en 'j', se utilizo la funcion calloc() para alocar cada columna e ir seteandola en cero. Si bien no estamos seteando a cada elemento de la matriz en 'j', no hay problema en setearlos en cero ya que luego se va a llenar la matriz con otros valores mediante la funcion fill().<br> 
![imagen](https://user-images.githubusercontent.com/88598932/233866688-28706342-4e09-4645-95cd-0c7a8c5ff475.png)

Tabla de tiempos:

|            | Real    | User    | Sys     |
|------------|---------|---------|---------|
| Ejecución 1| 0,45    | 0,148   | 0,2     |
| Ejecución 2| 0,423   | 0,131   | 0,19    |
| Ejecución 3| 0,391   | 0,127   | 0,167   |
| Ejecución 4| 0,435   | 0,158   | 0,193   |
| Ejecución 5| 0,416   | 0,155   | 0,148   |
| Promedio   | 0,423   | 0,1438  | 0,1796  |

Por lo que vemos, hemos obtenido una mejora en cuanto al original pero es muy pequeña, hemos pasado de 0,4644 [seg] a 0,423 [seg].

### Optimizacion 2:
