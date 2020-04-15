# Informe TP0

## Detalles administrativos

* **Materia:** Taller de Programación I
* **Año y cuatrimestre:** 1°C 2020
* **Alumno:** PARAFATI, Mauro
* **Padrón:** 102749
* [**Link al repositorio**](https://github.com/mauro7x/taller_tp0)

<!-- ##################################################################### -->

# Tabla de contenidos

1. [Presentación del trabajo](#presentacion)
    * [Descripción del problema](#descripcion)
    * [Especificaciones requeridas por la cátedra](#especificaciones)
    * [Restricciones](#reestricciones)
2. [Resolución](#resolucion)
    * [Paso 0: Entorno de trabajo](#r_paso0)
    * [Paso 1: SERCOM - Errores de generación y normas de programación](#r_paso1)
    * [Paso 2: SERCOM - Errores de generación 2](#r_paso2)
    * [Paso 3: SERCOM - Errores de generación 3](#r_paso3)
    * [Paso 4: SERCOM - Memory Leaks y ​ Buffer Overflows](#r_paso4)
    * [Paso 5: SERCOM - Código de retorno y salida estándar](#r_paso5)
    * [Paso 6: SERCOM - Entrega exitosa](#r_paso6)
3. [Conclusiones](#conclusiones)

<!-- ##################################################################### -->

# Presentación del trabajo <a name="presentacion"></a>

## Descripción del problema <a name="descripcion"></a>

> Se requiere una aplicación de consola que cuente palabras y líneas de un archivo de texto plano determinado.

## Especificaciones requeridas por la cátedra <a name="especificaciones"></a>

### De funcionalidad:

> El sistema debe recibir el archivo de texto por entrada estándar o como argumento de invocación por línea de comando.
>
> El proceso del archivo consiste en analizar su contenido contando las palabras como conjuntos de caracteres separados por cierto delimitador.
> 
> El total encontrado se debe imprimir por salida estándar.

### De formato:

> La sintaxis de ejecución del aplicativo será la siguiente:
>
> `./tp [archivo]`
>
> El parámetro [​archivo​] es opcional e indica la ruta del archivo de texto a procesar. Su ausencia indica el uso de la entrada estándar.

### De códigos de retorno:

> Se requieren los siguientes códigos de error:
>    - 0 en caso de éxito,
>    - 1 en caso de que el archivo especificado no exista o no se poseen permisos de lectura.


### De la entrada estándar y archivos de texto:

> De ser requerido, el sistema leerá el contenido del archivo de entrada estándar. En cualquier caso, el formato del archivo de entrada se asume como texto plano con caracteres usuales.
> 
> Se considera una palabra a cualquier conjunto de caracteres que se encuentre delimitado por alguno de los siguientes elementos:
>    - inicio de archivo,
>    - fin de archivo,
>    - caracteres delimitadores: espacio (‘ ‘), fin de línea (‘\n’), punto (‘.’), coma (‘,’), punto y coma (‘;’), dos puntos (‘:’).


### De la salida estándar:

> Se espera el siguiente formato de salida estándar:
> 
> `<cantidad-palabras>\n`


## Restricciones <a name="reestricciones"></a>

> La siguiente es una lista de restricciones técnicas exigidas para el sistema:
> 1. El sistema debe desarrollarse en ISO C (C99).
> 2. Está prohibido el uso de variables globales.
> 3. Se requiere un correcto uso de Tipos de Datos Abstractos (TDA).
> 4. Está prohibido el uso de funciones globales sin su correspondiente TDA salvo ‘ main​ ’.
> 5. Se deben respetar el uso de buenas prácticas de programación.


<!-- ##################################################################### -->

# Resolución <a name="resolucion"></a>
Este trabajo se realizó siguiendo una serie de **pasos** provistos por la cátedra. Se procede a detallar el realizamiento de los mismos a continuación.

## PASO 0: Entorno de trabajo <a name="r_paso0"></a>

> Si bien SERCOM es capaz de compilar y someter a prueba las entregas del alumno, usarlo como entorno de trabajo remoto es lento e ineficiente.
> Por eso es importante que el alumno invierta un tiempo en la configuración de un entorno de trabajo local que le será mucho más redituable y cómodo.


### Documentación requerida


> **a.** Capturas de pantalla de la ejecución del aplicativo (con y sin Valgrind).

Se incluyen a continuación las capturas de pantallas pedidas:

1. Ejecución del aplicativo sin Valgrind: ![esta es una imagen](img/p0-ejecucion_gcc.png)

2. Ejecución del aplicativo con Valgrind: ![esta es una imagen](img/p0-ejecucion_valgrind.png)

#

> **b.** ¿Para qué sirve ​ Valgrind​? ¿Cuáles son sus opciones más comunes?

Valgrind es una herramienta que nos permite, entre otras cosas, verificar que nuestro código no tenga problemas con el **manejo de la memoria** dinámica, como pueden ser los llamados `memory leaks`, en los que nunca se libera la memoria reservada.

Si bien sé que Valgrind tiene otras opciones, como desconozco sus utilidades me limito a contestar sobre el uso que le dí en anteriores materias *(Algoritmos y Programación II)*.

#

> **c.** ¿Qué representa ​ sizeof()​? ¿Cuál sería el valor de salida de sizeof(char)​ y ​sizeof(int)​?

`sizeof()` es una **función** provista por el lenguaje que nos proporciona información sobre la **cantidad de bytes** que ocupa una determinada variable o un determinado tipo de variable **en nuestra arquitectura**. Es muy útil para cuando queremos reservar memoria, ya que necesitamos saber cuántos bytes ocupan las variables que queremos alojar en la misma.

El valor de salida de `sizeof()` (en particular, de `sizeof(char)` y de `sizeof(int)`) **no es fijo** ya que siempre **depende** de la arquitectura en la que se está trabajando. En general, `sizeof(char)` devolvera `1` mientras que `sizeof(int)` devolvera `4`, pero repito, depende siempre de la arquitectura.

#

> **d.** ¿El ​ sizeof()​ de una struct de C es igual a la suma del sizeof() ​de cada uno sus elementos?
>
> Justifique mediante un ejemplo.

Como siempre, la respuesta es **depende** de la arquitectura en uso, pero en general, esto no se cumple ya que si bien un `char` puede ocupar `1 byte`, por cuestiones de **alineamiento** en la memoria (es conveniente tener las variables en memoria *alineadas* a n bytes, por lo general, a 4 o a 8, ya que facilita su acceso) terminan quedando bytes "desperdiciados".

Para ejemplificar, definamos el siguiente struct:
```
[In: 1]     struct Ejemplo {
                char a;
                int b;
            };
```
Como vemos, suponiendo que la salida de `sizeof(char)` sea 1 y que la de `sizeof(int)` sea 4, la suma de los `sizeof()` de los respectivos componentes de la estructura, sería 5.

Sin embargo, si corremos `sizeof(struct Ejemplo)`, el resultado no será 5, sino 8, que corresponde a 1 byte del char `a`, 3 bytes "desperdiciados" por la alineación a 4 bytes, y 4 bytes del int `b`.
```
[In: 2]     sizeof(char)
[Out: 2]    1

[In: 3]     sizeof(int)
[Out: 3]    4

[In: 4]     sizeof(struct Ejemplo)
[Out: 4]    8
```

#

> **e.** Investigar la existencia de los archivos estándar: STDIN, STDOUT, STDERR.
> 
> Explicar brevemente su uso y cómo redirigirlos en caso de ser necesario (caracteres `>`​ y `​<`​) y como conectar la salida estándar de un proceso a la entrada estándar de otro con un pipe​ (carácter `|`).


## PASO 1: SERCOM - Errores de generación y normas de programación <a name="r_paso1"></a>

## PASO 2: SERCOM - Errores de generación 2 <a name="r_paso2"></a>

## PASO 3: SERCOM - Errores de generación 3 <a name="r_paso3"></a>

## PASO 4: SERCOM - Memory Leaks y ​ Buffer Overflows <a name="r_paso4"></a>

## PASO 5: SERCOM - Código de retorno y salida estándar <a name="r_paso5"></a>

## PASO 6: SERCOM - Entrega exitosa <a name="r_paso6"></a>


# Conclusiones <a name="conclusiones"></a>





