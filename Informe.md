## Trabajo Práctico 4-SO2

## Alumno: Marco Valentino Di Giannantonio.
## LinkRepo: https://github.com/ICOMP-UNC/2023---soii---laboratorio-iv-Valentino-DG


### Primero voy a listar el hardware del sistema con el que realicé el trabajo:
![imagen](https://github.com/Valentino-DG/PracticaDeGit/assets/88598932/a4e13583-1636-49fb-8660-cf1a0342bb8f)

## En que consiste el laboratorio:
El laboratorio consiste en diseñar un programa que a partir de una imagen ".tif" obtener sus 3 bandas RGB, y aplicar una convolucion a cada una de estas utilizando un filtro o kernel de borde. Dando por resultado 3 imagenes filtradas, una para cada banda. El diseño debe adaptarse a distintas imagenes de distintos tamaños.<br>
Primero el programa debe realizarse sin explotar el paralelismo, una vez terminado el programa y que cumple con los requerimientos, se debe realizar una nueva implementacion pero utilizando la libreria OpenMP y explotar al máximo el paralelismo del problema.

## Mi programa serial:
El código fuente es "labSerie.c".
Hago mucho uso de la libreria GDAL para leer y procesar imagenes raster. El porgrama consiste en 2 cosas:
1) Se abre la imagen ".tif", se lee una de las bandas de esta (primeramente la roja) , luego se almacenan los datos en una matriz, y posteriormente se genera una imágen de salida en formato TIFF a partir de dicha matriz (esta va a corresponder a la banda extraida). Esto se hace para las 3 bandas de la imagen, por lo que como resultado obtengo 3 imagenes ".tif" monocromaticas, donde una representa la banda roja de la imagen original, otra la banda verde y otra la banda azul.  
2) Se aplica un filtro de convolución a cada una de las matrices de las bandas RGB utilizando un filtro o kernel de borde y luego las matrices convolucionadas en imagenes ".tif" una imagen por convolucion. Por ende lo que termino obteniendo son 3 imagenes ".tif", que corresponden a cada unas de las bandas habiendolas filtrado por dicho kernel.

## Mi programa paralelo:
El código fuente es "labParalelo.c".
Lo que hice fue partir de mi programa serial y aplicar paralelismo de hilos en las secciones que consideré correctas porque a mi parecer explotan el paralelismo del problema, además se agregó un poco de dinamismo permitiendole al usuario que elija la cantidad de hilos a utilizar en las regiones paralelizadas y el scheduling (static, dynamic, o guided) para la asignacion de iteraciones de los bucles for que se paralelizan (estos dos valores se ingresan como argumentos al programa). La paralelización se implementó mediante el uso de la libreria OpenMP.

- En la planificación **static**, las iteraciones del bucle for se dividen en bloques de tamaño igual (o casi igual) y se asignan a los hilos.
- En la planificación **dynamic**, las iteraciones del bucle for se asignan a los hilos a medida que estos terminan sus tareas.
- En la planificación **guided**, los bloques de iteraciones del bucle for que se asigan a los hilos se reducen a medida que los hilos completan sus tareas (primero a cada hilo se le asigna un bloque de iteraciones y luego cuando terminan la tarea se les asigan uno mas chico y así sucesivamente). 

### Modificaciones:

1) Se paralelizó los 3 llamados a la función "extraerBanda_y_generarImagen". Esta función extrae del dataset (imagen) la banda especificada por parametro, luego se la carga en una matriz y se llama a la función "generarImagen" que genera una imagen ".tif" partir de la matriz que se recibe.
Se realizó esta paralelización ya que los 3 llamados a esta funciòn actuan de forma independiente entre sí (en realidad no tan independiente, ya que comparten un recurso que se va a exlpicar en la modificación 2) ya que se actua con bandas distintas de la imagen (un llamado trabaja con la banda roja, otro con la azul y otro con la verde). 

![imagen](https://github.com/Valentino-DG/PracticaDeGit/assets/88598932/b3ac92c8-cd27-4e8a-bc91-9f0213ec7880)

2) Se tuvo que agregar una sección critica en el la funcion "extraerBanda_y_generarImagen" debido a que cuando los hilos creados para paralelizar estos 3 llamados intentaban leer los datos de la banda extraida del dataset y escribirlos en la matriz que tiene como fin almacenar estos datos se producian errores ya que los hilos se interferian.

![imagen](https://github.com/Valentino-DG/PracticaDeGit/assets/88598932/0d0de353-c21b-4450-9d52-4df39be7f902)

3) Como tercer modificación fue el proceso de convolución de cada banda con el filtro de deteccion de bordes. Este cambio fue el que maás impacto tuvo en cuanto a rendimiento ya que las convoluciones son bastante pesadas y de nuevo, se hacer las 3 convoluciones en paralelo tranquilamente ya que son procedimientos independientes entre ellos. 
Los bucles for fueron los que sufrieron la paralelización, se aplicó una opcion llamada "collapse(2)", esto lo que hace es que convertir los dos primeros bucles for en uno y que se haga una paralelizacion a nivel de pixel es decir de (y,x) en vez de paralelizar a nivel de filas (y) como sería el caso en que no ponga la opcion de collapse(2). Esta paralelización a nivel de pixel permite distribur mejor la carga entre los hilos. Esta opcion de collapse(2) me brindó una mejora con respecto a no usarla, si bien no fue grande la hubo.

![imagen](https://github.com/Valentino-DG/PracticaDeGit/assets/88598932/4576e0ad-fe9f-4f20-9995-38d2945e2016)

## Estadísticas y gráfico de rendimiento:
Se van a tomar el tiempo en segundos que demora ejecutar el programa serie y los tiempos en segundos que demoran ejecutar el programa paralelo usando 1,2,3,4,5,6,7 y 8 hilos para las regiones paralelizadas y se va a utilizar un tipo descheduling "static" ya que probando con "dynamic" y "guided" obtengo prácticamente los mismos tiempos con los 3. Luego se hará un gráfico mostrando los diferentes desempeños.


|               | Serial | 1 Hilo  | 2 Hilos | 3 Hilos | 4 Hilos | 5 Hilos | 6 Hilos | 7 Hilos | 8 Hilos |
|---------------|--------|---------|---------|---------|---------|---------|---------|---------|---------|
| Ejecución 1   | 18.795 | 19.805  | 10.238  | 7.163   | 6.325   | 6.345   | 6.194   | 5.754   | 5.593   |
| Ejecución 2   | 18.359 | 19.559  | 10.492  | 7.171   | 6.672   | 6.412   | 6.226   | 5.794   | 5.611   |
| Ejecución 3   | 18.458 | 19.588  | 10.27   | 7.125   | 6.394   | 6.326   | 6.254   | 5.967   | 5.581   |
| Ejecución 4   | 18.42  | 19.609  | 10.324  | 7.482   | 6.238   | 6.427   | 6.391   | 5.927   | 5.638   |
| Ejecución 5   | 18.616 | 19.601  | 10.397  | 7.165   | 6.462   | 6.354   | 6.375   | 5.798   | 5.477   |
|---------------|--------|---------|---------|---------|---------|---------|---------|---------|---------|
| Promedio      | 18.53  | 19.63   | 10.344  | 7.221   | 6.418   | 6.373   | 6.288   | 5.848   | 5.58    |


Se visualiza cuanto es mejora porcentual de la ejecucion del programa paralelo (con cada numero de hilos hasta 8) con respecto a la ejecucion del programa en serie:

|                  | 1 Hilo | 2 Hilos | 3 Hilos | 4 Hilos | 5 Hilos | 6 Hilos | 7 Hilos | 8 Hilos |
|------------------|--------|---------|---------|---------|---------|---------|---------|---------|
| Mejora porcentual| -5.95  |  44.17  |  61.02  |  65.36  |  65.6   |  66.06  |  68.43  |  69.88  |

Se observa que con 8 hilos se tiene el máximo desempeño, se redujo el tiempo de ejecución casi un 70% con respecto al programa en serie.

### Se visualiza gráficamente:

![imagen](https://github.com/Valentino-DG/PracticaDeGit/assets/88598932/534fabae-af8a-495b-84d6-01e2409c6967)

Cuando ejecuto el programa paralelo especificando 1 solo hilo, tengo un disminucion de la velocidad de ejecucion en un total del 5,95% esto se lo atriubuyo a que estoy perdiendo velocidad por la sobrecarga de la libreria OpenMP, debido a que la estoy usando pero no estoy creando hilos en las zonas paralelas, solo ejecuto con uno, es decir en serie.


### Uso de los flags de optimización:

Se probaron los flags de optimización -O1, -O2, y -O3 para la compilación de los codigos fuente labSerie.c y labParalelo.c y se obtuvieron grandes mejoras.

Se muestra el desempeño para cada uno de los flags, ejecutando el programa serial y el paralelo, este último con 8 hilos ya que con esta cantidad es la que se obtuvo la velocidad máxima de ejecución.

Ejecución con -O1:
![imagen](https://github.com/Valentino-DG/PracticaDeGit/assets/88598932/a0236122-25e6-4acb-ba5a-fb4478d73ca4)

Ejecución con -O2:
![imagen](https://github.com/Valentino-DG/PracticaDeGit/assets/88598932/2febb2a2-00f1-4e2b-8a2b-67492e2ba256)

Ejecución con -O3:
![imagen](https://github.com/Valentino-DG/PracticaDeGit/assets/88598932/803c27fa-75d0-4075-b66f-51c33d939a2f)


Los tiempos de ejecuciòn se redujeron mucho, y los porcentaje de mejoras de velocidad del programa paralelo con respecto al serie disminuyeron:

Para el flag -O1: 60.37%<br>
Para el flag -O2: 61.12%<br>
Para el flag -O3: 40.42%<br>

Aunque porcentualemte con el flag -O3 obtengo una menor mejora de velocidad con respecto al programa serie, este flag me acelera más la ejeución de los programas (serie y paralelo) que con respecto al uso de los demás flags.

Voy a nombrar algunas razones que yo creo del por qué utilizando estos flags de optimización se empeora porcentualmente la velocidad del programa paralelo con respecto al programa serie, ya que sin usar estos flags la mejora porcentual era del 70% aprox (TENER EN CUENTA QUE SI BIEN LA MEJORA PORCENTUAL DISMINUYE, LA MEJORA EN TIEMPO DE EJECUCIÓN ES MUY GRANDE):

**Sobrecarga del paralelismo:** Al ejecutar un programa en paralelo, hay una cierta cantidad de sobrecarga asociada con la creación y gestión de múltiples hilos. A medida que el tiempo de ejecución de cada hilo se reduce (debido a las optimizaciones), esta sobrecarga puede llegar a ser una parte más significativa del tiempo total de ejecución.

¿Por que creo que cae mas aun el procentaje cuando se usa el flag -O3?<br>
**Efectos del paralelismo en las optimizaciones:** Algunas de las optimizaciones realizadas por -O3 pueden ser menos efectivas (o incluso perjudiciales) en un entorno paralelo. Por ejemplo, las optimizaciones que aumentan el uso de la memoria caché pueden conducir a conflictos de caché cuando se ejecutan en paralelo.
