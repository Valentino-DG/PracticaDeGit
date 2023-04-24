## Trabajo Práctico 3-SO2

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

Se ve como el promedio de tiempo de ejecucion es 0,4644. Este es mi punto de partida.<br>

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

Por lo que vemos, he obtenido una mejora en cuanto al original pero es muy pequeña, el tiempo de ejecucion ha pasado de 0,4644 [seg] a 0,423 [seg].

### Optimizacion 2:
La segunda optimizacion consistio en reemplazar los printf() por fprint() en las funciones "compute" y "print". Lo que realicé fue en vez de sacar la salida del programa por la consola, escribirla en un archivo llamado "output.txt". Realicé este cambio ya que printf() escribe en consola, siendo esto una operacion relativamente costosa en términos de tiempo pero por otro lado, fprintf() escribe directamente en un archivo y no necesita esperar a que cada carácter se muestre en la pantalla, lo que puede ralentizar el proceso de escritura en el caso de la consola cuando estamos imprimiendo muchos datos como es el caso.

Tabla de tiempos:

|            | Real    | User    | Sys     |
|------------|---------|---------|---------|
| Ejecución 1| 0,049   | 0,045   | 0,004   |
| Ejecución 2| 0,051   | 0,047   | 0,004   |
| Ejecución 3| 0,061   | 0,041   | 0,020   |
| Ejecución 4| 0,058   | 0,046   | 0,012   |
| Ejecución 5| 0,064   | 0,060   | 0,004   |
| Promedio   | 0,0566  | 0,0478  | 0,0088  |

En este caso el cambio es demasiado grande, he obtenido una gran mejora en cuanto al programa original con la optimizacion 1 agregad. El tiempo de ejecucion ha pasado de 0,423 [seg] a 0,0566 [seg].


## Uso de Valgrind:
El ajecutar el programa con el uso del valgrind, me sale el siguiente error una gran cantidad repetida de veces:

![imagen](https://user-images.githubusercontent.com/88598932/233872626-b38d78ff-aaff-4ff3-ba00-70cf68f87ccc.png)

Esto me está indicando que en la funcion "compute" hay una variable sin inicializar. Rastreando un poco el codigo de la funcion compute la variable sin inicializar es la variable "accum" que se utiliza para asignarla a las posiciones de la matriz. El problema es que la variable accum se inicializa si se ingresa a la condicion if(), y luego se usa para asignarla a un elemento de la matriz, pero en caso de que no se entre a este if(), la variable no se va a inicializar y al elemento de la matriz se le va a asignar basura, y esto va a generar un problema cuando se haga un print() de la matriz. Para solucionar esto se inicializa la variable "accum" a cero antes de entrar a los bucles de la funcion compute.

Otro registro de errores que me sale son los siguientes:
![imagen](https://user-images.githubusercontent.com/88598932/233872771-9da63519-a161-4b17-888a-4452fbe04fde.png)

Esto me está indicando que he alocado memoria y no la estoy desalocando. Esto es ya que para generar la matriz aloco memoria pero no le hago un free. Para solucionar esto he agregado una funcion que se encargue de hacer el free a la matriz (dicha funcion es llamada en el main al final del codigo):

![imagen](https://user-images.githubusercontent.com/88598932/233873901-92fd9c71-4e76-4aa9-abc4-61ed2caea239.png)

Una vez hechas estas correcciones paso a ejecutar el programa con valgrind de nuevo:
![imagen](https://user-images.githubusercontent.com/88598932/233874185-175693cb-e2cf-491a-89b9-809b7bb9443e.png)

Se puede observar que los errores han sido corregidos.



