# UnaSmartHome OTA

Repository pubblico destinato esclusivamente agli aggiornamenti firmware.

- `automatico.json`: Debug abilitato per il collaudo 1.0.6; Release disabilitato.
- `manuale.json`: aggiornamento su richiesta, indipendente dai canali automatici.
- `una_esp8684_mesh_fissa_1.0.6.bin`: APP ESP8684/ESP32-C2, versione `1.0.6-debug`.

## Versione 1.0.6

Include le correzioni locali 1.0.5: ingressi letti ogni 10 ms con antirimbalzo,
salvataggi I/O differiti, controlli LED nella pagina Dispositivo, box uscite vuoti
nascosti e comandi Accendi/Spegni oppure Riavvia tempo per T Restart acceso.
Corretta l'inizializzazione delle card sui nodi senza layout-view, il recupero
dopo errore iniziale del catalogo e l'identificazione degli asset per evitare cache vecchie.

Build e test locali verificati. La stessa base 1.0.5 e stata provata via USB e
browser sul dispositivo 1; la numerazione 1.0.6 non e stata flashata via USB.
La pubblicazione non certifica l'installazione OTA sui moduli. Restano i problemi
di RAM e rallentamenti durante HTTPS con BLE, e il collaudo fra piu nodi e aperto.

## Collaudo Mesh Fissa

Rete comune incorporata nel codice, senza pairing manuale. Le chiavi sono
pubbliche e SOLO DI TEST: questa rete non protegge un impianto reale.
Un solo gateway con indice zero; slave con indici distinti da 1 a 9.
Tutte le schede nascono 2 luci / 2 rele con indice 1, da cambiare prima di
utilizzarle insieme. Annunci di identita e tipo scheda ogni circa 15 secondi.

OTA automatico predefinito al primo avvio. Una scelta Manuale gia salvata non
viene forzata: cambiarla da interfaccia per ricevere gli aggiornamenti automatici.
Le slave si aggiornano tramite Wi-Fi/OTA; non e un trasferimento firmware via BLE.
Colori LED indipendenti salvati in NVS, blu predefinito, collegati ai rele.
Il gateway mantiene disattivate le proprie uscite fisiche.

Correzione del port NimBLE per il buffer loopback locale e stack OTA ampliato.
Il binario pubblico non incorpora la password Wi-Fi di prova: usa reti salvate.
Non e l'immagine completa per un primo flash.

1.0.4 corregge l'avvio Wi-Fi sulle reti salvate: solo STA, senza AP superfluo.
L'AP di configurazione resta disponibile come fallback in assenza di reti valide.
Aggiornamento automatico reale 1.0.2 -> 1.0.3 verificato sul dispositivo 1,
con impostazioni e colori LED conservati e BLE online dopo il riavvio.
Aggiornamento da file 1.0.3 -> 1.0.4 verificato, con hash ELF e dati conservati.
Il tentativo automatico 1.0.3 -> 1.0.4 non e riuscito per memoria insufficiente.
Stress 1.0.4 BLE + HTTPS + pagine concorrenti: 262 risposte valide e 10 timeout,
nessun panic; la stabilita sotto questo carico NON e ancora raggiunta.

Revisione di collaudo: stack HTTPS corretto e tabelle ECC pre-calcolate in flash.
Riserva RAM aumentata ridimensionando task e buffer senza disabilitare BLE.
Il binario iniziale e stato ritirato per un overflow dello stack durante HTTPS.

Collaudo precedente dispositivo 1: download reale GitHub da 1.0.0 a 1.0.1, progresso 100%,
nuovo hash ELF verificato, impostazioni conservate e BLE attivo dopo il riavvio.
Non e una versione qualificata per produzione: il margine RAM durante HTTPS
con richieste web concorrenti resta molto basso e richiede altre ottimizzazioni.

Il binario non incorpora credenziali Wi-Fi o token GitHub. Mantiene le impostazioni
gia presenti sul dispositivo. Non e un'immagine completa per il primo flash:
richiede la tabella partizioni a due slot OTA prevista dal progetto MESH_IDF.

BLE rimane abilitato. Le credenziali di rete vengono lette dalla configurazione
locale, oppure impostate tramite l'AP `UNA-Smart` quando non ancora associato.

La compatibilita hardware e prevista per il modulo 2 ingressi / 2 uscite
ESP8684H4X: non caricare su altri modelli o configurazioni di partizioni.

## Accesso GitHub Dal Firmware

Il firmware usa l'API Contents con `Accept: application/vnd.github.raw+json`,
verifica CA e nome del server, e negozia TLS 1.2 ECDHE-ECDSA/AES128-GCM per
ridurre il carico RAM rispetto al certificato RSA del server raw.

L'accesso pubblico senza token e soggetto al limite GitHub di 60 richieste/ora
per indirizzo IP: adatto al collaudo, non al polling di molti nodi dietro lo
stesso router. In produzione occorre distribuire gli indici tramite gateway,
cache o un servizio OTA dedicato. Non incorporare token privati nel firmware.

Il campo `sha256` documenta l'impronta del file pubblicato; il firmware attuale
valida l'immagine ESP e il canale/versione, ma non confronta questo campo del
manifest. Non sostituisce una firma digitale del produttore.
