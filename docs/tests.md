# Manuelles Testprotokoll

Durchgeführt am 20.10.2026. Ergebnisse basieren auf beobachteten Konsolenausgaben und den bestätigten Vergleichstests. Keine automatisierte Testsuite.

| Nr. | Prüfung | Beobachtetes Ergebnis |
|---|---|---|
| 1 | Anmeldung nach Installation | Ubuntu 24.04.4 LTS, Prompt ilke@linux-lab |
| 2 | whoami / hostname | ilke / linux-lab |
| 3 | ip -br address | enp0s1 UP, 192.168.64.2/24 |
| 4 | Türkische Zeichen nach setupcon | Eingabe erfolgreich |
| 5 | Hostschlüsselvergleich | ED25519-Fingerabdrücke stimmen überein |
| 6 | SSH vom Mac | Anmeldung erfolgreich |
| 7 | echo "$SSH_CONNECTION" | 192.168.64.1 51197 192.168.64.2 22 |
| 8 | Eigentümer erstellt test.txt im eigenen 700-Verzeichnis | Erfolgreich |
| 9 | labuser listet Verzeichnis unter /home/ilke | Permission denied; übergeordnete Sperre erkannt |
| 10 | labuser listet isoliertes 755-Testverzeichnis | Erfolgreich, leere Ausgabe |
| 11 | labuser listet isoliertes 700-Testverzeichnis | Permission denied |
| 12 | labuser erstellt Datei im 755-Testverzeichnis | Permission denied |
| 13 | Eigentümer erstellt ilke.txt dort | Erfolgreich; nur ilke.txt gelistet |
| 14 | Neue SSH-Verbindung bei gestopptem Service und Socket | Connection refused |
| 15 | Neue SSH-Verbindung nach Wiederherstellung | Anmeldung erfolgreich |

## Vergleichstest nachvollziehen
Nur in der eigenen Lern-VM ausführen. Der Testbenutzer muss bereits existieren. Falls das Testverzeichnis bereits vorhanden ist, zuerst dessen Besitzer und Inhalt prüfen, statt es erneut anzulegen oder fremde Daten zu ändern.

```bash
mkdir -m 755 /tmp/ilke-permission-lab
sudo -u labuser ls /tmp/ilke-permission-lab
chmod 700 /tmp/ilke-permission-lab
sudo -u labuser ls /tmp/ilke-permission-lab
chmod 755 /tmp/ilke-permission-lab
sudo -u labuser touch /tmp/ilke-permission-lab/labuser.txt
touch /tmp/ilke-permission-lab/ilke.txt
ls -l /tmp/ilke-permission-lab
```

Erwartete Reihenfolge: Auflisten erlaubt, Auflisten verweigert, Schreiben für labuser verweigert, Schreiben für ilke erfolgreich. Die Testdatei war leer und hatte beim Test die Rechte `-rw-rw-r--`. Diese Dateirechte sind von den Verzeichnisrechten zu unterscheiden.

## Nicht geprüft
- Dauerhafte Erreichbarkeit nach Wechsel des WLANs oder Änderung der DHCP-Adresse.
- SSH-Anmeldung mit Benutzer-Schlüsseln.
- Vollständiger Abschluss aller verfügbaren Systemupdates.
- Produktiver Betrieb oder automatisierte Wiederherstellung.
