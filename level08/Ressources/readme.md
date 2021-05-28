# Level08 - Lien symbolique
Pour résoudre le level08, on a un fichier level08 et un fichier token. Quand on exécute level08, on nous demande de lui donner un fichier à lire.

```
level08@SnowCrash:~$ ./level08
./level08 [file to read]
```

On lui donne token, mais le programme nous dit que nous n'avons pas le droit de le lire.

```
level08@SnowCrash:~$ ./level08 token
You may not access 'token'
```

On utilise `strings` sur le programme, et on peut voir une ligne mentionnant `token`, et plus bas un appel à la fonction `strstr`:

```
%s [file to read]
token
You may not access '%s'
Unable to open %s
Unable to read fd %d
...
strstr@@GLIBC_2.0
```

Si on essaie d'exécuter le programme avec n'importe quel autre fichier, ce dernier est lu sans soucis. On en conclus donc qu'il y a une protection sur le nom du fichier que le programme lis, et que si ce dernier contient "token", il ne sera pas lu. Comme nous n'avons pas le droit de renommer token ou de le copier, on crée un lien symbolique qui sera nommé autrement.

```
level08@SnowCrash:~$ ln -s /home/user/level08/token /tmp/toto
level08@SnowCrash:~$ ./level08 /tmp/toto
quif5eloekouj29ke0vouxean
```

On a notre token: `quif5eloekouj29ke0vouxean`

