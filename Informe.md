## Trabajo Práctico 3-SO2

## Alumno: Marco Valentino Di Giannantonio.
## LinkRepo: 

## Optimizacion: 
Lo primero que se va a hacer es tomar el programa tal cual y se lo va a ejecutar un total de 5 veces para sacar un promedio del tiempo de ejecucion, de forma tal de tener un punto de partida y ver como el promedio se va reducioendo a medida que voy agregando optimizaciones:<br>

### Programa original:

|            | Real   | User   | Sys    |
|------------|--------|--------|--------|
| Ejecución 1| 0,45   | 0,137  | 0,203  |
| Ejecución 2| 0,438  | 0,156  | 0,175  |
| Ejecución 3| 0,462  | 0,174  | 0,191  |
| Ejecución 4| 0,49   | 0,191  | 0,183  |
| Ejecución 5| 0,498  | 0,194  | 0,19   |
| Promedio   | 0,4676 | 0,1704 | 0,1884 |


Se ve como el promedio de tiempo de ejecucion es 0,4644. Este es mi punto de partida.<br>

### Optimizacion 1:
La primer optimizacion se llevó a cabo a la funcion "alloc_matrix". Lo que se hizo basicamente fue que en vez de alocar memoria a a cada columna de la matriz y luego recorrerla para setearla en 'j', se utilizo la funcion calloc() para alocar cada columna e ir seteandola en cero. Si bien no estamos seteando a cada elemento de la matriz en 'j', no hay problema en setearlos en cero ya que luego se va a llenar la matriz con otros valores mediante la funcion fill().<br><br> 
El codigo de la izquierda es el optimizado y el de la derecha es el original
![imagen](https://user-images.githubusercontent.com/88598932/234174699-8569313c-b94e-4fe3-a273-b13010a4760b.png)

Tabla de tiempos:

|            | Real   | User   | Sys    |
|------------|--------|--------|--------|
| Ejecución 1| 0,444  | 0,166  | 0,187  |
| Ejecución 2| 0,395  | 0,116  | 0,19   |
| Ejecución 3| 0,427  | 0,113  | 0,19   |
| Ejecución 4| 0,422  | 0,096  | 0,194  |
| Ejecución 5| 0,419  | 0,201  | 0,145  |
| Promedio   | 0,4214 | 0,1384 | 0,1812 |


Por lo que vemos, he obtenido una mejora en cuanto al original pero es muy pequeña, el tiempo de ejecucion ha pasado de 0,4644 [seg] a 0,423 [seg].
### Optimizacion 2:
El codigo de la izquierda es el optimizado y el de la derecha es el original
![imagen](https://user-images.githubusercontent.com/88598932/234175762-c3a4be7b-631e-4336-90fe-cb88eeeb22d3.png)
Tabla de tiempos:

|            | Real   | User   | Sys    |
|------------|--------|--------|--------|
| Ejecución 1| 0,411  | 0,175  | 0,168  |
| Ejecución 2| 0,381  | 0,12   | 0,15   |
| Ejecución 3| 0,395  | 0,143  | 0,165  |
| Ejecución 4| 0,394  | 0,147  | 0,157  |
| Ejecución 5| 0,401  | 0,139  | 0,169  |
| Promedio   | 0,3964 | 0,1448 | 0,1618 |

### Optimizacion 3:
La segunda optimizacion consistio en reemplazar los printf() por fprint() en las funciones "compute" y "print". Lo que realicé fue en vez de sacar la salida del programa por la consola, escribirla en un archivo llamado "output.txt". Realicé este cambio ya que printf() escribe en consola, siendo esto una operacion relativamente costosa en términos de tiempo pero por otro lado, fprintf() escribe directamente en un archivo y no necesita esperar a que cada carácter se muestre en la pantalla, lo que puede ralentizar el proceso de escritura en el caso de la consola cuando estamos imprimiendo muchos datos como es el caso.

Tabla de tiempos:

|            | Real   | User   | Sys    |
|------------|--------|--------|--------|
| Ejecución 1| 0,056  | 0,05   | 0,006  |
| Ejecución 2| 0,048  | 0,039  | 0,009  |
| Ejecución 3| 0,052  | 0,037  | 0,013  |
| Ejecución 4| 0,06   | 0,056  | 0,004  |
| Ejecución 5| 0,054  | 0,038  | 0,016  |
| Promedio   | 0,054  | 0,044  | 0,0096 |


En este caso el cambio es demasiado grande, he obtenido una gran mejora en cuanto al programa original con la optimizacion 1 agregad. El tiempo de ejecucion ha pasado de 0,423 [seg] a 0,0566 [seg].

### POSIBLE Optimizacion:

Tabla de tiempos:

|            | Real   | User   | Sys    |
|------------|--------|--------|--------|
| Ejecución 1| 0,042  | 0,034  | 0,008  |
| Ejecución 2| 0,044  | 0,036  | 0,008  |
| Ejecución 3| 0,048  | 0,042  | 0,006  |
| Ejecución 4| 0,05   | 0,045  | 0,005  |
| Ejecución 5| 0,045  | 0,041  | 0,004  |
| Promedio   | 0,0458 | 0,0396 | 0,0062 |

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



