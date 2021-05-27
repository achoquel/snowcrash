# Level01 - /etc/passwd & John the Ripper
Pour résoudre le level01, on regarde dans /etc/passwd, et on voit que l'utilisateur flag01 a le hash de son mot de passe visible.

```
level01@SnowCrash:/$ cat /etc/passwd
...
flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash
...
```

La structure des entrées dans le fichier /etc/passwd est:
`username:password:uid:gid:gecos:home:command`

On récupère donc le hash, et on va utiliser `John the Ripper` pour récupérer le vrai mot de passe.
On stocke la ligne obtenue dans `/etc/passwd` dans un fichier, et on exécute John dessus.
```
john --show passwd
flag01:abcdefg:3001:3001::/home/flag/flag01:/bin/bash

1 password hash cracked, 0 left
```

On se connecte avec le mot de passe abcdefg, et on lance getflag.

```
level01@SnowCrash:/$ su flag01
Password: agcdefg
Don't forget to launch getflag !
flag01@SnowCrash:~$ getflag
Check flag.Here is your token : f2av5il02puano7naaf6adaaf
```

On a notre token: `f2av5il02puano7naaf6adaaf`

