---
date: '2017-11-05 17:38 +0100'
published: true
title: NodeJS - installera Node.js med NVM på en VPS
---
Nvm installerar node.js i användarens hemkatalog. Det här är bra för utveckling, men om du verkligen vill vara värd för nodeapplikationer, vill du inte installera den senaste nya versionen av nod via nvm och upptäcka att du oavsiktligt har orsakat din produktnosapp (som kan vara inkompatibel med senaste noden.js) slutar fungera. Det är bäst att installera en kopia av nod globalt så att andra användare kan komma åt den och använda nvm för att växla mellan dina utvecklingsversioner.

Kommandot nedan kopiera vilken version av nod du har aktiv via nvm till /usr/local/ katalogen) och ställa in behörigheterna så att alla användare kan komma åt dem.

```
n=$(which node);n=${n%/bin/node}; chmod -R 755 $n/bin/*; sudo cp -r $n/{bin,lib,share} /usr/local
```

> Om du någonsin vill ändra versionen av nod som är installerad, gör bara en annan nvm-användning vXX.XX.XX för att byta användarens nod till den version du vill ha och kör sedan kommandot ovan för att kopiera det till systemet katalogen.

För att kontrollera att det fungerar, byt till rotanvändaren och gör ett till which kommando för att se till att noden nu är installerad till /usr/local/bin:

```
sudo -s
which node
```

Du borde nu få se: 

```
/usr/local/bin/node
```
