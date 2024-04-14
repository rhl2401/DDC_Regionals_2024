# Pegeleg

## Opgaven
> Den danske stat skal til at tilegne folk nye adresser. De har derfor udviklet et smart system som selv tilegner folk en addresse. Det eneste du skal gøre er at taste navnet på din bolig. Men det siges at borgmesteren altid vil bo 2288 addresser væk fra dig. Kan du finde hans addresse og dermed flaget?

## Løsning
Første skridt er at få forbindelse til lab og se, hvad der ligger på maskinen, vi får forbindelse til. 

```
DDC@40b41e02add0:~$ ls -la
total 44
drwxr-xr-x 1 DDC  DDC   4096 Apr 13 22:39 .
drwxr-xr-x 1 root root  4096 Apr 13 22:39 ..
-rw-r--r-- 1 DDC  DDC    220 Apr 23  2023 .bash_logout
-rw-r--r-- 1 DDC  DDC   3526 Apr 23  2023 .bashrc
-rw-r--r-- 1 DDC  DDC    807 Apr 23  2023 .profile
-rwxr-xr-x 1 root root 18240 Apr 13 22:39 huse
-rw-rw-rw- 1 root root  1239 Apr 13 22:38 system.c
```

Det viser sig, at der ligger selve programmet `system.c` og et kompilerede program `huse`. 

Lad os prøve at køre programmet: 

```
DDC@40b41e02add0:~$ ./huse 
Enter 1 to create house
Enter 2 to print house
Enter -1 to quit
```

Vi får tre muligheder, enten at oprette et hus, printe et hus eller lukke programmet. 

Først prøver vi at oprette et hus og ser, hvad der sker: 

```
DDC@40b41e02add0:~$ ./huse 
Enter 1 to create house
Enter 2 to print house
Enter -1 to quit
1
name of home?
```

Vi giver den et tilfældigt navn, da vi fra starten af ikke har bedre bud. 

```
DDC@40b41e02add0:~$ ./huse 
Enter 1 to create house
Enter 2 to print house
Enter -1 to quit
1
name of home?
mithus
your address is, 0x555c272cbb90
Enter 1 to create house
Enter 2 to print house
Enter -1 to quit
```

Når huset er oprettet, bemærkes det, at vi får en "address" i et sjovt format - det ligner vist lidt noget hex. 

Så ser vi, hvad "print" kan klare. 

```
Enter 1 to create house
Enter 2 to print house
Enter -1 to quit
1
name of home?
mithus
your address is, 0x555c272cbb90
Enter 1 to create house
Enter 2 to print house
Enter -1 to quit
2
Enter the address for s2 in hexadecimal format (e.g., 0x7ffe1): 
```

Her beder programmet om at man indtaster en adresse, men igen i et hex-format. 

Fra opgavebeskrivelsen ved vi, at borgesterens hus altid ligger 2288 adresser væk fra én selv. Derfor prøver vi at se, om vi kan tage vores egen adresse og lægge 2288 til. 

### Udregning

Ved brug af en hex-lommeregner (eller din skarpe hovedregning fra Computer Engineering-timerne), finder vi, at tallet 2288 i decimaltal (10-talssystemet) er lig med `0x8F0` i hex. 

Derefter lægger vi de to tal sammen: 
`0x555c272cbb90` + `0x8F0` = `0x555C272CC480`

Og skriver dette tal ind som adresse i programmet: 

```
Enter 1 to create house
Enter 2 to print house
Enter -1 to quit
1
name of home?
mithus
your address is, 0x555c272cbb90
Enter 1 to create house
Enter 2 to print house
Enter -1 to quit
2
Enter the address for s2 in hexadecimal format (e.g., 0x7ffe1): 0x555C272CC480
name of home is, 
free(): invalid pointer
Aborted (core dumped)
```

Som giver en fejl ... Nå, lad os se, om vi skal trække fra. 

Vi er nødt til at køre programmet igen, oprette et nyt hus og så trække `0x8F0` fra den nye adresse. 

```
Enter 1 to create house
Enter 2 to print house
Enter -1 to quit
1
name of home?
mithus
your address is, 0x5617dc2fab90
Enter 1 to create house
Enter 2 to print house
Enter -1 to quit
2
Enter the address for s2 in hexadecimal format (e.g., 0x7ffe1): 0x5617DC2FA2A0
name of home is, DDC{peg_ikke_p4_mig}
Enter 1 to create house
Enter 2 to print house
Enter -1 to quit
```

Og ganske rigtigt, så finder vi et hus med et flag.

``` 
DDC{peg_ikke_p4_mig}
```


