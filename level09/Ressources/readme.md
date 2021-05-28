# Level09 - Interprétation de programme
Pour résoudre le level09, on a un fichier level09 et un fichier token. Quand on exécute level09, on nous demande de lui donner un argument.

```
level09@SnowCrash:~$ ./level09
You need to provied only one arg.
```

On lui donne token, mais le programme nous retourne un résultat étrange.

```
level09@SnowCrash:~$ ./level09 token
tpmhr
```

Si on ouvre token, on remarque que ce dernier semble "crypté". Par ailleurs, ce n'est pas le contenu du fichier qui est lu par le programme, mais bien l'argument lui même, qui est par la suite modifié. Le contenu du fichier token a du être encrypté par `level09`.
```
level09@SnowCrash:~$ cat token
f4kmm6p|=�p�n��DB�Du{��
```

Pour trouver le pattern d'encryptage, on teste le programme avec différentes strings:

```
level09@SnowCrash:~$ ./level09 abc
ace
level09@SnowCrash:~$ ./level09 12345678
13579;=?
level09@SnowCrash:~$ ./level09 11111111
12345678
```

On peut voir que le programme semble incrémenter chaque caractère de sa position dans la string.

Pour décoder le token, on crée un programme qui fait l'inverse:

```
#include <unistd.h>
#include <string.h>

int main(int ac, char **av)
{
    int i = 0;
    
    while(i < strlen(av[1]))
    {
        putchar(av[1][i] - i);
        ++i;
    }
}
```
On lance le programme en lui envoyant le contenu du fichier token:
```
level09@SnowCrash:/tmp$ ./a.out $(cat /home/user/level09/token)
f3iji1ju5yuevaus41q1afiuq
level09@SnowCrash:/tmp$
```
On se connecte sur le compte flag09 et on obtient notre token:

```
level09@SnowCrash:/tmp$ su flag09
Password: f3iji1ju5yuevaus41q1afiuq
Don't forget to launch getflag !
flag09@SnowCrash:~$ getflag
Check flag.Here is your token : s5cAJpM8ev6XHw998pRWG728z
```

On a notre token: `s5cAJpM8ev6XHw998pRWG728z`

