## El comando para generar un par de claves tiene estas partes:

    # Generar el par de claves criptográficas
    ssh-keygen
        -t rsa  # algoritmo de cifrado
        -b 4096 # tamaño del número aleatorio
        -C "`whoami`@`hostname`" # comentario para identificar la clave
        -f ~/.ssh/inmegen # el archivo donde se va a guardar la llave

## `inmegen.pub` permite cifrar la información para que sólo pueda leerla quien tenga el archivo `inmegen`

<div class="columns">

<div class="column" width="50%">

![inmegen.pub](../imagenes/candado.png)

</div>

<div class="column" width="50%">

![inmegen](../imagenes/llave.png)

</div>

</div>

## `ssh-copy-id` copia una clave pública al servidor remoto en `$HOME/.ssh/authorized_keys`

    # Copiar la clave al servidor
    ssh-copy-id -i ~/.ssh/inmegen.pub castillo

## `ssh-copy-id` además crea el directorio `$HOME/.ssh` y configura los permisos correctos

    # Copiar la clave al servidor
    ssh-copy-id -i ~/.ssh/inmegen.pub castillo

## La alternativa a `ssh-copy-id` sería ejecutar los siguientes comandos

    # Copiar la clave al servidor
    ssh-copy-id -i ~/.ssh/inmegen.pub castillo
    
    # Copiar la clave al servidor
    ssh castillo
    mkdir .ssh
    chmod 700 .ssh
    exit
    scp ~/.ssh/inmegen.pub castillo:.ssh
    ssh castillo
    cat .ssh/inmegen.pub >> .ssh/authorized_keys
    chmod 600 .ssh/authorized_keys
    rm .ssh/inmegen.pub

## Una cosa que no verifica `ssh-copy-id` es que `/home` tenga permisos correctos

    # Esto evita que podamos acceder sin contraseña al servidor
    #
    # ¿Por qué?
    #
    chmod g+w $HOME

## Probemos entrar en el servidor

    ssh castillo

## Para ejecutar procesos que toman tiempo en un servidor remoto se recomienda usar `byobu`

    # sin byobu el proceso termina
    ssh castillo
    yes '¡Sigo ejecutándo!'
    # salirse de la terminal
    ssh castillo

## `byobu` mantiene los procesos ejecutándose en un servidor remoto en el evento de la desconexión

    # sin byobu el proceso termina
    ssh castillo
    byobu
    yes '¡Sigo ejecutándo!'
    # salirse de la terminal
    ssh -t castillo byobu

## También se puede compartir un proceso entre varias personas usando `byobu`

    # primera persona
    ssh castillo
    byobu -S /labs/taller/byobu-compartido
    chmod g+w /labs/taller/byobu-compartido
    
    # todos los demás
    ssh -t byobu -S /labs/taller/byobu-compartido attach

## Podemos acceder a los archivos remotos en un servido desde nuestra computadora con `sshfs`

    # Necesitamos un directorio vacío
    mkdir -p ~/ssh/castillo
    
    # Conectamos el servidor remoto en nuestro directorio vacío
    sshfs castillo: ~/ssh/castillo \
        -o local -o volname=castillo # sólo las mac necesitan esta parte
    
    # Desconectamos el servidor remoto
    fusermount -u ~/ssh/castillo # linux
    umount ~/ssh/castillo # mac

## Para ver los archivos directamente, las personas con windows necesitan un gestor de archivos

    # Sólo ubuntu on windows
    sudo apt install pcmanfm

## En windows, los paquetes están en `%userprofile%\AppData\Local\Packages`

![](https://www.howtogeek.com/wp-content/uploads/2018/03/ximg_5a99b5465e474.png.pagespeed.gp+jp+jw+pj+ws+js+rj+rp+rw+ri+cp+md.ic.2mRvtXr_3D.png)  

# Para transferir datos con el servidor podemos usar `rsync`, `wget`, `scp` y `sshfs` (entre otros)

## Podemos descargar referencias con `wget -x` y así conservar la URL completa

Ejercicio: descargar y comprobar que la información es
    correcta

    http://chiulab.ucsf.edu/SURPI/databases/28s_rRNA_gene_NOT_partial_18s_spacer_5.8s.fa.gz
    http://chiulab.ucsf.edu/SURPI/databases/28s_rRNA_gene_NOT_partial_18s_spacer_5.8s.fa.gz.md5

## Ejercicio 2: descargar y comprobar que la información es correcta

    ftp://mirbase.org/pub/mirbase/21/database_files/confidence_score.txt.gz

## Ejercicio 3: descargar y comprobar que la información es correcta

    ftp://ussd-ftp.illumina.com/2017-1.0/hg38/small_variants/ConfidentRegions.bed.gz.tbi
    ftp://ussd-ftp.illumina.com/2017-1.0/hg38/small_variants/md5sum.txt

  - usuario: platgene\_ro
  - contraseña
vacía

# Una vez comprobada la descarga ¿Qué permisos deben tener?

## Si tienes permiso de administrador puedes evitar que borren las referencias con `chattr +i` o `chmod +t`

    chattr +i # hace los archivos inmutables
    chmod +t # sólo el dueño de los archivos puede borrarlos
