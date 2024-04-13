# Drilske Dæknavne - Email
## Opgaven
>
> I forbindelse med en større sag om import og salg af narkotika, har man beslaglagt en mobiltelefon. Mobiltelefonen er en ældre Samsung S9, og ejes af en (knap så smart) kriminel som går under navnet "Martin Michellin" - et dæknavn, som han så fint sagde under anholdelsen med et dumt smil...
> 
> Vi mistænker at han skal aflevere noget stof til en småkriminel gadesælger ved navn "Jens Vejmand", noget stof som han får importeret snarligt. Vi har brug for at vide:
>
> Hvilken e-mail benytter martin? Den email vil være et flag som DDC{martin@email.dk}.
>
> Vi ved at der bliver benyttet diverse applikationer så som Snapchat, Whatsapp, SMS, opkald og lignende. Kan du undersøge telefonen?

Der udleveres en zipfil, som er et dump af en Android-telefon.


## Løsning
Første skridt er at unzippe filen og se mappestrukturen. Som det også står i opgaven er det en Samsung-telefon (Android), hvilket strukturen også passer ganske godt med.

```                                                                                                               
┌──(kali㉿kali)-[~/Downloads/22b9edd507047ece_files_full]
└─$ ll
total 136
drwxr-xr-x  2 kali kali  4096 Feb 16 06:26 acct
drwxr-xr-x 14 kali kali  4096 Feb 16 06:26 apex
-rw-r--r--  1 kali kali 36115 Dec 31  2008 audit_filter_table
drwxr-xr-x  7 kali kali  4096 Feb 16 06:30 cache
drwxr-xr-x  2 kali kali  4096 Dec 31  2008 carrier
drwxr-xr-x  2 kali kali  4096 Dec 31  1969 config
drwxr-xr-x  3 kali kali  4096 Jun 10  2018 cpefs
drwxr-xr-x 63 kali kali  4096 Feb 16 06:30 data
drwxr-xr-x  2 kali kali  4096 Dec 31  2008 debug_ramdisk
-rw-r--r--  1 kali kali  6071 Dec 31  2008 dpolicy
drwxr-xr-x 29 kali kali  4096 Feb 16 04:32 efs
drwxr-xr-x  3 kali kali  4096 Dec 31  2008 lib
drwxr-xr-x  2 kali kali  4096 Feb 16 06:26 lost+found
drwxr-xr-x 15 kali kali  4096 Feb 16 06:26 mnt
drwxr-xr-x  8 kali kali  4096 Dec 31  2008 odm
drwxr-xr-x  2 kali kali  4096 Dec 31  2008 oem
drwxr-xr-x  3 kali kali  4096 Jun 14  2018 omr
drwxr-xr-x  2 kali kali  4096 Dec 31  2008 preload
-rw-r--r--  1 kali kali  1509 Dec 31  2008 publiccert.pem
drwxr-xr-x  2 kali kali  4096 Dec 31  2008 sbin
drwxr-xr-x  5 kali kali  4096 Feb 16 06:30 storage
drwxr-xr-x  2 kali kali  4096 Feb 16 06:26 sys
drwxr-xr-x 21 kali kali  4096 Dec 31  2008 system
-rw-r--r--  1 kali kali  3186 Dec 31  2008 ueventd.rc
drwxr-xr-x 13 kali kali  4096 Dec 31  2008 vendor
```

Vi er gjort bekendt med, at vi skal finde en mailadresse et sted. De to personer bruger desuden en række af forskellige kommunikationsplatforme, herunder Snapchat, Whatsapp, SMS mv.

Men hvor kan man finde en mailadresse på telefonen? 

Mit første bud var Samsungs indbyggede mail-app, men efter undersøgelse af denne, fandt jeg ikke noget. Derfor gik jeg videre til de apps, der nævnes i opgavebeskrivelsen.

Mit bud er, at man vil kunne finde den som en del af profilen på i hvert fald Snapchat og Whatsapp. Derfor navigerer vi til data-stien for Snapchat med disse stier:

```
┌──(kali㉿kali)-[~/…/22b9edd507047ece_files_full/data/data/com.snapchat.android]
└─$ ll                     
total 28
drwxr-xr-x  6 kali kali 4096 Feb 16 06:20 cache
drwxr-xr-x  2 kali kali 4096 Feb 16 05:02 code_cache
drwxr-xr-x  4 kali kali 4096 Apr 13 13:54 databases
drwxr-xr-x 19 kali kali 4096 Feb 16 05:31 files
drwxr-xr-x  2 kali kali 4096 Feb 16 05:09 no_backup
drwxr-xr-x  2 kali kali 4096 Feb 16 05:35 Perception_ml_model
drwxr-xr-x  2 kali kali 4096 Feb 16 05:50 shared_prefs                                                           
```

Her er det mest oplagte bud, at man vil finde konfigurationer og lignende i `databases`, der som regel indeholde SQLite-databaser. 

Det viser sig, at der ligger et utal af databaser i den sti.
```
┌──(kali㉿kali)-[~/…/data/data/com.snapchat.android/databases]
└─$ ll
total 4088
drwxr-xr-x 2 kali kali   4096 Apr 13 13:08 489f5071-e858-43ae-b10c-59065e7f84d8
-rw-r--r-- 1 kali kali  18432 Apr 13 13:54 a19769fb-57aa-e237-acfd-b844e8ddc49d_fidelius.db
-rw-r--r-- 1 kali kali   4096 Feb 16 05:18 app.db
-rw-r--r-- 1 kali kali  32768 Feb 16 05:18 app.db-shm
-rw-r--r-- 1 kali kali 152472 Feb 16 05:18 app.db-wal
-rw-r--r-- 1 kali kali 253952 Feb 21 05:57 arroyo.db
-rw-r--r-- 1 kali kali  32768 Apr 13 13:54 arroyo.db-shm
-rw-r--r-- 1 kali kali      0 Apr 13 13:54 arroyo.db-wal
-rw-r--r-- 1 kali kali  57344 Feb 16 06:00 com.google.android.datatransport.events
-rw-r--r-- 1 kali kali  25136 Feb 16 06:00 com.google.android.datatransport.events-journal
-rw-r--r-- 1 kali kali 174080 Apr 13 13:06 core.db
-rw-r--r-- 1 kali kali   1024 Feb 16 05:09 creativetools.platform.db
-rw-r--r-- 1 kali kali  32768 Feb 16 05:50 creativetools.platform.db-shm
-rw-r--r-- 1 kali kali 100640 Feb 16 05:50 creativetools.platform.db-wal
-rw-r--r-- 1 kali kali  90112 Feb 16 05:50 durable_job
-rw-r--r-- 1 kali kali  32768 Feb 16 06:35 durable_job-shm
-rw-r--r-- 1 kali kali 524288 Feb 16 06:35 durable_job-wal
-rw-r--r-- 1 kali kali   1024 Feb 16 05:09 feature
-rw-r--r-- 1 kali kali  32768 Feb 16 05:50 feature-shm
-rw-r--r-- 1 kali kali 873016 Feb 16 05:50 feature-wal
-rw-r--r-- 1 kali kali   1024 Feb 16 05:08 fidelius_database.db
-rw-r--r-- 1 kali kali  32768 Feb 16 05:08 fidelius_database.db-shm
-rw-r--r-- 1 kali kali  23088 Feb 16 05:08 fidelius_database.db-wal
-rw-r--r-- 1 kali kali  57344 Apr 13 13:08 journal.db
-rw-r--r-- 1 kali kali 837632 Feb 16 07:50 main.db
-rw-r--r-- 1 kali kali   1024 Feb 16 05:09 media_packages
-rw-r--r-- 1 kali kali  32768 Feb 16 05:37 media_packages-shm
-rw-r--r-- 1 kali kali  88064 Feb 16 05:35 media_packages-wal
-rw-r--r-- 1 kali kali   1024 Feb 16 05:09 memories.db
-rw-r--r-- 1 kali kali  32768 Feb 16 05:35 memories.db-shm
-rw-r--r-- 1 kali kali 115312 Feb 16 05:35 memories.db-wal
drwxr-xr-x 3 kali kali   4096 Apr 13 13:15 native_content_manager
-rw-r--r-- 1 kali kali   1024 Feb 16 05:09 rtus.db
-rw-r--r-- 1 kali kali  32768 Feb 16 05:09 rtus.db-shm
-rw-r--r-- 1 kali kali   9464 Feb 16 05:09 rtus.db-wal
-rw-r--r-- 1 kali kali   1024 Feb 16 05:09 simple_db_helper.db
-rw-r--r-- 1 kali kali  32768 Feb 16 05:12 simple_db_helper.db-shm
-rw-r--r-- 1 kali kali 278800 Feb 16 05:12 simple_db_helper.db-wal
-rw-r--r-- 1 kali kali   1024 Feb 16 05:19 stickers.core.db
-rw-r--r-- 1 kali kali  32768 Feb 16 05:19 stickers.core.db-shm
-rw-r--r-- 1 kali kali   9464 Feb 16 05:19 stickers.core.db-wal
-rw-r--r-- 1 kali kali   4096 Feb 16 05:09 tiv.db
-rw-r--r-- 1 kali kali  32768 Feb 16 05:09 tiv.db-shm
-rw-r--r-- 1 kali kali  16512 Feb 16 05:09 tiv.db-wal
```

Ved brug af et værktøj til at læse SQLite-databaser, som eksempelvis [DB Browser for SQLite](https://sqlitebrowser.org/) kan man gennemse databaserne. 

Der er en række af interessante database-filer i blandt, men en fil ved navn `core.db` fanger interessen. 

![img.png](img.png)

Efter lidt søgen rundt i tabellerne, findes tabellen `SnapUserStore`, som indeholder en række af data om brugeren, herunder mailadressen: `michellinmartin04@gmail.com`, som også er løsningen på opgaven.
