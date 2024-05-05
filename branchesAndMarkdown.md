# Ramas Git

###  Ramificaciones en Git

En primera instancia Git ofrece una ventaja a un desarrollador al administrar ramas. Utilizando conceptos muy intuitivos, Git no funciona de manera incremental, es decir realizando cambios que pueden rehacerse o deshacerce, de hecho captura instantaneas con una confirmacion asignada (commits) de todos los archivos que se encuentren en ese momento.

### Ramas

Al momento de inicializar un repositorio en Git, es creada una rama por defecto cuyos nombres son main o master, que funge como una rama principal.

A medida que se trabaja sobre el proyecto se realizan los commits creando una nueva captura con todos los cambios realizados:

```console
  $ git add README test.rb LICENSE
  $ git commit -m 'primer commit de mi proyecto'
```

Sobre una rama se encuentra el marcador HEAD y si este no se encuentra sobre la rama en la que estemos trabajando, esta no podra crear sus propias instantaneas, por el momento creamos una nueva rama "problemas" que va a compartir confirmacion con la rama main.

```console
  $ git branch problemas

```


 Usando el comando *git log --oneline --decorate* se puede verificar que:

```console
  $ git log --oneline --decorate
  f30ab (HEAD, main, problemas) add feature #32 - ability to add new
  34ac2 fixed bug #1328 - stack overflow under certain conditions
  98ca9 primer commit de mi proyecto
```

 Un ejemplo practico es cuando se desea trabajar de manera personal en una parte de un proyecto, no se quiere tocar la rama principal asi que usamos la rama problemas y se van realizando los respectivos cambios:
 
```console
      $ git checkout problemas
```
 
  Una vez verificados todos los cambios se procede a realizar un commit y el marcador HEAD se postra sobre el mismo otorgando una instantanea propia a la rama problemas:

```console
  $ vim test.rb
  $ git commit -a -m 'algunos cambios'
  ```

En la vida real se deben solucionar inconvenientes, y supongamos que debemos corregir unos archivos sobre la rama main en el cual estamos . No queremos usar nuestra rama de problemas asi que regresamos a la rama main con un checkout y, en efecto, los archivos se revierten a la ultima instantanea creada por la rama main. 

```console
  $ git checkout main
```

Hacemos unos cambios y los confirmamos:

```console
  $ vim test.rb
  $ git commit -a -m 'hice unos cambios'
```

 Ahora, las ramas "problemas" y "main" se denominan ramas divergentes, porque cada una cuenta con su propia instantanea y los cambios que se realicen en una no afectan en otra:

 ```console
   $ git log --oneline --decorate --graph --all
  * c2b9e (HEAD, main) hice otros cambios
  | * 87ab2 (problemas) algunos cambios
  |/
  * f30ab add feature #32 - ability to add new formats to the
  * 34ac2 fixed bug #1328 - stack overflow under certain conditions
  * 98ca9 primer commit de mi proyecto

 ```

---

### Fusion

Supongamos que nos llaman para realizar una correccion de ultimo momento en nuestro proyecto, nosotros creamos una nueva rama para trabajar ese conflicto, verificamos y testeamos nuestros cambios y realizamos una fusion (merge) para enviar a la produccion principal (push).

Empezamos creando una nueva rama:

```consola
 $ git checkout -b prb53
  Switched to a new branch "prb53"
```
Realizamos los cambios y un respectivo commit:

```console
  $ vim index.html
  $ git commit -a -m 'se ha agregado un pie de pagina [Problema 53]'
```

Pero nos vuelven a llamar para resolver otro problema, para esto, no queremos tocar nuestro progreso realizado en prb53, entonces regresaremos al main a crear otra rama:

```console
  $ git checkout main
  Switched to branch 'main'
  ```
  Creamos la rama hotfix y una vez realizados los respectivos cambios resolvemos el problema:

```console
    $ git checkout -b hotfix
  Switched to a new branch 'hotfix'
  $ vim index.html
  $ git commit -a -m 'direccion de correo arreglada'
  [hotfix 1fb7853] direccion de correo arreglada
   1 file changed, 2 insertions(+)
```

Ahora que ya tenemos la confirmacion respectiva de hotfix y hemos testeado nuestros cambios procedemos a fusionar:

```console
 $ git checkout main
  $ git merge hotfix
  Updating f42c576..3a0874c
  Fast-forward
   index.html | 2 ++
   1 file changed, 2 insertions(+)
```

Ahora la rama main ocupa la instantanea de la rama hotfix con la misma confirmacion y los cambios realizados en hotfix ahora se encuentran en la rama main.

Borramos la rama hotfix porque no la necesitaremos mas:

```console
  $ git branch -d hotfix
  Deleted branch hotfix (3a0874c).
  ```

  Y con un checkout regresamos a trabajar en el problema #53.

----

  ### Administrar proyectos en un repositorio remoto.

Creando una cuenta en Github,  y conociendo la url que nos lleva hacia un repositorio remoto:

```console
 $ git remote add github https://server/namespace/project.git
 ```
 Y si no tenemos una copia local del mismo: 

 ```console
   $ git clone https://server/namespace/project.git
   ```
   Es recomendable usar la interfaz web para descubrir cosas interesantes sobre la administracion y personalizacion de tu repositorio,

   ---
   ### Contribuir a un proyecto

   Si trabajamos en un equipo de pocos colaboradores, tenemos suerte de encontrarnos en la configuracion mas probable.

   Tenemos a Jaime que clona el repositorio de manera local para realizar su cambio:

   ```console
        # Jaime's Machine
  $ git clone jaime@githost:simplegit.git
  Initialized empty Git repository in /home/jaime/simplegit/.git/
  ...
  $ cd simplegit/
  $ vim lib/simplegit.rb
  $ git commit -am 'removed invalid default value'
  [master 738ee87] removed invalid default value
   1 files changed, 1 insertions(+), 1 deletions(-)
```

Mafer hace lo mismo:

```console
    # Mafer's Machine
  $ git clone mafer@githost:simplegit.git
  Initialized empty Git repository in /home/mafer/simplegit/.git/
  ...
  $ cd simplegit/
  $ vim TODO
  $ git commit -am 'add reset task'
  [master fbff5bc] add reset task
   1 files changed, 1 insertions(+), 0 deletions(-)
```

Mafer decide subir su trabajo:

```console
    # Mafer's Machine
  $ git push origin master
  ...
  To mafer@githost:simplegit.git
     1edee6b..fbff5bc  master -> master
```

Jaime tambien intenta enviar su trabajo al servidor:

```console
    # Jaime's Machine
  $ git push origin master
  To jaime@githost:simplegit.git
   ! [rejected]        master -> master (non-fast forward)
  error: failed to push some refs to 'jaime@githost:simplegit.git'

```
Jaime no pudo subir su trabajo porque Mafer habia subido parte del suyo en la misma rama, en este caso Git se dio cuenta de la diferencia de los archivos.

Jaime debe fusionar sus archivos con los de Mafer:

```console
  $ git fetch origin
  ...
  From jaime@githost:simplegit
   + 049d078...fbff5bc master     -> origin/master

```
Asi es como Jaime tiene una idea de lo que ha hecho Mafer, solo le basta fusionar su trabajo con el de ella:

```console 
  $ git merge origin/master
  Merge made by recursive.
   TODO |    1 +
   1 files changed, 1 insertions(+), 0 deletions(-)
```

Al revisar que la combinacion de su trabajo con el de Mafer no tiene ningun problema y haber testeado correctamente, Jaime puede subir su trabajo al servidor:

```console
  $ git push origin master
  ...
  To jaime@githost:simplegit.git
     fbff5bc..72bbc59  master -> master
```