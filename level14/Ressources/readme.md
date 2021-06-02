# Level14 - RIP getflag
Pour résoudre le level14, on a rien. Aucun fichier. `getflag` ne nous donne pas le flag. On se doute que pour finir en beauté, on va exploiter getflag. On

On va utiliser la même méthode que pour le level précédent, c'est à dire modifier l'uid lors de l'exécution du programme pour faire croire à getflag que nous sommes l'utilisateur `flag14`.


On va désassembler le programme avec `gdb` et regarder sa structure.

```
level14@SnowCrash:~$ gdb getflag
...
(gdb) disass main
Dump of assembler code for function main:
   0x08048946 <+0>:	push   %ebp
   0x08048947 <+1>:	mov    %esp,%ebp
   0x08048949 <+3>:	push   %ebx
   0x0804894a <+4>:	and    $0xfffffff0,%esp
   0x0804894d <+7>:	sub    $0x120,%esp
   0x08048953 <+13>:	mov    %gs:0x14,%eax
   0x08048959 <+19>:	mov    %eax,0x11c(%esp)
   0x08048960 <+26>:	xor    %eax,%eax
   0x08048962 <+28>:	movl   $0x0,0x10(%esp)
   0x0804896a <+36>:	movl   $0x0,0xc(%esp)
   0x08048972 <+44>:	movl   $0x1,0x8(%esp)
   0x0804897a <+52>:	movl   $0x0,0x4(%esp)
   0x08048982 <+60>:	movl   $0x0,(%esp)
   0x08048989 <+67>:	call   0x8048540 <ptrace@plt>
   0x0804898e <+72>:	test   %eax,%eax
 ...
   0x08048af8 <+434>:	call   0x80484c0 <fwrite@plt>
   0x08048afd <+439>:	call   0x80484b0 <getuid@plt>
   0x08048b02 <+444>:	mov    %eax,0x18(%esp)
   0x08048b06 <+448>:	mov    0x18(%esp),%eax
   0x08048b0a <+452>:	cmp    $0xbbe,%eax
   0x08048b0f <+457>:	je     0x8048ccb <main+901>
   0x08048b15 <+463>:	cmp    $0xbbe,%eax
   0x08048b1a <+468>:	ja     0x8048b68 <main+546>
   0x08048b1c <+470>:	cmp    $0xbba,%eax
   0x08048b21 <+475>:	je     0x8048c3b <main+757>
   0x08048b27 <+481>:	cmp    $0xbba,%eax
   0x08048b2c <+486>:	ja     0x8048b4d <main+519>
   0x08048b2e <+488>:	cmp    $0xbb8,%eax
   0x08048b33 <+493>:	je     0x8048bf3 <main+685>
  ...
   0x08048eca <+1412>:	leave
   0x08048ecb <+1413>:	ret
End of assembler dump.
```

On remarque directement la partie qui nous intéresse, c'est à dire l'appel à `getuid()` suivi d'un tas de comparaisons:

```
   0x08048afd <+439>:	call   0x80484b0 <getuid@plt>
   0x08048b02 <+444>:	mov    %eax,0x18(%esp)
   0x08048b06 <+448>:	mov    0x18(%esp),%eax
   0x08048b0a <+452>:	cmp    $0xbbe,%eax
   0x08048b0f <+457>:	je     0x8048ccb <main+901>
   0x08048b15 <+463>:	cmp    $0xbbe,%eax
   0x08048b1a <+468>:	ja     0x8048b68 <main+546>
   0x08048b1c <+470>:	cmp    $0xbba,%eax
   0x08048b21 <+475>:	je     0x8048c3b <main+757>
   0x08048b27 <+481>:	cmp    $0xbba,%eax
   0x08048b2c <+486>:	ja     0x8048b4d <main+519>
   0x08048b2e <+488>:	cmp    $0xbb8,%eax
   0x08048b33 <+493>:	je     0x8048bf3 <main+685>
  ...
```

On se doute que le fonctionnement est simillaire au `level13`. On récupère l'UID de l'utilisateur actuel, et on va le comparer pour donner le bon flag. On va mettre un breakpoint sur la comparaison, et on va venir modifier la valeur contenue dans `eax`, qui sera le retour de `getuid()`, par l'id de flag14.

```
level14@SnowCrash:~$ id flag14
uid=3014(flag14) gid=3014(flag14) groups=3014(flag14),1001(flag)

level14@SnowCrash:~$ gdb getflag
...
(gdb) break *0x08048b0a
Breakpoint 1 at 0x8048b0a
(gdb) run
Starting program: /bin/getflag
You should not reverse this
[Inferior 1 (process 4493) exited with code 01]
```

Ca ne marche pas. Quelque chose semble nous empêcher de reverse le programme. On met un breakpoint au debut du main et on exécute en pas-à-pas le programme. Le problème semble venir de l'appel à `ptrace()`.
On peut contourner ce problème grâce à quelques commandes que l'on trouve sur ce post:
https://gist.github.com/poxyran/71a993d292eee10e95b4ff87066ea8f2

```
(gdb) catch syscall ptrace
Catchpoint 2 (syscall 'ptrace' [26])
(gdb) commands 1
Type commands for breakpoint(s) 1, one per line.
End with a line saying just "end".
>set $(eax) = 0
>continue
>end

(gdb) break *0x08048b0a
Breakpoint 1 at 0x8048b0a

(gdb) run
Starting program: /bin/getflag

Catchpoint 1 (call to syscall ptrace), 0xb7fdd428 in __kernel_vsyscall ()

Catchpoint 1 (returned from syscall ptrace), 0xb7fdd428 in __kernel_vsyscall ()

Breakpoint 1, 0x08048b0a in main ()

(gdb) i r
eax            0x7de	2014
ecx            0xb7fda000	-1208115200
edx            0x20	32
ebx            0xb7fd0ff4	-1208152076
esp            0xbffff5c0	0xbffff5c0
ebp            0xbffff6e8	0xbffff6e8
esi            0x0	0
edi            0x0	0
eip            0x8048b0a	0x8048b0a <main+452>
eflags         0x200292	[ AF SF IF ID ]
cs             0x73	115
ss             0x7b	123
ds             0x7b	123
es             0x7b	123
fs             0x0	0
gs             0x33	51
(gdb) set $eax = 3014
(gdb) c
Continuing.
Check flag.Here is your token : 7QiHafiNa3HVozsaXkawuYrTstxbpABHD8CPnHJ
[Inferior 1 (process 4532) exited normally]
```

On a notre token: `7QiHafiNa3HVozsaXkawuYrTstxbpABHD8CPnHJ`

