# VARIABLES, ALIAS, ÓRDENES DE BÚSQUEDA Y GUIONES

## 0 - ÓRDENES LINUX

| **alias** | **declare** | **egrep** | **env** | **export** |
| :---: | :---: | :---: | :---: | :---: |
| **expr** | **fgrep** | **find** | **grep** | **printenv** |
| **printf** | **set** | **unalias** | **unset** |

`alias` → Iguala una orden con argumentos a una cadena de caracteres
- `alias` → Muestra todos los alias del sistema
- `alias [string]=’[command]’` → Asigna la orden [command] a [string]

`declare` → Declara variables y funciones, dándoles valor
- `declare` → Muestra la lista de todas las variables y funciones del sistema
- `declare [var]=[value]` → Asigna el valor [value] a la variable [var]
- `declare -f` → Sólo muestra la lista de funciones del sistema
- `declare -F` → Sólo muestra la lista de nombres de funciones del sistema

`egrep` → Igual que `grep` pero permite más operadores en las expresiones regulares

`env` → Muestra las variables del sistema, igual que printenv

`export` → Exporta variables para utilizar tras cerrar la consola
- `export` → Muestra la lista de todas las variables exportadas
- `export [var]` → Exporta la variable [var]
- `export [var]=[value]` → Exporta la variable [var] declarada con valor [value]
- `export -n [var]` → Elimina la variable [var] de la lista de variables exportadas
- `export -p` → Muestra la lista de todas las variables exportadas

`expr` → 

`fgrep` → Igual que `grep` pero no acepta expresiones regulares

`find` → Busca archivos en una jerarquía de directorios
- `find [dir][args]` → Busca en [dir] y su descendencia archivos que cumplan con los argumentos [args]
- `find ! [args]` → Busca archivos que no cumplan con los argumentos [args]
- `find -atime [days]` → Busca archivos a los que que se accedió hace [days] días
- `find -name [name]` → Busca archivos con el nombre [name]
- `find -perm [permissions]` → Busca archivos con los permisos [permissions] declarados con la sintaxis de chmod
- `find -size [size]` → Busca archivos que ocupen [size] unidades de asignación en el disco
- `find -type [type]` → Busca archivos del tipo [type]
- `find [args] -exec [command] \;` → Ejecuta la orden [command] sobre los archivos que cumplan con los argumentos [arg]
- `find [args] -ok [command] \;` → Ejecuta la orden command sobre los archivos que cumplan con los argumentos [arg] confirmando en cada caso

`grep` → Busca cadenas de caracteres en archivos de un directorio
- `grep [args] [string] [dir]` → Busca la cadena [string] en el directorio [dir] según unos argumentos [args]
- `grep -c` → Imprime el número de coincidencias de la cadena en cada archivo
- `grep -e` → Protege las cadenas que empiezan por “-”
- `grep -i` → Ignora las distinciones entre mayúsculas y minúsculas
- `grep -l` → Busca la cadena en los nombre de los archivos, no en su contenido
- `grep -n` → Añade el número de línea en la salida de las coincidencias
- `grep -v` → Imprime las líneas que no contengan la cadena especificada
- `grep -x` → Localiza sólo líneas que coincidan totalmente

`printenv` → Muestra las variables del sistema, igual que env
- `printenv -0` → Termina cada línea con NUL en vez de un salto de línea

`printf` → Imprime un texto siguiendo un formato especificado
- `printf [format][args]` → Imprime el texto de [format] junto con los argumentos [args] especificados en el cuerpo de [format]

`set` → 

`unalias` → Elimina un alias de la lista alias
- `unalias [alias]` → Elimina el alias [alias] de la lista alias

`unset` → 

## 1 - VARIABLES

### 1.1 - TIPOS DE VARIABLES

Las **variables de entorno** son comunes a todos los shells y se escriben en mayúscula por convención. Pueden visualizarse con las órdenes `env` y `printenv`.

Las **variables locales** sólo son visibles en el shell donde se definen y se les da valor. Pueden verse mediante el comando `set`. Para consultar la lista de variables propias de bash se utiliza la orden `help variables`.

### 1.2 - CONTENIDO DE LAS VARIABLES

Las variables pueden contener cadenas de caracteres, números con los que operar, constantes no modificables  y vectores. Estos últimos son conjuntos de elementos accesibles mediante un índice, que suele ser un número entero tomando para la primera posición el valor `0`.

### 1.3 - CREACIÓN Y VISUALIZACIÓN DE VARIABLES

Para asignar valor a una variable basta con escribir el nombre de la variable, el símbolo `=` y el valor a asignar, que puede ser un valor por sí mismo u otra variable. Esta sintaxis no tiene espacios, ya que de otra forma bash lo interpreta como un comando y sus argumentos. El nombre de la variable puede contener dígitos pero no puede empezar por uno.

```shell
# Correcto
numero=0
# Incorrecto
numero = 0
```

Para crear vectores se declara su contenido entre paréntesis y se separan sus elementos mediante espacios. Para acceder a sus elementos se nombra el vector entre llaves y se declara la posición a acceder entre corchetes.

Las variables se visualizan mediante la orden `echo $variable` y pueden eliminarse mediante la orden `unset variable`.

```
$ numero=1
$ colores=(rojo amarillo verde azul)
$ echo numero
  1
$ echo $colores
  rojo
$ echo ${colores[0]}
  rojo
$ echo ${colores[3]}
  Azul
$ unset numero colores
$ echo numero colores
```

Existe una serie de variables especiales y otras que se crean al entrar el usuario.

| VARIABLE | DESCRIPCIÓN |
| :---: | --- |
| `$BASH` | Contiene la ruta de acceso completa usada para ejecutar la instancia actual de bash. |
| `$HOME` | Almacena el directorio raíz del usuario. Se puede emplear junto con la orden `cd` sin argumentos para ir al directorio raíz del usuario. |
| `$PATH` | Guarda el camino de búsqueda de las órdenes, este camino está formado por una lista de todos los directorios en los que queremos buscar una orden. |
| `$?` | Contiene el código de retorno de la última orden ejecutada, bien sea una instrucción o un script. |

Para declarar una variable con atributos específicos se utiliza la orden `declare -opts variable=valor`. Esto nos permite evitar errores de ejecución a la hora de asignar valores diferentes a su tipo a una variable, pero puede producir errores lógicos.

```
$ declare -i edad=22
$ echo $edad
  22
$ edad=hola
$ echo $edad
  0
```

### 1.4 - EXPORTAR VARIABLES

Las variables locales sólo mantienen su valor hasta que se acaba el script en el que están declaradas o se cierra el shell. Para poder utilizar los valores que tienen variables las variables de un entorno en otros se utiliza la orden `export variable` o `export variable=valor` (no escribimos `$variable`). Si las variables no se exportan vuelven a su valor original o se borran si no se hubieran declarado previamente al terminar la ejecución de la instancia de bash en la que se crearon o modificaron.

### 1.5 - SIGNIFICADO DE LAS COMILLAS EN LAS ÓRDENES

En bash se puede ejecutar una orden de forma que su salida se trate como el valor de una variable. Este proceso se llama sustitución de órdenes y su sintaxis permite declarar la sustitución encerrando la orden entre paréntesis o usando acentos graves sin el símbolo `$`.

```
$ echo “Los archivos de este directorio son $(ls -m).”
  Los archivos de este directorio son Practica1, Practica2.
$ echo “Los archivos de este directorio son `ls -m`.”
  Los archivos de este directorio son Practica1, Practica2.
```

### 1.6 - ASIGNACIÓN DE RESULTADOS DE ÓRDENES A VARIABLES

Para guardar en una variable la salida de una orden se declara el valor de la variable como dicha orden entre acentos graves. El valor de la variable no se actualiza cada vez que se llama a ella, sino que devuelve la salida de su orden al momento de declararla.

```
$ thisdir=`ls .`
$ echo $thisdir
  Ejer1 Ejer2 Ejer3 ejercicio1 ejercicio3
$ cd Ejer1
$ echo $thisdir
  Ejer1 Ejer2 Ejer3 ejercicio1 ejercicio3
```

Tras ejecutar una orden, la orden `echo $?` nos indica si la orden anterior se ha ejecutado correctamente o ha tenido un error. 

```
$ echo holi
  holi
$ echo $?
  0
$ cat holi
  cat: holi: No such file or directory
$ echo $?
  1
```

Para aplicar operaciones lógicas con las variables no basta con operar con ella. Hay que llamar a la orden expr para que lo haga, de otra forma concatena sus caracteres como si se tratase de un string.

```
$ numero=1
$ numero=$numero+1
$ echo $numero
  1+1
$ numero=1
$ numero=`expr $numero + 1`
$ echo $numero
  2
```

Sin embargo, esto no ocurre si declaramos las variables con tipos de datos específicos desde el primer momento.

```
$ declare -i numero=1
$ echo $numero
  1
$ numero=$numero+1
$ echo $numero
  2
```

## 2 - LA ORDEN EMPOTRADA PRINTF

###### [Documentación extendida](http://wiki.bash-hackers.org/commands/builtin/printf)

Como la orden `echo` puede comportarse de formas diferentes dependiendo del sistema Unix, es recomendable utilizar la orden empotrada (*built-in*) `printf`, que imprime un mensaje utilizando un formato que se le especifica.

El formato de printf tiene tres tipos de objetos:
- **Texto plano:** Se imprime tal cual en la salida
- **Secuencias de caracteres de escape:** Se convierten y copian en la salida
  - `\b` → Espacio atrás
  - `\n` → Nueva línea
  - `\t` → Tabulador
  - `\’` → Carácter comilla simple
  - `\\` → Barra invertida
  - `\NNN` → Número en octal (hasta 3 dígitos) que representa un carácter ASCII de 8 bits
- **Especificaciones de formato:** Se aplican a cada uno de los argumentos
  - `%d` → Un número con signo
  - `%f` → Un número con coma flotante sin notación exponencial
  - `%q` → Muestra una cadena entrecomillada, añade caracteres de escape
  - `%s` → Interpreta un argumento como una cadena sin entrecomillar
  - `%x` → Muestra un número en hexadecimal
  - `%o` → Muestra un número en octal

Es muy importante comprender que el formato y los argumentos de `printf` no son independientes del otro ni realizan acciones distintas. La sección de formato admite texto plano al igual que especificaciones sobre el formato de dicho texto o de los argumentos a escribir. El parser toma el texto de la sección de formato (si lo hubiese) y lo va imprimiendo hasta que llega a un código (como `%d`). Para imprimir el valor de dicho código busca su argumento asociado en orden (el primer argumento para el primer código, el segundo para el segundo…) y lo imprime. La orden acaba cuando llega al final de la cadena.

```
$ printf "Ayer me comí %d perritos y %d camperos.\n" 5 2
  Ayer me comí 5 perritos y 2 camperos.
```

A la hora de imprimir elementos se pueden marginar añadiendo números entre el `%` y el carácter que designa el código de formato. Esto resulta muy útil para mostrar elementos en columnas.

### 2.1 - TABULACIÓN POR LA IZQUIERDA

La tabulación por la izquierda toma como referencia el último carácter de la cadena y la separa del margen izquierdo ese número de columnas o, como mínimo, su longitud.

```
$ printf "%5d\n" 45
  ···45
$ printf "%5d\n" 123456
  123456
```

Los tres puntos antes del número no se imprimen, están escritos posteriormente para hacer más fácil la visualización del número de espacios que se imprimen. En cadenas más largas que el espaciado especificado no se cortan los caracteres, sino que se alinean directamente a la izquierda.

También se pueden concatenar tabulaciones sobre elementos anteriores, que se añaden a los espacios escritos naturalmente en el formato. De nuevo, los puntos están añadidos para ayudar a la lectura.

```
$ printf "%5d%7d\n" 45 567
  ···45····567
$ printf "%5d %7d\n" 45 567
  ···45·····567
```
### 2.2 - TABULACIÓN POR LA DERECHA

Al indicar la tabulación como un número negativo los argumentos se imprimen naturalmente como una palabra (tras un espacio si fuera necesario) y dejan espacio a su derecha para la siguiente porción de texto. La tabulación por la derecha empieza a contar desde el último carácter de su argumento. De nuevo, los puntos están añadidos para ayudar a la lectura.

```
$ printf "Holi, tengo %-5d años.\n" 22
  Holi, tengo 22····años.
$ printf "%-5d %-5d %3d\n" 12 12 12
  12····12·····12
$ printf "%-5d %-5d %4d\n" 12 12 12
  12····12······12
```

Al unir tabulaciones por la izquierda y por la derecha se debe tener en cuenta qué carácter se toma como referencia para iniciar la tabulación y que los espacios nunca se van a superponer.

### 2.3 - TABULACIÓN DECIMAL

Al indicar la tabulación de números decimales se utiliza un número decimal en el formato. La parte entera de dicho número indica las columnas de tabulación por la izquierda (o por la derecha si fuese negativo) y la parte decimal indica el número mínimo de dígitos decimales con los que se representa el argumento, que influye en la tabulación.

Es muy importante distinguir entre el decimal de formato, que siempre se escribe con el separador `.`, y el decimal argumento, que se escribe con `.` o `,` dependiendo del paquete de lenguaje del sistema. De nuevo, los puntos están añadidos para ayudar a la lectura.

```
$ printf "%10.2f\n" 15.4
  ·····15.40
$ printf "%10.3f\n" 15.4
  ····15.400
$ printf "%-10.4f\n" 15.4
  15.4000   
$ printf "%-10.4f %d\n" 15.4 11
  15.4000····11
```

### 2.4 - CONVERSIÓN DE UNIDADES

A la hora de imprimir unidades como argumentos se debe indicar su base en el formato, pero no tienen que coincidir necesariamente. Al introducir una unidad de una base como argumento y declarar otra base en el formato, printf convierte la unidad del argumento a la especificada en el formato. Los números decimales se escriben normalmente, los octales van precedidos de `0` y los hexadecimales, de `0x`.

```
  ¿Por qué los informáticos confunden Halloween y Navidad?
$ printf "Porque OCT %o = DEC %d.\n" 0x19 0x19
  Porque OCT 31 = DEC 25.
```

### 2.5 - INTERPRETACIÓN DE CADENAS DE CARACTERES

En los argumentos se pueden introducir cadenas de caracteres definidas entre comillas simples. Mientras que el comando `%s` toma los caracteres de la cadena y los imprime, `%q` les añade los caracteres de escape necesarios para que puedan volver a utilizarse como una cadena de entrada, añadiendo los caracteres de escape necesarios.

```
$ printf "Estudio %s en la %q.\n" 'Ingeniería Informática' 'Universidad de Granada'
  Estudio Ingeniería Informática en la Universidad\ de\ Granada.
```

### 2.6 - IMPRESIÓN DE VARIABLES

Por último, se pueden imprimir variables argumentos en forma de variables indicando su tipo de dato (o el tipo de dato al que se quiere convertir) en el formato.

```
$ nota=10
$ printf "He sacado un %d en %s y en %s.\n" $nota FP FS
  He sacado un 10 en FP y FS.
```

No es necesario entrecomillar las cadenas si están formadas por una sola palabra.

## 3 - ALIAS

Los alias son cadenas a las que se le asocian órdenes para que la consola las interprete al introducirlas como órdenes propias. Son muy útiles para resumir órdenes (u órdenes encauzadas o concatenadas) muy largas en una cadena más simple o asociarles sinónimos conocidos de otros sistemas.

```
$ alias longls='ls -la'
$ longls
  total 28
  drwxr-xr-x 5 groctel groctel 4096 oct  8 16:53 .
  drwxr-xr-x 3 groctel groctel 4096 oct  8 16:52 ..
  drwxr-xr-x 2 groctel groctel 4096 oct  6 21:30 Ejer1
  drwxr-xr-x 3 groctel groctel 4096 oct  6 21:29 Ejer2
  drwxr-xr-x 2 groctel groctel 4096 oct  8 16:50 Ejer3
```

Si un alias estuviera ocupando el lugar de una orden y se quisiera ejecutar la orden original, basta con escribir “\” antes de la orden para no buscarla en los alias.

```
$ alias ls='ls -l'
$ ls
  total 20
  drwxr-xr-x 2 groctel groctel 4096 oct  6 21:30 Ejer1
  drwxr-xr-x 3 groctel groctel 4096 oct  6 21:29 Ejer2
  drwxr-xr-x 2 groctel groctel 4096 oct  8 16:50 Ejer3
  -rwxr-xr-x 1 groctel groctel  738 oct  8 16:53 ejercicio1
  -rwxr-xr-x 1 groctel groctel  169 oct  8 16:54 ejercicio3
$ \ls
  Ejer1  Ejer2  Ejer3  ejercicio1  ejercicio3
```

## 4 - ÓRDENES DE BÚSQUEDA

### 4.1 - ORDEN FIND

Para buscar archivos en directorios y toda su descendencia, `find` toma unas opciones de entrada que definen el tipo de archivo que se quiere buscar y compara cada archivo que encuentra con dichos argumentos, imprimiendo sólo los que coincidan.

Si una de las opciones va acompañada de un argumento de tipo numérico, como es el caso de `-atime` o `-size`, se le puede añadir un `+` o un `-` para indicar que se buscan valores superiores o inferiores al especificado.

La búsqueda por tipos sólo acepta argumentos de un carácter:
- `d` → Directorio
- `f` → Archivo normal
- `l` → Enlace simbólico

Al tratarse de un operador booleano `(if(args){print(file)})`, se pueden separar dos opciones mediante `-o`, que actúa como el operador `||`. El operador `&&` se sobreentiende al escribir dos opciones distintas en la misma línea.

Mediante las opciones `-exec` o `-ok` se pueden asignar órdenes que se ejecuten sobre los archivos que cumplan con los argumentos anteriores. Para designar todos los archivos que cumplan con dicho argumento se utiliza el metacarácter `{}`. Es muy importante cerrar estas órdenes con `\;`.

```
$ find . -size -2 -exec cat {} \;
  groctel  :0           2018-10-08 15:47 (:0)
  arch100.txt
  Ej31
  Ej32
  ejer3arch.txt
  ejer3filetags.txt
  ejercicio3
  filetags.txt
  practFS.ext
  readme3.pdf
  u18  73 738 ejercicio1
```

### 4.2 - ÓRDENES GREP, EGREP Y FGREP

La orden `grep` y sus derivadas permiten buscar cadenas de caracteres dentro de archivos. Al contrario que `find`, sólo pueden buscar en un directorio y devuelven todas las cadenas que coinciden con la búsqueda. Sus diferentes argumentos se utilizan para para tratar los dados de las búsquedas

```
$ grep -n Ejer2 *
  Ej31:2:Ejer2
  Ej32:1:Ejer2
  Ej32:4:Ejer2
$ grep -c Ejer2 *
  arch100.txt:0
  Ej31:1
  Ej32:2
  ejer3arch.txt:0
```

## 5 - SCRIPTS

Los guiones del shell (de ahora en adelante **scripts**) son archivos de texto que contienen secuencias de órdenes del shell y del sistema operativo. Cuando un shell ejecuta una orden, sigue las instrucciones de su script. Los scripts se escriben en archivos de texto plano y se guardan con extensión .sh o sin ella. En Atom, su lenguaje asignado es *Shell Script*.

Un ejemplo muy sencillo de script es un programa que imprima `Hola mundo!`. Para lanzar un script se utiliza la orden `bash`, que no se encuentra documentada más arriba porque sólo la vamos a usar para este propósito:

###### holamundo
```shell
echo "Hola mundo!"
```

```
$ bash holamundo
  Hola mundo!
```

A la hora de trabajar con variables, es importante tener en cuenta los diferentes tipos de acotación. La **acotación débil** trata todo lo que hay en su interior como una cadena excepto por los caracteres `!`, `$`, `\` y ``` ``, que se tratan como parte de los argumentos. La **acotación fuerte** trata todo lo que hay en su interior como una cadena y sólo toma como argumento el carácter `!`. Esta teoría se ve reflejada en la sección 1.5 de este tema.

###### acotados
```shell
var=ordenador

printf "Me acabo de comprar un $var\n"
printf ‘Me acabo de comprar un $var\n’
printf "Me acabo de comprar un \$var\n"
```

```
$ bash acotados
  Me acabo de comprar un ordenador
  Me acabo de comprar un $var
  Me acabo de comprar un $var
```

La segunda línea devuelve un error lógico porque la acotación es fuerte y la tercera, porque aunque la acotación sea grave, el carácter `\` anula la función de `$` como argumento.

### 5.1 - ELIMINACIÓN DE LA ORDEN BASH

Para no tener que escribir `bash [script]` para invocar cada ejecución se añade en la primera línea del script una línea que invoque a la localización en el sistema de archivos de la consola en la que se va a ejecutar precedida de `#!`. En este caso, `/bin/bash`. Para ejecutar un script de esta forma, se debe incorporar el directorio de trabajo a la variable `$PATH` o comenzar la orden con `./`.

Es muy importante tener en cuenta que, al no estar usando la orden `bash`, tenemos que darle manualmente permisos de ejecución al script.

###### home
```shell
#!/bin/bash

printf "El directorio $HOME contiene los siguientes archivos:\n"

ls $HOME
```

```
$ chmod +x home
$ ./home
  El directorio /home/groctel contiene los siguientes archivos:
  Desktop      Pictures
  Documents    Public
  Downloads    Templates
  Music        Videos
```

### 5.2 - VARIABLES DEFINIDAS PARA ARGUMENTOS

Al ejecutar un script se le pueden incorporar argumentos como si de una orden normal se tratase. Dentro del script se pueden buscar dichos argumentos usando estos códigos:

| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;CÓDIGO&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | DESCRIPCIÓN |
| :---: | --- |
| `$0` | Nombre del script ejecutado. Sólo se emplea dentro del propio script. |
| `$1 - $9` | Argumentos incorporados a la orden de ejecución en orden. Es muy importante definir la sintaxis de ejecución para que funcionen correctamente. |
| `${n}` | Argumentos incorporados si el orden de argumentos `n>9`.     |
| `$*` | Contiene todos los argumentos incorporados. Acotado débilmente los muestra como una cadena. |
| `$@` | Contiene todo los argumentos incorporados. Acotado débilmente los muestra como una cadena. |
| `$#` | Contiene el número de argumentos incorporados al script.     |
| `${arg:-[value]}` | Si el argumento tiene valor no nulo continúa con él. En caso contrario, se le asigna [value]. |
| `${arg:?[value]}` | Si el argumento tiene valor no nulo se le asigna [value]. En caso contrario se imprime [value] y se cierra el script. Si se omite [value] se imprime un mensaje indicando que el argumento es no nulo o no está asignado. |

###### shellbackup
```shell
#!/bin/bash

printf "Haciendo copia de seguridad de $1 en $2...\n"

mkdir $2/shellbackup/
cp -r $1/* $2/shellbackup/

printf "Copia realizada con éxito.\n"
```

```
$ chmod +x shellbackup
$ ./shellbackup . ~/Desktop
  Haciendo copia de seguridad de . en /home/groctel/Desktop...
  Copia realizada con éxito.
```

### 5.3 - NORMAS DE ESTILO

###### [Estándares de estilo de GNU](http://www.gnu.org/prep/standards/)
###### [Estándares de estulo de Bash](https://lug.fh-swf.de/vim/vim-bash/StyleGuideShell.en.pdf)

Los comentarios en un script se inicializan con #. Es de buena costumbre indicar al inicio del script dados sobre su autor, versión y descripción.

###### shellbackup
```shell
#!/bin/bash

# Titulo: shellbackup
# Fecha: 2018/10/12
# Autor: Groctel
# Versión: 1.0
# Descripción: Ejemplo de un script de backup sencillo
# Opciones: Ninguna
# Uso: Demostración académina

printf "Haciendo copia de seguridad de $1 en $2...\n"

mkdir $2/shellbackup/ #Por si no existe el directorio
cp -r $1/* $2/shellbackup/

printf "Copia realizada con éxito.\n"
```

## 6 - EJERCICIOS

### EJERCICIO 1

Escriba, al menos, cinco variables de entorno junto con el valor que tienen.

### EJERCICIO 2

Ejecute las órdenes del cuadro e indique qué ocurre y cómo puede resolver la situación para que la variable NOMBRE se reconozca en el shell hijo.

```
$ NOMBRE=FS
$ echo $NOMBRE
$ bash
$ echo $NOMBRE
```

### EJERCICIO 3

Compruebe qué ocurre estas expresiones si se quitan las comillas dobles del final y se ponen después de los dos puntos. ¿Qué sucede si se sustituyen las comillas dobles por comillas simples?

```
$ echo “Los archivos que hay en el directorio son: $(ls -l)”
$ echo “Los archivos que hay en el directorio son: `ls -l`”
```

### EJERCICIO 4

Construya un guión que acepte como argumento una cadena de texto (por ejemplo, su nombre) y que visualice en pantalla la frase Hola y el nombre dado como argumento.

### EJERCICIO 5

Varíe el guión anterior para que admita una lista de nombres.

### EJERCICIO 6

Cree tres variables llamadas `VAR1`, `VAR2` y `VAR3` con los valores `hola`, `adios` y `14` respectivamente.

Imprima los valores de las tres variables en tres columnas con 15 caracteres de ancho.

¿Son variables locales o globales?

Borre la variable `VAR2`.

Abra otra ventana de tipo terminal, ¿puede visualizar las dos variables restantes?

Cree una variable de tipo vector con los valores iniciales de las tres variables.

Muestre el segundo elemento del vector creado en el apartado e.

### EJERCICIO 7

Cree un alias que se llame `ne` (nombrado así para indicar el número de elementos) y que devuelva el número de archivos que existen en el directorio actual. ¿Qué cambiaría si queremos que haga lo mismo pero en el directorio home correspondiente al usuario que lo ejecuta?

### EJERCICIO 8

Indique la línea de orden necesaria para buscar todos los archivos a partir del directorio `home` de usuario (`$HOME`) que tengan un tamaño menor de un bloque. ¿Cómo la modificaría para que además imprima el resultado en un archivo que se cree dentro del directorio donde nos encontremos y que se llame `archivosP`?

### EJERCICIO 9

Indique cómo buscaría todos aquellos archivos del directorio actual que contengan la palabra `ejemplo`.

### EJERCICIO 10 

Indique cómo buscaría si un usuario dispone de una cuenta en el sistema.

### EJERCICIO 11

Indique cómo contabilizar el número de ficheros de la propia cuenta de usuario que no tengan permiso de lectura para el resto de usuarios.

### EJERCICIO 12

Modifique el ejercicio 7 de forma que, en vez de un alias, sea un guión llamado `numE` el que devuelva el número de archivos que existen en el directorio que se le pase como argumento.

## 7 - SOLUCIONES

### EJERCICIO 1

```
LESSOPEN=| /usr/bin/lesspipe %s
GTK_IM_MODULE=ibus
LC_TIME=en_GB.UTF-8
_=/usr/bin/env
OLDPWD=/home/groctel
```

### EJERCICIO 2

```
$ NOMBRE='FS'
$ echo $NOMBRE
  FS
$ bash
$ echo $NOMBRE
  
$ exit
  exit
$ export NOMBRE='FS'
$ bash
$ echo $NOMBRE
  FS
```

Para que la variable pueda ser accedida desde shells descendientes usamos la orden export en el momento de la asignación preferentemente.

### EJERCICIO 3

```
$ echo "Los archivos que hay en el directorio son: $(ls -l)"
  Los archivos que hay en el directorio son: total 0
  -rw-r--r-- 1 groctel groctel 0 Oct  6 21:25 blue.me
  -rw-r--r-- 1 groctel groctel 0 Oct  6 21:25 ejerllsol.txt
  -rw-r--r-- 1 groctel groctel 0 Oct  6 21:25 robet202.me
$ echo "Los archivos que hay en el directorio son: `ls -l`"
  Los archivos que hay en el directorio son: total 0
  -rw-r--r-- 1 groctel groctel 0 Oct  6 21:25 blue.me
  -rw-r--r-- 1 groctel groctel 0 Oct  6 21:25 ejerllsol.txt
  -rw-r--r-- 1 groctel groctel 0 Oct  6 21:25 robet202.me
$ echo "Los archivos que hay en el directorio son:" $(ls -l)
  Los archivos que hay en el directorio son: total 0 -rw-r--r-- 1 groctel
  groctel 0 Oct 6 21:25 blue.me -rw-r--r-- 1 groctel groctel 0 Oct 6
  21:25 ejerllsol.txt -rw-r--r-- 1 groctel groctel 0 Oct 6 21:25 robet202.me
$ echo "Los archivos que hay en el directorio son:" `ls -l`
  Los archivos que hay en el directorio son: total 0 -rw-r--r-- 1 groctel
  groctel 0 Oct 6 21:25 blue.me -rw-r--r-- 1 groctel groctel 0 Oct 6
  21:25 ejerllsol.txt -rw-r--r-- 1 groctel groctel 0 Oct 6 21:25 robet202.me
$ echo "Los archivos que hay en el directorio son:" ls -l
  Los archivos que hay en el directorio son: ls -l
```

La orden `echo` imprime todo en una línea. Para imprimir una orden con su formato debe estar entre acotación débil. Si se le da la orden sin indicarla con acotación débil o como una variable definida por una orden, imprime la propia orden, no su resultado. 

### EJERCICIO 4

###### hola
```shell
#!/bin/bash

printf "Hola $1.\n"
```

```
$ ./hola Taxo
  Hola Taxo.
```

### EJERCICIO 5

###### hola
```shell
#!/bin/bash

printf "Hola $*.\n"
```

```
$ ./hola Taxo Inés Leire Marta Clara
  Hola Taxo Inés Leire Marta Clara.
```
### EJERCICIO 6

```
$ VAR1="hola" VAR2="adios" VAR3=14
$ printf "%14q %14q %14d" $VAR1 $VAR2 $VAR3
  ···········hola··········adios·············14
```

Son variables locales

```
$ unset VAR2
$ echo $VAR2
  
$ bash
$$ echo $VAR1 $VAR3
   
$$ exit
$ VECTOR=(hola adios 14)
$ echo ${VECTOR[1]}
  adios
```

### EJERCICIO 7

```
$ alias ne=’ls -l | head -1’
$ ne
  total 24
$ alias ne=’ls ~ -l | head -1’
$ ne
  total 13
```

### EJERCICIO 8

```
$ find ~ -size -1
  Sale un tochazo con todos los archivos
$ find ~ -size -1 > archivosP
$ cat archivosP
  Sale el mismo tochazo con todos los archivos
```

### EJERCICIO 9

```
$ grep ejemplo ./*
  grep: ./Desktop: Is a directory
  grep: ./Documents: Is a directory
  grep: ./Downloads: Is a directory
  ./examples.desktop:Comment[es]=Contenido de ejemplo para Ubuntu
  grep: ./Music: Is a directory
  grep: ./Pictures: Is a directory
  grep: ./Public: Is a directory
  grep: ./snap: Is a directory
  grep: ./Templates: Is a directory
  grep: ./Videos: Is a directory
```

Importante la diferencia entre contener una cadena o contener una cadena en el nombre del archivo.

### EJERCICIO 10

```
$ cat /etc/passwd | grep [usuario]
```

Existe la orden `who` y también podemos buscar los directorios que haya en `/home`, pero si hay usuarios que no tengan carpetas o no estén formalmente identificados, sólo se van a encontrar en `/etc/passwd`.

### EJERCICIO 11

```
$ find ~ ! -perm -o+r | wc -l
  15357
```

### EJERCICIO 12

###### numE
```shell
#!/bin/bash

ls $1 -l | head -1
```

```
$ ./numE .
  total 40
```
