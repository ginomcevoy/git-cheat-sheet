# Comenzar a trabajar en el branch develop

## Clonar repositorio

Asumimos que el branch develop ya existe en el repositorio remoto:

```ssh
git clone <url>
git config user.name "Nombre Apellido"
git config user.email my.email@example.com
git checkout develop
```

## Trabajando el workspace (versión local)

Si Alice crea un archivo nuevo (archivo-nuevo.txt), este archivo se encuentra en el estado 'untracked' (no existe en el repositorio local).

```
$ git status
On branch develop 
Your branch is up to date with 'origin/develop'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	archivo-nuevo.txt

nothing added to commit but untracked files present (use "git add" to track)
```

Alice ahora edita un archivo existente (Alice.txt), este archivo ya está controlado por Git.

```
$ git status
On branch develop 
Your branch is up to date with 'origin/develop'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   Alice.txt

Untracked files:
  (use "git add <file>..." to include in what wil be committed)
	archivo-nuevo.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

Aquí es posible realizar las siguientes acciones:

* Hacer el staging del archivo nuevo

```
git add archivo-nuevo.txt
```

Esto **no** realiza un commit, solamente prepara el commit.
Para revertir la acción, usar `git restore --staged` como indicado, esto hace que el archivo nuevo regrese al estado 'untracked'.
Después de hacer `git add` del archivo nuevo:

```
$ git status
On branch develop 
Your branch is up to date with 'origin/develop'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	new file:   archivo-nuevo.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   Alice.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	../.casos-uso.md.swp
	../casos-uso.md

```

Para hacer el staging del archivo editado, usar `git add` nuevamente. `git add --all` adiciona todos los archivos editados.

* Observar los cambios en el archivo controlado por git:

```
$ git diff Alice.txt
diff --git a/example/Alice.txt b/example/Alice.txt
index 5d0912d..e22bed8 100644
--- a/example/Alice.txt
+++ b/example/Alice.txt
@@ -3,3 +3,5 @@ Archivo editado solamente por Alice.
 Alice trabajando...
 
 Alice realizó el primer commit después de escribir esta línea.
+
+Alice trabajo mas
```

Este formato es compatible con el comando patch (el uso de patch está fuera del alcance de este documento).

* Revertir el cambio del archivo Alice.txt:

```
git restore Alice.txt
```

Este comando va a hacer que el archivo local Alice.txt retorne al estado del último commit. Los cambios realizados en la versión del workspace (no comitados) del  archivo serán **perdidos** y esta acción no puede ser revertida.

## Enviar cambios al repositorio

El commit se realiza después de usar `git add` para adicionar cambios en el workspace, y después de verificar los cambios con `git status`.

```
git commit -m 'Un mensaje que refleje los cambios en este commit'
```

```
$ git status
On branch develop
Your branch is ahead of 'origin/develop' by 1 commit.
  (use "git push" to publish your local commits)

nothing to commit, working tree clean
```

* El status indica que repositorio local tiene registrado el commit realizado por Alice, pero que este commit aún no se ha enviado al repositorio remoto.
* Se pueden realizar más commits antes de hacer `git push`, que va a enviar al repositorio remoto.
* Los commits locales no pueden ser vistos por otros colegas hasta hacer `git push`.
* Es mejor tener alguna seguridad que los cambios están correctos antes de hacer `git push`, pues esto afecta la versión accessible por otros colegas.
* Para corregir un commit que ya fue enviado por push, es mejor realizar un nuevo commit encima y hacer `git push` nuevamente.

Para ver la lista de commits usar

```
git log
```

Para enviar los cambios usar

```
git push
```

* Verificar los datos del autor y commits usando `git log`
* El comando `git push` necesita de credenciales apropiadas (fuera del alcance de este documento).
