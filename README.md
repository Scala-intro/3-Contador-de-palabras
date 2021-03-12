# 3 Contador de palabras
## Versión 1 
Vamos a empezar a hacer un ejercicio, un contador de palabras.

- Lo primero es ejecutar el contexto de spark
~~~scala
sc.version
~~~
![spark](./images/001.png)

- Después creamos un lista de frases.

~~~scala
val frases = List("Un día fui a comprar un ordenador", "Me dijeron que el mejor es el mac", "pero también me dijeron que es el más caro", "Al final decicí por comprarme el mac", "pagué el precio y ahora estoy muy contento")
~~~

- Ahora vamos a funsionar spark y scala. 
Pedimo a spark que suba a memoria la colección de frases, ya que spark trabaja en memoria.

~~~scala
val texto = sc.parallelize(frases)
~~~

- Ahora toca contar palabras
    
    1º  pasamos la líneas a minúsculas
    ~~~scala
    toLowerCase
    ~~~
    2º dividimos las frases en palabras con una expresión regular
    ~~~scala
    split("\\W+")
    ~~~
    3º y el resultado lo presentamos en una tupla, (palbara, 1)
    ~~~scala
    map(pal => (pal ,1))
    ~~~
    4º Hacemos un conteo clave/valor con reduceByKey
    ~~~scala
    reduceByKey(_+_)
    ~~~

    ~~~scala
    val numPalabras = texto.flatMap(line => line.toLowerCase.split("\\W+")).map(pal => (pal ,1)).reduceByKey(_+_)
    ~~~
    5º Obtenemos un RDD (Resilient Distributed Dataset) al cual le podemos hacer operaciones.

    ~~~scala
    numPalabras.collect().foreach(println(_))
    ~~~
    ![scala](./images/002.png)


## Versión 2 

Vamos a utilizar un dataset localizado en (https://tinyurl.com/rja-901) o (https://github.com/jalbacar/github-demo/blob/master/quijote.txt.zip)

1º Vamos a importar algunas librerías
~~~
import org.apache.spark._
import org.apache.spark.SparkContext._
import org.apache.log4j._
~~~

2º Creamos la función `procesarQuijote`
~~~scala
def procesarQuijote()
{
    // RDD
    val input = sc.textFile("file:///home/patricia/Documentos/scala/contador_palabras/data/quijote.txt")
    
    val palabras = input.flatMap(linea => linea.split("\\W+"))
    
    val sumPalabras = palabras.countByValue()
    
    //Imprimo resultado
    sumPalabras.foreach(println)

}

//lanzar proceso
procesarQuijote()
~~~

![scala](./images/003.png)