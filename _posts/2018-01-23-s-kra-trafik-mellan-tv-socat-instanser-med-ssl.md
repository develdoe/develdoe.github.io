---
date: '2018-01-23 14:28 +0100'
published: false
title: Säkra trafik mellan två socat-instanser med SSL
---
## Introduktion

När du vill ansluta två socat-processer som körs på olika maskiner och känner att du behöver skydda anslutningen mot obehörig åtkomst, sniffing, dataprofilering mm kanske du vill kryptera kommunikationen.

socat integrerar OpenSSL-biblioteket och tillhandahåller SSL-klient- och serverfunktioner.

SSL är ett komplext protokoll som ger mycket fler funktioner än vad som krävs för att skydda en enda anslutning. I det här dokumentet presenterar vi bara ett enkelt scenario som ger bara de grundläggande säkerhetskraven.

## Konfigurera OpenSSL

Det här avsnittet visar hur SSL-adresserna kan konfigureras i socat. I det här dokumentet använder vi bara självtecknade certifikat för enkelhets skull.

Vi antar att serverns värd heter *server.domain.org* och servernsprocessen använder port 4433. För att hålla det enkelt använder vi en mycket enkel server funktionalitet som bara echos data (`echo`) och `stdio` på klienten.

### Generera ett servercertifikat

Utför följande steg på en betrodd värd där OpenSSL är installerat. Det kan lika väl vara klienten eller servern värd själva.

Förbered ett basnamn:

```bash
FILENAME=server
```

Skapa ett offentligt/privat nyckelpar:

```bash
openssl genrsa -out $FILENAME.key 1024
```

Skapa ett självtecknat certifikat:

```bash
openssl req -new -key $FILENAME.key -x509 -days 3653 -out $FILENAME.crt
```

*Du kommer att bli uppmanad att ange landskod, namn etc .; du kan sluta alla anvisningar med enter-tangenten.*

Generera PEM-filen genom att bara lägga till nyckel- och certifikatfilerna:

```bash
cat $FILENAME.key $FILENAME.crt >$FILENAME.pem
```

The files that contain the private key should be kept secret, thus adapt their permissions:

```bash
chmod 600 $FILENAME.key $FILENAME.pem
```

Ta nu filen `server.pem` till SSL-servern, t.ex. att katalogera  HOME/etc/, med en säker kanal som USB-minne eller SSH. Håll fasta behörigheter på filen även på målvärden och ta bort alla andra instanser av `server.key` och `server.pem`.

Kopiera trustcertifikat `server.crt` till SSL-klientvärden, t.ex. att katalog `$HOME/etc/`; en säker kanal krävs inte här, och behörigheterna är inte kritiska.

## Skapa ett klientcertifikat

Förbered först ett annat basnamn för filerna relaterade till klientcertifikatet:

```bash
FILENAME=client
```

Upprepa proceduren för certifikatgenerering som beskrivs ovan. Kopiera client.pem till SSL-klienten och client.crt till servern.

## OpenSSL Server

I stället för att använda en tcp-lyssning (tcp-l) -adress, använder vi openssl-listen (ssl-l) för servern, cert = ... berättar programmet för filen som innehåller dess ceritificate och privat nyckel och cafile =. .. pekar på filen som innehåller certifikat av peer; Vi litar bara på klienter om de kan bevisa att de har den privata nyckeln (OpenSSL hanterar detta för oss):

```bash
socat openssl-listen:4433,reuseaddr,cert=$HOME/etc/server.pem,cafile=$HOME/etc/client.crt echo
```

Efter att ha startat det här kommandot bör socat lyssna på port 4433, men kräver klientautentisering.

## OpenSSL Client
