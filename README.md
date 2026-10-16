# App Android "Orologio Scolastico"

App (Java, nessuna dipendenza esterna, Android 6.0+, targetSdk 35; NON ancora compilata né provata su un telefono reale) per la scheda
Arduino Nano con RTC, LCD e modulo Bluetooth seriale (HC-05/HC-06 o simili, profilo SPP).

## Funzioni
- Cerca i dispositivi Bluetooth, mostra anche quelli già associati, scelta della scheda e associazione (PIN 1234 / 0000).
- **Invia data e ora del telefono** -> comando `*updatetime*ddMMyyyyHHmmss` e feedback "Clock Time Updated" con l'ora riletta dall'RTC.
- **Orario di oggi** -> `*readtiming*` (RAM / EEPROM / durata campana).
- **Orario settimanale** -> `*read@weeks*` (da Lunedi a Sabato, letto dalla EEPROM).

## Ottenere l'APK

### A) Con GitHub (senza installare nulla)
1. Crea un repository su github.com e carica tutto il contenuto di questa cartella (compresa `.github`).
2. Scheda **Actions** -> "Build APK" -> attendi ~3 minuti.
3. Scarica l'artefatto **OrologioScolastico-apk** (contiene `app-debug.apk`).

### B) Con Android Studio
File -> Open -> scegli questa cartella -> Build -> Build APK(s).
L'APK è in `app/build/outputs/apk/debug/app-debug.apk`.

### C) Da riga di comando (con Android SDK installato)
`gradle assembleDebug` (Gradle 8.14+, JDK 17)

## Installazione sul telefono
Copia `app-debug.apk` sul telefono, aprilo e consenti "Installa app sconosciute" per quel gestore file.
Alla prima apertura concedi i permessi Bluetooth.

## Nota sul firmware (consigliata)
In `Time_BTH_To_RTC()` lo sketch legge il comando con `while(BTH.available())`. Se il controllo cade proprio
mentre arrivano i byte, la stringa può risultare incompleta (circa 1 volta su 15). L'app lo rileva confrontando
l'ora restituita dall'RTC e avvisa di ripetere l'invio. Per eliminare il problema, nello sketch sostituire il ciclo
di lettura con: `BTH.setTimeout(300); stringa = BTH.readStringUntil('\n');` e ricavare le cifre da `stringa`.
Inoltre `if(ch=="\n")` confronta un carattere con una stringa: usare `'\n'`.
