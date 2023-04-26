## Trabajo Práctico 3-SO2

## Alumno: Marco Valentino Di Giannantonio.
## LinkRepo: https://github.com/ICOMP-UNC/2023---soii---laboratorio-iii-Valentino-DG

## 1 - Optimización utilizando comando Time: 
Lo primero que se va a hacer es tomar el programa original y se lo va a ejecutar un total de 5 veces con el comando "time" para sacar un promedio del tiempo de ejecución, de forma tal de tener un punto de partida y ver como el promedio se va reduciendo a medida que voy agregando optimizaciones al programa:<br>

### Programa original:

Tabla de tiempos (la unidad es segundos):

|            | Real   | User   | Sys    |
|------------|--------|--------|--------|
| Ejecución 1| 0,45   | 0,137  | 0,203  |
| Ejecución 2| 0,438  | 0,156  | 0,175  |
| Ejecución 3| 0,462  | 0,174  | 0,191  |
| Ejecución 4| 0,49   | 0,191  | 0,183  |
| Ejecución 5| 0,498  | 0,194  | 0,19   |
| Promedio   | 0,4676 | 0,1704 | 0,1884 |


Se ve como el promedio de tiempo de ejecución es 0,4676 [seg]. Este es mi punto de partida.<br>

### Optimización 1:
La primer optimizacion se llevó a cabo a la función "alloc_matrix". Lo que se hizo basicamente fue que en vez de alocar memoria para todas las columnas de cada fila de la matriz con malloc() y luego recorrerla a la matriz para setear en 'j'  cada uno de sus elementos con memset(), se utilizó directamente la función calloc() para alocar memoria para todas las columnas de cada fila y a su vez seteralas a cero. Si bien no estamos seteando a cada elemento de la matriz en 'j', no hay problema en setearlos en cero ya que luego se va a llenar la matriz con otros valores mediante la función "fill".<br><br>
El código de la izquierda es el optimizado y el de la derecha es el original.
![imagen](https://user-images.githubusercontent.com/88598932/234174699-8569313c-b94e-4fe3-a273-b13010a4760b.png)

Tabla de tiempos (la unidad es segundos):

|            | Real   | User   | Sys    |
|------------|--------|--------|--------|
| Ejecución 1| 0,444  | 0,166  | 0,187  |
| Ejecución 2| 0,395  | 0,116  | 0,19   |
| Ejecución 3| 0,427  | 0,113  | 0,19   |
| Ejecución 4| 0,422  | 0,096  | 0,194  |
| Ejecución 5| 0,419  | 0,201  | 0,145  |
| Promedio   | 0,4214 | 0,1384 | 0,1812 |


Por lo que se ve, he obtenido una mejora en cuanto al programa origina,l pero es muy pequeña, el tiempo de ejecución en promedio ha pasado de 0,4676 [seg] a 0,4214 [seg].

### Optimización 2:
La segunda optimización se llevó a cabo en la función "compute". Se hicieron 2 cambios. El primer cambio fue eliminar el arreglo temporal "tmp_sum" que se encarga de almacenar los 9 valores calculados de la convolución del kernel y 9 valores de la matriz de datos, eliminando asi el tener que acceder a dicha variable tanto para almacenar los valores de la convolucion como para leer tales valores para hacer la suma que se almacena en la variable "accum". El segundo cambio fue que en vez de almacenar estos nueve valores y luego hacer la suma de los mismos guardando el resultado de las sumas en la variable "accum", lo que se hace es que a medida que se va recorriendo cada elmento del kernel y se va haciendo la convulución con el elemento correspondiente de la matriz de datos ir directamente sumando en la variable "accum" este resutlado.<br>
Con estos dos cambios estoy evitando el tener una variable temporal que se va a tener que acceder muchas veces, para almacenar los valores de la convolución y posteriormente la lectura de los mismos para almacenar su suma en la variable "accum", eliminando asi los dos bucles for destinados a esto último.<br><br>
El código de la izquierda es el optimizado y el de la derecha es el original
![imagen](https://user-images.githubusercontent.com/88598932/234175762-c3a4be7b-631e-4336-90fe-cb88eeeb22d3.png)

Tabla de tiempos (la unidad es segundos):

|            | Real   | User   | Sys    |
|------------|--------|--------|--------|
| Ejecución 1| 0,411  | 0,175  | 0,168  |
| Ejecución 2| 0,381  | 0,12   | 0,15   |
| Ejecución 3| 0,395  | 0,143  | 0,165  |
| Ejecución 4| 0,394  | 0,147  | 0,157  |
| Ejecución 5| 0,401  | 0,139  | 0,169  |
| Promedio   | 0,3964 | 0,1448 | 0,1618 |

Por lo que se ve, he obtenido una mejora pequeña en cuanto al codigo con la primera optimización, el tiempo de ejecución ha pasado de 0,4214 [seg] a 0,3964 [seg].

### Optimización 3:
La tercera optimizacion consistió en reemplazar los printf() por fprint() en las funciones "compute" y "print". Lo que realicé fue en vez de sacar la salida del programa por la consola, escribirla en un archivo llamado "outputSoloTime.txt". Realicé este cambio ya que printf() escribe en consola, siendo esto una operacion relativamente costosa en términos de tiempo, pero por otro lado, fprintf() escribe directamente en un archivo y no necesita esperar a que cada carácter se muestre en la pantalla, lo que puede ralentizar el proceso de escritura en el caso de la consola cuando estamos imprimiendo muchos datos como es el caso.

Tabla de tiempos (la unidad es segundos):

|            | Real   | User   | Sys    |
|------------|--------|--------|--------|
| Ejecución 1| 0,056  | 0,05   | 0,006  |
| Ejecución 2| 0,048  | 0,039  | 0,009  |
| Ejecución 3| 0,052  | 0,037  | 0,013  |
| Ejecución 4| 0,06   | 0,056  | 0,004  |
| Ejecución 5| 0,054  | 0,038  | 0,016  |
| Promedio   | 0,054  | 0,044  | 0,0096 |


En este caso la mejora de velocidad es abismal, he obtenido una gran mejora en cuanto al programa original con la optimizacion 1 y 2 agregada. El tiempo de ejecución ha pasado de 0,3964 [seg] a 0,054 [seg].

### POSIBLE Optimización:
Pensé en una optimzación que a mi punto de vista es necesaria, pero como modifica el output con respecto al programa original **NO** la dejé implementada pero muestro los resultados con la mejora de tiempos. La optimización consiste en eliminar la impresión que se hace en la funcion "compute" ya que la veo como una sobrecarga innecesaria a diferencia de la impresión de la matriz en la funcion "print" que si es imporante. Modifica el output con respecto al programa original ya que no se va a contar con tales impresiones.

Tabla de tiempos (la unidad es segundos):

|            | Real   | User   | Sys    |
|------------|--------|--------|--------|
| Ejecución 1| 0,042  | 0,034  | 0,008  |
| Ejecución 2| 0,044  | 0,036  | 0,008  |
| Ejecución 3| 0,048  | 0,042  | 0,006  |
| Ejecución 4| 0,05   | 0,045  | 0,005  |
| Ejecución 5| 0,045  | 0,041  | 0,004  |
| Promedio   | 0,0458 | 0,0396 | 0,0062 |

En este caso el tiempo de ejecución ha pasado de 0,054 [seg] correspondiente al programa original con la optimizacion 1,2 y 3 agregadas a 0,0458 [seg].

## 2 - Optimización utilizando Valgrind: 
Lo que se va a hacer es tomar el programa original (sin ninguna optimización) y ejecutarlo con el analizador dinámico valgrind.
El ajecutar el programa con el uso de valgrind, errores del siguiente estilo se pronuncian una gran cantidad repetida de veces:

![imagen](https://user-images.githubusercontent.com/88598932/234410279-5c1416a8-58a6-4d82-ba30-9fb73ea406c6.png)

Esto me está indicando se está creando una variable sin inicializar en la función "compute" y que se está haciendo uso de ella. Rastreando un poco el código de la función compute la variable sin inicializar es la variable de tipo "accum" que se utiliza almacenar el resultado de la convolución y asignarla a las posiciones de la matriz. El problema es que la variable accum se inicializa si es que se ingresa a la condicion if(), a este if solo se ingresa en el caso en que los elementos de la matriz no pertenezcan al borde de la misma. El problema radica en que inicialmente al empezar a recorrer la matriz, los primeros elementos van a pertencer al borde de la misma ya que se va barriendo la matriz desde la primera fila (la primera fila es el borde superior), por ende no se va a ingresar a este if(), entonces la variable "accum" no se va a inicializar y al elemento de la matriz se le va a asignar basura. Para solucionar esto se inicializa la variable "accum" a cero antes de entrar a los bucles de la funcion compute.

Otro registro de errores que me sale son los siguientes:
![imagen](https://user-images.githubusercontent.com/88598932/234409577-668305fb-0105-40b6-996c-376f5b09d1f4.png)

Esto me está indicando que he alocado memoria y no la estoy desalocando. De forma mas precisa, se me está informando que estoy perdiendo (no liberando) 80800 Bytes, 800 Bytes son los que aloqué para los 100 punteros double que apuntan a cada una de las filas de la matriz, y los 80000 Bytes restantes son los 10000 elemetos double de la matriz. Es decir que en definitiva la memoria alocada para crear la matriz no la estoy liberando y esto me puede llevar a un problema de memory leak. Para solucionar esto he agregado una función que se encargue de hacer el free a la matriz (dicha función es llamada en el main al final del código):

![imagen](https://user-images.githubusercontent.com/88598932/233873901-92fd9c71-4e76-4aa9-abc4-61ed2caea239.png)

Una vez hechas estas correcciones paso a ejecutar el programa con valgrind de nuevo:

![imagen](https://user-images.githubusercontent.com/88598932/234412494-51235cb2-63bd-4f19-a1a7-59a569daf977.png)

Se puede observar que los errores han sido corregidos.

Una vez hechas estas correcciones de manejo de memoria vamos a tomar el tiempo de ejecución del programa. Como no se hizo ninguna optimización de tiempo sino que se hizo una optimizacion en el manejo de memoria se espera que el tiempo de ejecución sea prácticamente el mismo que el del programa original:

Tabla de tiempos (la unidad es segundos):

|            | Real   | User   | Sys    |
|------------|--------|--------|--------|
| Ejecución 1| 0,433  | 0,16   | 0,188  |
| Ejecución 2| 0,497  | 0,124  | 0,231  |
| Ejecución 3| 0,453  | 0,147  | 0,2    |
| Ejecución 4| 0,464  | 0,106  | 0,19   |
| Ejecución 5| 0,462  | 0,156  | 0,191  |
| Promedio   | 0,4618 | 0,1386 | 0,2    |

Como se observa, el tiempo es casi el mismo que para el programa original. A mi parecer este programa deberia demorar un poco más en ejecutarse que el programa original ya que se agrega código para liberar la memoria alocada. En mi opinión, para poder notar este cambio necesitaria tomar mas ejecuciones para sacar el promedio y tomar una matriz de mayor tamaño ya que la liberación de la memoria se ejecuta muy rapido en una matriz de no tantos elementos.

## 3 - Comparación de ámbas versiones:
En cuanto a las diferencias entre ambas versiones podemos destacar dos. Una de las diferencias es que en la version optimizada sin el uso de valgrind, el tiempo de ejecución es mucho mas chico con respecto a la optimización con el uso de valgrind, estamos hablando de 0,054 [seg] para la optimización sin el uso de valgrind y 0,4618 [seg] para la optimización con el uso de valgrind. La otra diferencia es que para el caso de la optimización con el uso de valgrind se está manejando mucho mejor la memoria ya que valgrind me permitió darme cuenta de que habia memoria que no estaba liberando produciendome asi un memory leak pudiéndome potencialemte quedarme sin memoria y además me permitió darme cuenta de que habia una variable sin inicializar que podria afectar el resultado del programa.<br>
Por ende en mi opinión no es que una versión sea mejor que la otra sino que en una optimicé velocidad por sobre manejo de memoria y en otra optimicé mucho el manejo de memoria, por lo que lo mejor seria hacer una nueva versión fusionando ámbas. 
Dicha version se realizó y se encuentra en el repo con el nombre de "laboratorio3_OptTimeMasValgrind.c".

## 4 - Unit Test para validar software:
Se llevó a cabo un testeo para verificar que la salida de los nuevos programas generados debido al agregado de las optimizaciones no han cambiado con respecto a la salida del programa original.<br>

Para ello se tuvo que setear la semilla que genera los valores pseudo aleatorios en los distintos programas en un valor constante (se eligió el entero '10' de forma arbitraria) ya que si no vamos a tener distintas salidas ya que a las matrices en cada uno de los programas se llenarán con valores distintos.<br>

La forma de comprobar que las salidas de los programas son iguales fue mediante el comando **sha1sum <archivo.txt>** que se utiliza para calcular el valor de hash SHA-1 (valor hash de 160 bits) de un archivo y mostrar el resultado en la pantalla. Por ende se va a calcular el valor de hash SHA-1 de todas las salidas de los programas (todas las optimizaciones y el original) y en caso de que los hash de las salidas de los programas que sufrieron alguna optimizacion coincidan con el hash de la salida del programa original es porque no hubo cambios en los outputs con respecto al programa original, que es lo que estamos buscando.

![imagen](https://user-images.githubusercontent.com/88598932/234428742-7f28d2e2-91ff-47ed-a0f9-72d0f94bd9ee.png)

Se observa que no hubo cambios en la salidas de los programas optimizados con respecto a la salida del programa original ya que los hash coinciden.

**Se provee un script (en la carpeta "unit_test") que se encarga de compilar los códigos fuente de testeo, ejecutar los programas resultantes y de generar los hash de las salidas de los programas, asi se puede corroborar que no hay variación en los outputs. Todo esto de forma automática sin la necesidad de tener que hacerlo a mano.**
