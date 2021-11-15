# Crear un 'change branch'

- Un 'change branch' es un branch donde se va a realizar algún branch en el repositorio.
- Evitar realizar cambios directamente en "develop", en vez es mejor hacer "pull requests"
- De la misma forma, evitar realizar cambios en "master"!

## Crear un change branch desde CLI

Asumimos que el branch no existe aún en el repositorio remoto, entonces "Alice" lo va a crear por primera vez:

```
git checkout develop
git checkout -b my_change_branch
```

## Hacer commit de cambios en change branch

Hacemos los cambios de la misma forma que ya explicado anteriormente. Luego, podemos hacer uno o más commits:

```
git commit -m "Cambios para el feature GITFLOW-123"
```

## Enviar el change branch al repositorio remoto

El repositorio remoto no conoce nuestro branch nuevo, entonces debemos indicar de forma explícita cuál es el branch remoto (normalmente usamos el mismo nombre): 

```ssh
(Alice) $ git push -u origin my_change_branch
Enumerating objects: 7, done.
Counting objects: 100% (7/7), done.
Delta compression using up to 8 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 397 bytes | 397.00 KiB/s, done.
Total 4 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
remote: 
remote: Create a pull request for 'my_change_branch' on GitHub by visiting:
remote:      https://github.com/ginomcevoy/git-cheat-sheet/pull/new/my_change_branch
remote: 
To https://github.com/ginomcevoy/git-cheat-sheet.git
 * [new branch]      my_change_branch -> my_change_branch
Branch 'my_change_branch' set up to track remote branch 'my_change_branch' from 'origin'.
```

Ahora otras personas pueden ver el branch nuevo.

## Obtener copia local del change branch

Si otras personas quieren trabajar en el change branch, debemos traer los cambios. Una forma segura de hacer esto es con `git fetch`. En este ejemplo, el branch my_change_branch es nuevo:

```ssh
(Bob) $ git fetch
remote: Enumerating objects: 15, done.
remote: Counting objects: 100% (13/13), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 7 (delta 3), reused 7 (delta 3), pack-reused 0
Unpacking objects: 100% (7/7), 772 bytes | 772.00 KiB/s, done.
From https://github.com/ginomcevoy/git-cheat-sheet
   2e26a17..2c37093  master     -> origin/master
 * [new branch]      my_change_branch  -> origin/my_change_branch
```

Esto solamente actualiza la "vista local" de los branches en el repositorio remoto, esto no es lo mismo que los branches locales! El checkout va a crear un branch local que hace tracking al branch remoto:

```ssh
(Bob) $ git checkout my_change_branch
Branch 'my_change_branch' set up to track remote branch 'my_change_branch' from 'origin'.
Switched to a new branch 'my_change_branch'
```

Ahora Bob puede trabajar en my_change_branch. Este mismo procedimento es realizado cuando el branch es creado de forma externa, por ejemplo a través de GitFlow.

## Actualizar copia local del change branch

Ahora que Bob ha realizado un cambio y ha hecho push, Alice puede ver este cambio, usamos `git fetch` y notamos un cambio en my_change_branch:

```ssh
(Alice) $ git fetch
remote: Enumerating objects: 37, done.
remote: Counting objects: 100% (35/35), done.
remote: Compressing objects: 100% (18/18), done.
remote: Total 29 (delta 13), reused 24 (delta 8), pack-reused 0
Unpacking objects: 100% (29/29), 4.24 KiB | 98.00 KiB/s, done.
From https://github.com/ginomcevoy/git-cheat-sheet
   81d065c..c1f66bd  my_change_branch  -> origin/my_change_branch
```

Para saber qué hacer, siempre podemos hacer `git status`

```ssh
(Alice) $ git status
On branch my_change_branch
Your branch and 'origin/my_change_branch' have diverged,
and have 1 and 1 different commits each, respectively.
  (use "git pull" to merge the remote branch into yours)

nothing to commit, working tree clean
```

En este caso, Alice tiene un commit que el repositorio remoto no tiene, y el repositorio remoto tiene un commit que Alice no tiene. Seguimos la recomendación, va a pedir un mensaje para el "merge" de ambos commits:

```ssh
(Alice) $ git pull
warning: Pulling without specifying how to reconcile divergent branches is
discouraged. You can squelch this message by running one of the following
commands sometime before your next pull:

  git config pull.rebase false  # merge (the default strategy)
  git config pull.rebase true   # rebase
  git config pull.ff only       # fast-forward only

You can replace "git config" with "git config --global" to set a default
preference for all repositories. You can also pass --rebase, --no-rebase,
or --ff-only on the command line to override the configured default per
invocation.

Merge made by the 'recursive' strategy.
 example/Bob.txt | 2 ++
 1 file changed, 2 insertions(+)
```

Nota: podemos hacer "git pull" directamente sin "git fetch", pero estos pasos adicionales ayudan a entender lo que ocurre.
Por ahora vamos a usar la estrategia "pull.rebase false", que ha creado un nuevo commit:

```ssh
(Alice) $ git log
commit 471e328876ef3441516f988c1832a3c4586c70a4 (HEAD -> my_change_branch)
Merge: 9ad5f97 c1f66bd
Author: Alice <gino.mcevoy@gmail.com>
Date:   Mon Nov 15 16:59:05 2021 -0300

    Merge branch 'my_change_branch' of https://github.com/ginomcevoy/git-cheat-sheet into my_change_branch

commit 9ad5f9799cc467f2d45ae6c318e0468371204921
Author: Alice <gino.mcevoy@gmail.com>
Date:   Mon Nov 15 16:54:56 2021 -0300

    Alice Alice.txt my_change_branch

commit c1f66bd1d1eaada840483f9f49c6f73dafd1304c (origin/my_change_branch)
Author: Giacomo Mc Evoy <gino.mcevoy@gmail.com>
Date:   Mon Nov 15 16:52:51 2021 -0300

    Bob en Bob.txt
```

Ahora Alice tiene una versión coherente de my_change_branch, que incluye los cambios de Bob (en el repositorio remoto) y sus propios cambios (solamente en el repositorio local de Alice). Podemos terminar con git push para que el repositorio remoto tenga los cambios de ambos. 

```ssh
(Alice) $ git push
```

## Subir copia local desactualizada del change_branch 

Si hacemos git push sin revisar el repositorio remoto, podemos darnos con la "sorpresa" que no podemos hacer push:

```ssh
(Alice) $ git push
To https://github.com/ginomcevoy/git-cheat-sheet.git
 ! [rejected]        my_change_branch -> my_change_branch (fetch first)
error: failed to push some refs to 'https://github.com/ginomcevoy/git-cheat-sheet.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

Aquí lo que debemos hacer es repetir el procedimiento anterior e integrar los cambios locales con los remotos.

## Actualizar copia local del change_branch con conflicto

Si el repositorio remoto tiene un cambio que no puede ser integrado de forma automática con git pull, git nos lo va a hacer saber:

```ssh
(Alice) $ git pull
warning: Pulling without specifying how to reconcile divergent branches is
discouraged. You can squelch this message by running one of the following
commands sometime before your next pull:

  git config pull.rebase false  # merge (the default strategy)
  git config pull.rebase true   # rebase
  git config pull.ff only       # fast-forward only

You can replace "git config" with "git config --global" to set a default
preference for all repositories. You can also pass --rebase, --no-rebase,
or --ff-only on the command line to override the configured default per
invocation.

Auto-merging example/Alice.txt
CONFLICT (content): Merge conflict in example/Alice.txt
Automatic merge failed; fix conflicts and then commit the result.
```

Esto ocurre porque Bob y Alice tienen *commits* que modifican un mismo archivo de forma diferente. Es necesario conciliar el conflicto, `git status` ayuda a entender lo ocurrido:

```ssh
(Alice) $ git status
On branch my_change_branch
Your branch and 'origin/my_change_branch' have diverged,
and have 3 and 1 different commits each, respectively.
  (use "git pull" to merge the remote branch into yours)

You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)
  both modified:   Alice.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

Git ya ha creado una versión *temporal* del archivo, que necesariamente debe ser revisado por Alice:

```ssh
(Alice) $ cat Alice.txt 
Archivo editado solamente por Alice.

Alice trabajando...

Alice realizó el primer commit después de escribir esta línea.
Alice ha trabajado otra vez y realiza otro cambio.
<<<<<<< HEAD

Alice my_change_branch
Alice my_change_branch otra vez
||||||| c1f66bd
=======

Bob ha editado el archivo de Alice!
>>>>>>> 54d25c9d751167806fe46d181fbd97968e788f58
```

Una forma de arreglar el conflicto es de la siguiente forma:

```ssh
(Alice) $ cat Alice.txt 
Archivo editado solamente por Alice.

Alice trabajando...

Alice realizó el primer commit después de escribir esta línea.
Alice ha trabajado otra vez y realiza otro cambio.
Alice my_change_branch
Alice my_change_branch con los cambios de Bob
```

Después de esto, hacemos git add para indicar a git que los cambios locales son adecuados y el conflicto se ha resuelto:

```
(Alice) $ git add Alice.txt
```

Ahora git status indica que todo está bien:

```
(Alice) $ git status
On branch my_change_branch
Your branch and 'origin/my_change_branch' have diverged,
and have 3 and 1 different commits each, respectively.
  (use "git pull" to merge the remote branch into yours)

All conflicts fixed but you are still merging.
  (use "git commit" to conclude merge)

Changes to be committed:
  modified:   Alice.txt
```

Después esto podemos hacer `git commit` (se va a abrir un editor de texto para el mensaje de merge) y `git push` para terminar.

## Hacer un merge de un branch a otro

- Normalmente NO queremos hacer esto cuando usamos GitFlow / CommonFlow. En vez de eso, queremos hacer un "Pull Request"
- Esto puede ser hecho si tenemos un branch personal de desarrollo propio que nadie más tiene, y queremos incluirlo en un "change branch". Ejemplo:

```ssh
$ git checkout public_change_branch
$ git merge my_secret_little_branch
```

En este ejemplo, los commits del branch "my_secret_little_branch" van a ser incluidos en "public_change_branch". Lo ideal es que el branch public_change_branch NO tenga commits nuevos, pues esto va a generar un commit de merge.

Para evitar el commit de merge, podemos hacer ANTES del merge anterior:

```ssh
$ git checkout my_secret_little_branch
$ git rebase public_change_branch
```

- Esto va a hacer que los commits nuevos que están en public_change_branch sean "reaplicados" en my_secret_little_branch.
- La recomendación es que "rebase" solamente sea usado con branches "personales" Y solamente en el caso que el branch personal es derivado del change branch, para minimizar la chance que el rebase "falle".

## Hacer un Pull Request

- Este es el proceso recomendado para actualizar el branch develop
- Siempre queremos hacer un PR de un change branch al branch develop
- El procedimento depende de la herramienta. El proceso es más o menos intuitivo, pero hay que asegurarse que el sentido change_branch -> develop sea el correcto.
- Esto es importante para que todos los involucrados puedan evaluar el PR y hacer cambios si es necesario.
