# UnaSmartHome OTA

Repository pubblico destinato esclusivamente agli aggiornamenti firmware.

- `automatico.json`: canali Debug e Release distinti; attualmente disabilitati.
- `manuale.json`: aggiornamento su richiesta, indipendente dai canali automatici.
- `una_esp8684_manual_1.0.1.bin`: APP ESP8684/ESP32-C2, versione `1.0.1-debug`.

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
