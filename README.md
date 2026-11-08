# Windows 11 Administratorschutz – Einrichtungsanleitung

![Windows 11](https://img.shields.io/badge/OS-Windows%2011%20%2824H2%2B%29-blue?style=flat&logo=windows)
![Status](https://img.shields.io/badge/Status-Entwurf%20v1.2-orange?style=flat)
![License](https://img.shields.io/badge/License-MIT-green?style=flat)

Eine praxisnahe Schritt-für-Schritt-Anleitung, um Windows 11 Home und Pro mit dem **Administratorschutz** (*Administrator Protection*) gegen unbemerkte Rechteausweitung durch Schadsoftware abzusichern – optional mit Neuinstallation und lokalem Benutzerkonto. Geschrieben für Einsteiger, mit Erklärungsboxen für alle, die verstehen wollen, *warum* ein Schritt nötig ist.

---

## 🎯 Worum geht es?

Mit der klassischen Benutzerkontensteuerung (UAC) arbeitet ein Administratorkonto zwar mit eingeschränktem Token – das volle Administrator-Token liegt aber in derselben Sitzung bereit und wird nach einem einfachen „Ja“-Klick freigegeben. Bekannte UAC-Umgehungen setzen genau dort an.

Der **Administratorschutz** trennt beides konsequent:

- Erhöhte Rechte gibt es erst nach Bestätigung per **Windows Hello** (PIN, Fingerabdruck oder Gesicht).
- Windows stellt dafür ein **kurzlebiges Admin-Token** in einem separaten, systemverwalteten Konto (`admin_<Benutzername>`) aus – nur für die eine Aufgabe.
- Schadsoftware in der normalen Sitzung kommt an dieses Token nicht heran.

> Wer Linux oder macOS kennt: Das Prinzip ähnelt `sudo` – Rechte nur auf ausdrückliche Bestätigung und nur für den einen Vorgang.

### 💡 Gut zu wissen: Erhöhte Programme leben in einem eigenen Profil

Weil erhöhte Prozesse im Hilfskonto `admin_…` laufen, haben sie ein **eigenes Benutzerprofil** (eigene Registry-Zweige unter HKCU, eigenes `%APPDATA%`). Typische Folgen, die die Anleitung behandelt:

| Beobachtung | Lösung in der Anleitung |
|---|---|
| `winget` fehlt im Terminal (Administrator) | winget im normalen Terminal nutzen (Teil 5.1) |
| „Terminal (Administrator)“ öffnet nur ein schlichtes Fenster | Windows Terminal für das Hilfskonto registrieren (Teil 5.3) |
| `$env:USERNAME` meldet `SYSTEM` | `whoami` bzw. `[Environment]::UserName` verwenden (Teil 6) |
| Media Creation Tool verlangt eine Anmeldung als Admin | Installationsstick aus der ISO erstellen (Teil 2.1) |

---

## 📚 Dateien in diesem Repository

| Datei | Inhalt |
|---|---|
| 👉 [**Windows_11_Administratorschutz_Anleitung.md**](./Windows_11_Administratorschutz_Anleitung.md) | Die vollständige Anleitung zum Lesen auf GitHub |
| [Windows_11_Administratorschutz_Anleitung.pdf](./Windows_11_Administratorschutz_Anleitung.pdf) | Dieselbe Anleitung als druckbares PDF (DIN A4) |
| [Befehle_Administratorschutz.txt](./Befehle_Administratorschutz.txt) | Alle PowerShell-Befehle, nach Schritten gegliedert – **zum Kopieren empfohlen** |

> [!TIP]
> Öffne `Befehle_Administratorschutz.txt` parallel im Editor und kopiere die Befehle von dort. Manche PDF- und Markdown-Betrachter fügen beim Kopieren unsichtbare Formatierungszeichen ein, die PowerShell-Fehler verursachen.

### Aufbau der Anleitung

1. **Teil 1 – Vorbereitung:** Daten sichern, Programmliste mit `winget` exportieren, BitLocker-Wiederherstellungsschlüssel auf Papier sichern.
2. **Teil 2 – Neuinstallation (optional):** Installationsstick, Antwortdatei `autounattend.xml` für ein lokales Konto, Treiber-Sicherung, Verschlüsselung nach der Installation.
3. **Teil 3 – Konto umstellen (optional):** vom Microsoft-Konto auf ein lokales Konto.
4. **Teil 4 – Administratorschutz einrichten:** Windows Hello, Notfall-Administratorkonto, UAC-Grundwerte, Wiederherstellungspunkt, Aktivierung, Prüfung, Rückweg.
5. **Teil 5 – Nacharbeiten & Extras:** `winget`, Windows Terminal im erhöhten Modus, eigene Skriptordner schützen, Autostarts mit Adminrechten.
6. **Teil 6 – Stolpersteine & Glossar.**

Zwei Wege führen durch die Anleitung: **vorhandenes Windows behalten** (Teil 1 → 3 → 4 → 5) oder **neu installieren** (Teil 1 → 2 → 4 → 5).

---

## ⚡ Voraussetzungen

- **Betriebssystem:** Windows 11 Home oder Pro, Version **24H2 oder neuer**, mit allen aktuellen Updates.
- **Konto:** Benutzerkonto mit Administratorrechten.
- **Windows Hello:** Eine **PIN genügt**; Fingerabdrucksensor oder Hello-Kamera sind optional.
- **Zeitaufwand:** ca. 20–45 Minuten ohne Neuinstallation, ca. 2–3 Stunden mit.

### Kurzfassung für Erfahrene

Bevorzugt über **Windows-Sicherheit → Kontoschutz → Administratorschutz**. Alternativ per Registry (Terminal als Administrator, anschließend **Neustart**):

```powershell
$sys = 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System'

# Aktivieren (2 = Administratorschutz)
Set-ItemProperty $sys -Name TypeOfAdminApprovalMode -Value 2 -Type DWord
Restart-Computer

# Rückweg zur klassischen UAC (1 = Administratorgenehmigungsmodus)
Set-ItemProperty $sys -Name TypeOfAdminApprovalMode -Value 1 -Type DWord
Restart-Computer
```

Prüfen nach dem Neustart im Terminal (Administrator): `whoami` muss `<PC-Name>\admin_<Benutzername>` zeigen.

> [!WARNING]
> Vor der Aktivierung unbedingt ein **Notfall-Administratorkonto** mit Kennwort anlegen (Kennwort auf Papier notieren!) und einen **Wiederherstellungspunkt** erstellen – siehe Teil 4.3 und 4.5. Ohne funktionierendes Windows Hello und ohne zweites Admin-Konto kannst du dich sonst von Administratoraufgaben aussperren.

---

## ✅ Getestet

| System | Stand |
|---|---|
| Windows 11 Pro 26H2 (Build 26300) | vollständig durchgespielt |
| Windows 11 Home | **noch nicht verifiziert** – Rückmeldungen willkommen |

Alle PowerShell-Blöcke werden vor jeder Version mit dem PowerShell-Parser auf Syntaxfehler geprüft.

---

## 🤝 Mitwirken / Feedback

Verbesserungsvorschläge, Fehlerfunde in den Skripten oder Erfahrungen mit anderen Windows-Editionen und Geräten sind herzlich willkommen – einfach ein **Issue** eröffnen oder einen **Pull Request** senden.

Bitte bei Fehlermeldungen angeben: Windows-Edition und -Version (`winver`), der betroffene Schritt und die vollständige Fehlermeldung.

---

## ⚖️ Haftungsausschluss

Diese Anleitung wurde sorgfältig erstellt und getestet, erfolgt aber ohne Gewähr. Microsoft ändert Funktionen und Menünamen gelegentlich. Änderungen an Systemeinstellungen erfolgen auf eigene Verantwortung – mit Datensicherung, Notfallkonto und Wiederherstellungspunkt bleibt der Rückweg jederzeit offen.

## 📄 Lizenz

Dieses Projekt steht unter der [MIT-Lizenz](./LICENSE) – frei nutzbar, anpassbar und weiterzugeben.
