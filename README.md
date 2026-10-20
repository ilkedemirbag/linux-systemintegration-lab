# Linux Systemintegration Lab

Praktisches Lernprojekt zur Vorbereitung auf eine Ausbildung als Fachinformatiker/in für Systemintegration.

**Status:** Die unten beschriebenen praktischen Tests wurden am 20.10.2026 durchgeführt. Die Dokumentation wurde anschließend anhand der Konsolenausgaben und Testergebnisse erstellt.

## Ziel
Eine Linux-VM installieren, den Zugriff über SSH einrichten, Benutzer und Dateirechte untersuchen und einen gezielt ausgelösten SSH-Ausfall beheben.

## Umgebung
| Komponente | Konfiguration |
|---|---|
| Host | MacBook Pro, Apple M2 Max, 32 GB RAM; macOS Sonoma 14.5 |
| Virtualisierung | UTM / QEMU, ARM64 (aarch64) |
| Gast | Ubuntu Server 24.04.4 LTS |
| Ressourcen | 4 GB RAM, 30 GiB virtuelle Festplatte |
| Speicheraufteilung | EFI, /boot und LVM; etwa 13,5 GB für /, verbleibender LVM-Platz zunächst unzugeordnet |
| Netzwerk | Shared Network, DHCP, Schnittstelle enp0s1 |
| Hostname / Benutzer | linux-lab / ilke |
| Lokale Lab-Adressen beim Test | Mac: 192.168.64.1; Ubuntu: 192.168.64.2 |

Die CPU-Zuweisung wurde auf vier Kerne geplant, aber nach der Erstellung nicht separat geprüft. IP-Adressen können sich ändern. Die VM verwendet UTC; die lokale Uhrzeit in Berlin war am Testtag UTC+2.

## Durchgeführte Arbeiten
- Ubuntu Server installiert und den Start von der virtuellen Festplatte geprüft.
- Türkisches Q-Tastaturlayout konfiguriert.
- Benutzer, Hostname und Netzwerkadressen kontrolliert.
- ED25519-Hostschlüssel vor der ersten SSH-Anmeldung verglichen.
- Vom Mac über SSH angemeldet und die Verbindung geprüft.
- Testbenutzer `labuser` mit deaktivierter Passwortanmeldung erstellt.
- Lese- und Schreibzugriffe mit den Verzeichnisrechten 755 und 700 verglichen.
- Mit `namei -l` eine Zugriffssperre in einem übergeordneten Verzeichnis erkannt.
- SSH-Service und SSH-Socket gestoppt, Verbindungsfehler beobachtet und den Zugang wiederhergestellt.

## Ergebnisse
| Test | Ergebnis |
|---|---|
| Konsolenanmeldung | Erfolgreich |
| SSH-Anmeldung vom Mac | Erfolgreich |
| Verzeichnis mit 755: labuser darf auflisten | Erfolgreich |
| Verzeichnis mit 755: labuser darf nicht schreiben | Permission denied, wie erwartet |
| Verzeichnis mit 700: labuser darf nicht auflisten | Permission denied, wie erwartet |
| Eigentümer darf Datei erstellen | Erfolgreich |
| SSH nach Stoppen beider Units | Connection refused, wie erwartet |
| SSH nach erneutem Start | Erfolgreiche neue Anmeldung |

## Dokumentation
- [Aufbau und Durchführung](docs/durchfuehrung.md)
- [Fehleranalyse und Lösungen](docs/troubleshooting.md)
- [Testprotokoll mit Befehlen](docs/tests.md)

## Erkenntnisse
Ein Verzeichnis benötigt passende Rechte auf dem gesamten Pfad. Deshalb beweist ein fehlgeschlagener Zugriff allein noch nicht, welches Verzeichnis ihn verhindert. Ein Vergleich unter `/tmp` machte den Einfluss von 755 und 700 gezielt prüfbar.

Außerdem bedeutet `disabled` beim SSH-Service nicht, dass SSH gerade gestoppt ist: Der Service war aktiv und wurde über einen aktivierten Socket angestoßen. Für den kontrollierten Ausfall wurden beide Units gestoppt.

## Grenzen und nächste Schritte
Dies ist eine einzelne lokale Lern-VM, keine produktive Infrastruktur. Die Tests wurden manuell ausgeführt; es gibt keine automatisierte Bereitstellung. Sicherheitsupdates und eine Anmeldung mit eigenen SSH-Schlüsseln wurden in diesem Projekt noch nicht abgeschlossen. Die Testverzeichnisse unter `/tmp` sind temporär und können nach einem Neustart fehlen.

Die Dokumentation enthält übertragene Beobachtungen. Original-Screenshots sind dem Repository derzeit nicht beigefügt. Passwörter und private Schlüssel werden nicht veröffentlicht.
