# Level13 - Debugging avec GDB
Pour résoudre le level13, on a un fichier `level13`. Lorsqu'on lance le programme, il nous indique qu'il devrait être lancé depuis l'utilisateur ayant comme UID 4242.

```
level13@SnowCrash:~$ ./level13
UID 2013 started us but we we expect 4242
```

En regardant les `strings` du programme, on voit un appel à `getuid()`, et des strings évoquant le flag:

```
boe]!ai0FB@.:|L6l@A?>qJ}I
your token is %s
...
getuid@@GLIBC_2.0
```

On va désassembler le programme avec `gdb` et regarder sa structure.

```
level13@SnowCrash:~$ gdb level13
...
(gdb) disass main
Dump of assembler code for function main:
   0x0804858c <+0>:	push   %ebp
   0x0804858d <+1>:	mov    %esp,%ebp
   0x0804858f <+3>:	and    $0xfffffff0,%esp
   0x08048592 <+6>:	sub    $0x10,%esp
   0x08048595 <+9>:	call   0x8048380 <getuid@plt>
   0x0804859a <+14>:	cmp    $0x1092,%eax
   0x0804859f <+19>:	je     0x80485cb <main+63>
   0x080485a1 <+21>:	call   0x8048380 <getuid@plt>
   0x080485a6 <+26>:	mov    $0x80486c8,%edx
   0x080485ab <+31>:	movl   $0x1092,0x8(%esp)
   0x080485b3 <+39>:	mov    %eax,0x4(%esp)
   0x080485b7 <+43>:	mov    %edx,(%esp)
   0x080485ba <+46>:	call   0x8048360 <printf@plt>
   0x080485bf <+51>:	movl   $0x1,(%esp)
   0x080485c6 <+58>:	call   0x80483a0 <exit@plt>
   0x080485cb <+63>:	movl   $0x80486ef,(%esp)
   0x080485d2 <+70>:	call   0x8048474 <ft_des>
   0x080485d7 <+75>:	mov    $0x8048709,%edx
   0x080485dc <+80>:	mov    %eax,0x4(%esp)
   0x080485e0 <+84>:	mov    %edx,(%esp)
   0x080485e3 <+87>:	call   0x8048360 <printf@plt>
   0x080485e8 <+92>:	leave
   0x080485e9 <+93>:	ret
End of assembler dump.
```

On remarque directement la partie qui nous intéresse, c'est à dire l'appel à `getuid()` suivi d'une comparaison:

```
   0x08048595 <+9>:	call   0x8048380 <getuid@plt>
   0x0804859a <+14>:	cmp    $0x1092,%eax
```

On va mettre un breakpoint sur la comparaison, et on va venir modifier la valeur contenue dans `eax`, qui sera le retour de `getuid()`, c'est à dire `2013`, qui est l'uid de l'utilisateur level13.

```
(gdb) break *0x0804859a
Breakpoint 1 at 0x804859a

(gdb) run
Starting program: /home/user/level13/level13

Breakpoint 1, 0x0804859a in main ()

(gdb) i r
eax            0x7dd	2013
ecx            0xbffff764	-1073744028
edx            0xbffff6f4	-1073744140
ebx            0xb7fd0ff4	-1208152076
esp            0xbffff6b0	0xbffff6b0
ebp            0xbffff6c8	0xbffff6c8
esi            0x0	0
edi            0x0	0
eip            0x804859a	0x804859a <main+14>
eflags         0x200246	[ PF ZF IF ID ]
cs             0x73	115
ss             0x7b	123
ds             0x7b	123
es             0x7b	123
fs             0x0	0
gs             0x33	51

(gdb) set $eax = 4242

(gdb) c
Continuing.
your token is 2A31L79asukciNyi8uppkEuSx
```

On a notre token: `2A31L79asukciNyi8uppkEuSx`

