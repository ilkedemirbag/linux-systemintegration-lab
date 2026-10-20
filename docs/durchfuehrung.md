# Aufbau und Durchführung

## 1. Ubuntu installieren
In UTM wurde eine ARM64-VM mit 4 GB RAM und 30 GiB Disk erstellt. Als Installationsmedium wurde `ubuntu-24.04.4-live-server-arm64.iso` verwendet. Gewählt wurden Ubuntu Server, DHCP und die geführte Partitionierung mit LVM, ohne LUKS-Verschlüsselung. Proxy, Ubuntu Pro und zusätzliche Server-Snaps wurden übersprungen. OpenSSH wurde mit Passwortanmeldung installiert.

Nach Abschluss wurde das ISO aus dem virtuellen CD/DVD-Laufwerk entfernt. Anschließend startete Ubuntu vom installierten System und die Anmeldung als `ilke` war erfolgreich.

## 2. Türkisches Q-Layout
`sudo loadkeys trq` schlug fehl, weil die Zuordnung nicht gefunden wurde. Der anschließende Versuch mit `tr` erzeugte Warnungen. Die dauerhafte Einstellung erfolgte über:

```bash
sudo dpkg-reconfigure keyboard-configuration
sudo setupcon
```

Auswahl: Generic 105-key PC, Turkish, Standard für AltGr, kein Compose-Key. Die Eingabe türkischer Zeichen wurde danach erfolgreich geprüft. Ein Neustarttest des Layouts wurde nicht separat dokumentiert.

## 3. System und Netzwerk kontrollieren
```bash
whoami
hostname
ip -br address
```

Beobachtet: Benutzer `ilke`, Hostname `linux-lab`, Schnittstelle `enp0s1` mit Status UP und IPv4-Adresse `192.168.64.2/24`.

## 4. SSH-Verbindung
Auf dem Mac:
```bash
ssh ilke@192.168.64.2
```

Vor der Bestätigung wurde in der UTM-Konsole der öffentliche Hostschlüssel geprüft:
```bash
ssh-keygen -lf /etc/ssh/ssh_host_ed25519_key.pub
```

Die Fingerabdrücke stimmten überein. Nach Bestätigung und Passwortanmeldung war eine SSH-Sitzung möglich. Ein erster Versuch wurde vor erfolgreicher Anmeldung geschlossen; die Ursache wurde nicht ermittelt. Der erneute Versuch war erfolgreich.

In der SSH-Sitzung:
```bash
echo "$SSH_CONNECTION"
```

Beobachtete Ausgabe:
```text
192.168.64.1 51197 192.168.64.2 22
```

Reihenfolge: Client-IP, temporärer Client-Port, Server-IP, Server-Port. Die Nummer 51197 gilt nur für diese Sitzung.

## 5. Benutzer und Rechte
Ein Arbeitsverzeichnis wurde erstellt und auf 700 gesetzt:
```bash
mkdir -p ~/linux-lab/permissions
chmod 700 ~/linux-lab/permissions
touch ~/linux-lab/permissions/test.txt
sudo adduser --disabled-password --gecos "" labuser
```

Der Eigentümer konnte eine Datei erstellen. Der Zugriff als `labuser` scheiterte. Mit `namei -l` wurde erkannt, dass bereits `/home/ilke` den Pfad für andere Benutzer sperrte. Ein separater Test unter `/tmp` isolierte anschließend die Wirkung der Verzeichnisrechte. Die Befehle stehen im Testprotokoll.

## 6. Kontrollierter SSH-Ausfall
Service und Socket wurden geprüft und über die UTM-Konsole gestoppt. Eine neue Verbindung vom Mac wurde abgewiesen. Nach dem Start beider Units über die Konsole funktionierte eine neue SSH-Anmeldung. Details stehen in der Fehleranalyse.
