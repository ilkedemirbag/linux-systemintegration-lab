# Fehleranalyse und Lösungen

## Fall 1: Zugriff verweigert – an welcher Stelle?
**Symptom:** `labuser` konnte `/home/ilke/linux-lab/permissions` nicht auflisten.

**Prüfung:**
```bash
namei -l /home/ilke/linux-lab/permissions
```

Relevante Beobachtungen:
```text
drwxr-x--- ilke ilke ilke
drwxrwxr-x ilke ilke linux-lab
drwx------ ilke ilke permissions
```

**Analyse:** Bereits `/home/ilke` erlaubte anderen Benutzern keinen Durchgang. Daher ließ sich mit diesem Versuch der Effekt der Rechte des letzten Verzeichnisses allein nicht belegen.

**Lösung für den Versuchsaufbau:** Ein eigenes Testverzeichnis unter `/tmp` verwenden. Die Rechte des persönlichen Home-Verzeichnisses blieben unverändert.

**Verifikation:** Bei 755 konnte `labuser` auflisten, aber keine Datei erstellen. Bei 700 scheiterte das Auflisten. Der Eigentümer konnte eine Datei erstellen.

## Fall 2: Neue SSH-Verbindung wird abgewiesen
**Ausgangszustand:** Erfolgreiche SSH-Anmeldung vom Mac. `ssh.service` war active (running), aber disabled; `ssh.socket` war aktiv und enabled und lauschte auf Port 22.

**Gezielte Fehlerauslösung in der UTM-Konsole:**
```bash
sudo systemctl stop ssh.socket ssh.service
```

**Test in einer neuen lokalen Mac-Terminal-Registerkarte:**
```bash
ssh -o ConnectTimeout=5 ilke@192.168.64.2
```

**Beobachtung:**
```text
ssh: connect to host 192.168.64.2 port 22: Connection refused
```

**Einordnung:** Im kontrollierten Versuch waren Service und Socket absichtlich gestoppt. Allgemein beweist „Connection refused“ allein nicht, dass ein Dienst gestoppt ist; die Ursache muss im jeweiligen Kontext geprüft werden.

**Wiederherstellung in der UTM-Konsole:**
```bash
sudo systemctl start ssh.socket ssh.service
```

**Verifikation auf dem Mac:**
```bash
ssh ilke@192.168.64.2
```

Eine neue Passwortanmeldung war erfolgreich; der Prompt `ilke@linux-lab:~$` erschien. Der Konsolenzugang blieb für die Wiederherstellung verfügbar. Es wurden keine dauerhaften Startoptionen geändert.

## Fall 3: Installationsmedium noch eingelegt
Nach dem Installationsabschluss erschien `Failed unmounting cdrom.mount` zusammen mit der Aufforderung, das Installationsmedium zu entfernen. Das Ubuntu-ISO wurde über UTM aus dem virtuellen CD/DVD-Laufwerk entfernt. Nach Enter startete das installierte Ubuntu bis zum Login.
