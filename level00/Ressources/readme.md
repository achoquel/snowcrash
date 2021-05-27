# Level00 - Recherche de fichiers
Pour résoudre le level00, on a un indice dans la vidéo de présentation du sujet. On nous demande de trouver les fichiers appartenant à flag00. Pour se faire, on peut éxecuter la commande `find ./ -user flag00` dans le root directory.

:::info
level00@SnowCrash:/$ find ./ -user flag00
...
./usr/sbin/john
...
/rofs/usr/sbin/john
...
:::

On obtient deux résultats (qui sont en réalité les mêmes). On regarde le contenu de `/usr/sbin/john`.

:::success
level00@SnowCrash:/$ cat /usr/sbin/john
cdiiddwpgswtgt
:::

On teste de se connecter avec le contenu du fichier: 
:::danger
level00@SnowCrash:/$ su flag00
Password: cdiiddwpgswtgt
su: Authentication failure
:::

Ca ne marche pas. Le mot de passe est peut être chiffré. On va sur dcode.fr comme suggéré dans la vidéo, et on teste le Code César en mode bruteforce. On obtient:

:::info
+15	nottoohardhere
+2	abggbbunequrer
+14	opuuppibseifsf
+1	bchhccvofrvsfs
+11	rsxxsslevhlivi
+3	zaffaatmdptqdq
+18	klqqllexoaebob
+25	dejjeexqhtxuhu
+17	lmrrmmfypbfcpc
+16	mnssnngzqcgdqd
+4	yzeezzslcospcp
+24	efkkffyriuyviv
+21	hinniibulxbyly
+13	pqvvqqjctfjgtg
+10	styyttmfwimjwj
+12	qrwwrrkdugkhuh
+8	uvaavvohykolyl
+22	ghmmhhatkwaxkx
+6	wxccxxqjamqnan
+5	xyddyyrkbnrobo
+19	jkppkkdwnzdana
+7	vwbbwwpizlpmzm
+23	fgllggzsjvzwjw
+20	ijoojjcvmyczmz
+9	tuzzuungxjnkxk
:::

On se connecte avec le mot de passe nottoohardhere, et on lance getflag.

:::success
level00@SnowCrash:/$ su flag00
Password: nottoohardhere
Don't forget to launch getflag !
flag00@SnowCrash:~$ getflag
Check flag.Here is your token : x24ti5gi3x0ol2eh4esiuxias
:::

On a notre token:
:::warning
x24ti5gi3x0ol2eh4esiuxias
:::
