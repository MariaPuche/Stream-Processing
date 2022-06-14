# **NIFI** 

Su fuerte no son las ETL, permite de forma sencilla y gráficar plantear un pipeline a modo de diagrama arrastrando los procesador, con poco conocimiento sobre lenguajes de programación. 

Nifi permite diseñar tu propia procesador, no hace falta crearlos deesde las estructuras ya existentes.


0. Arranar apache nifi

        nifi-1.16.2/bin/nifi.sh start
        https://localhost:8443/nifi 

01. Crear dos directorios en nuestro $HOME desde el terminal

        mkdir nifisource
        mkdir nifitarget
        ls #Comprobar

**Ejemplo1**. Leer los datos que se encuentran en el fichero de nuestro sistema local y mover esos ficheros tal cual a otro directorio de nuestro sistema local.

    1.1 - Añadir un procesador GetFile para obtener los datos desde la carpeta fuente (desde la interfaz arrastrando). Configuramos el procesador creado, dejando  las propiedades por defecto y cambiamos:

        - El directorio de entrada: Properties --> Input Directory.        
            La ruta que vamos a poner se obtiene:
                cd nifisource
                pwd
            Ruta a introducir: /home/spark/nifisource

        - El nombre:  Setting --> Name: Leer Data desde Fuente      
    
    1.2 - Añadir un procesador putFile. Configuramos el procesador creado, dejando las propiedades por defecto y cambiamos:
    
        - El directorio de entrada:  Properties --> Directory
            La ruta que vamos a poner se obtiene:
                cd ..
                cd nifitarget
                pwd
            Ruta a introducir: /home/spark/nifitarget

        - El nombre: Setting --> Name: Volcar Data Destino

        - ¿Qué hacer cuando ocurre un error?: elationShips --> Failure (terminate) y success (terminate)

    1.3 - Conectar los dos procesadores

    1.5 - Arrancar el procesador de lectura de datos, pinchando en start.

    1.6 - Crear dos ficheros en nifisource

        cd nifisource
        ls 
        echo "Hola Mundo" > file1.txt
        echo "Hola Mundo2" > file2.txt

    1.7 - Ver la cola
        Pinchar sobre el conector y darle a list queue. Estos elementos van a desaparecer cuando arranquemos el otro procesador.

    1.8 - Arrancar el procesador Volcar Data Destino dandole  a start.  Vemos como desaparecen los elementos de la cola y cambia el tamaño de Read/Write

    1.9 - Detener los dos procesadores.

**Ejemplo2**. Leer los datos que se encuentran en el fichero de nuestro sistema local y mover esos ficheros tal cual a un directorio hdfs.

    2.1 - Copiamos el mismo procesador de origen que antes, puesto que se va a utilizar la misma carpeta de origen.

    2.2 - Creamos el procesador en el que se van a volcar los datos, del tipo PutHDFS. Cambiamos la ruta donde se van a volcar los datos:

            cd hadoop-2.10.2/etc/hadoop
            pwd

            Copiamos esta ruta en Properties -> /home/spark/hadoop-2.10.2/etc/hadoop/core-site.xml,/home/spark/hadoop-2.10.2/etc/hadoop/hdfs-site.xml

            Añadimos la ruta HDFS donde se quiere volcar el dato:
            Configure --> Properties --> Directory: /user/bigdata/nifi/hdfsexample1

    2.3 - Arrancar hdfs antes de darle a apply para comprobar que existe la ruta:

            start-dfs.sh
            hdfs dfs -ls /
            No existe la ruta, pero el sistema la creará sola.

    2.4 - Conectar los procesadores

    2.5 - Arrancar el procesador de lectura de datos, pinchando en start.

    2.6 - Crear ficheros en nifisource

        cd nifisource
        ls 
        echo "Hola Mundo" > file1.txt
        echo "Hola Mundo2" > file2.txt

    2.7 - Arrancar el procesador Volcar Data Destino dandole  a start.

    2.8 - Comprobar que se han volcado los ficheros en la ruta hdfs
        
            hdfs dfs -ls /user/bigdata/nifi/hdfsexample1

    2.9 - Detener los dos procesadores.

**Ejemplo3**. Volcar los datos en un direcotorio cuyo nombre muestre dias/horas/minuto cuando se realizó la transferencia.

    3.1 - Copiar el mismo procesador de origen que antes, puesto que se va a utilizar la misma carpeta de origen.

    3.2 - Crear el procesador en el que se van a volcar los datos, del tipo UpdateAtribute. Añadir una nueva propiedad, con el nombre ts, cuyo contenido es: ${now()}

    3.3 - Copiar el procesador putHDFS, cambiar la ruta donde se va a volcar el dato:

            Properties --> Directory --> /user/bigdata/nifi/hdfsexample2/${ts:format("yyyy-MM-dd")}/${ts:format("HHmm")}

    3.4 - Conectar los dos últimos procesadores creados

    3.5 - Analizar los 3 procesadores creados.

        Introducir los datos en nifisource
            echo "Hola Mundo3" > file3.txt
            echo "Hola Mundo1" > file1.txt
            echo "Hola Mundo2" > file2.txt

        Analizar lo creado

            hdfs dfs -ls /user/bigdata/nifi #Ver que se han creado las dos carpetas de los directorios con la fecha-

            hdfs dfs -ls -R /user/bigdata/nifi/hdfsexample2


