# Level02 - Fichier .pcap
Pour résoudre le level02, on nous fournis un fichier .pcap. Pour exploiter ce dernier, on l'ouvre avec WireShark. Il contient des données d'un réseau en packets. Si on follow le flux TCP, on peut voir que ce qui est contenu dans ce fichier est une tentative de login, qui a échoué. 

```
wwwbugs login: l.le.ev.ve.el.lX.X
..
Password: ft_wandr...NDRel.L0L
.
..
Login incorrect
```

La partie qui nous intéresse est celle du mot de passe. Sa structure semble bizarre, des portions sont en double. On se doute que le mot de passe a été initiallement mal entré et que l'utilisateur a voulu le corriger.

`Password: ft_wandr...NDRel.L0L`

On retire les segments en double et on laisse ceux retapés, et on obtient `ft_waNDReL0L`.


On se connecte avec le mot de passe ft_waNDReL0L, et on lance getflag.

```
level02@SnowCrash:/$ su flag02
Password: ft_waNDReL0L
Don't forget to launch getflag !
flag02@SnowCrash:~$ getflag
Check flag.Here is your token : kooda2puivaav1idi4f57q8iq
```

On a notre token: `kooda2puivaav1idi4f57q8iq`

