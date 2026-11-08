# Windows 11 sicherer machen: Administratorschutz einrichten

Schritt-für-Schritt-Anleitung für Windows 11 Home und Pro (24H2, 25H2, 26H2) – optional mit Neuinstallation und lokalem Konto.

**Version:** 1.0 (Entwurf)  
**Stand:** November 2026  

---
## 📖 Inhaltsverzeichnis
- [Teil 1 – Vorbereitung](#teil-1--vorbereitung-für-beide-wege)
- [Teil 2 – Neuinstallation](#teil-2--neuinstallation-optional)
- [Teil 3 – Konto umstellen](#teil-3--konto-umstellen-optional)
- [Teil 4 – Schutz einrichten](#teil-4--schutz-einrichten)
- [Teil 5 – Nacharbeiten & Extras](#teil-5--nacharbeiten--extras)
- [Teil 6 – Troubleshooting & Glossar](#teil-6--troubleshooting--glossar)
---
## So benutzt du diese Anleitung

Diese Anleitung führt dich in kleinen Schritten zu einem Windows, in dem Schadsoftware nicht mehr unbemerkt Administratorrechte bekommt. Du brauchst keine Vorkenntnisse – nur etwas Zeit, Sorgfalt und ein Konto mit Administratorrechten.

| Kennzeichen | Bedeutung |
| :--- | :--- |
| **`[GUI]`** | Bedienung mit der Maus in den Einstellungen oder Programmen. |
| **`[Normal]`** | Rechtsklick auf Start $\rightarrow$ **Terminal**. Befehl hineinkopieren, `Enter` drücken. |
| **`[Erhöht]`** | Rechtsklick auf Start $\rightarrow$ **Terminal (Administrator)**. Windows fragt nach Bestätigung. |

> [!NOTE]
> **Kurz erklärt:** Erläutert, was ein Schritt bewirkt – dient dem Verständnis, nicht dem Abarbeiten.

> [!WARNING]
> **Achtung:** Hier kann man Daten verlieren oder sich aussperren.

### Zwei Wege durch die Anleitung:
* **Weg 1 – Vorhandenes Windows behalten:** Teil 1 $\rightarrow$ (Teil 3) $\rightarrow$ Teil 4 $\rightarrow$ Teil 5.
* **Weg 2 – Windows neu installieren:** Teil 1 $\rightarrow$ Teil 2 $\rightarrow$ Teil 4 $\rightarrow$ Teil 5. *(Alle Daten auf dem Rechner werden dabei gelöscht!)*

---

> [!NOTE]
> **Kurz erklärt: Was ist der Administratorschutz?**  
> Normalerweise trägt ein Administratorkonto den „Generalschlüssel“ ständig bei sich – Windows fragt nur höflich nach („Ja/Nein“). Mit Administratorschutz bleibt der Schlüssel im Tresor: Erst wenn du per Fingerabdruck, Gesicht oder PIN bestätigst, gibt Windows für genau diese eine Aufgabe einen kurzlebigen Schlüssel aus – und zwar an ein getrenntes, von Windows verwaltetes Hilfskonto (`admin_...`). Schadsoftware, die heimlich in deiner Sitzung läuft, kommt an diesen Schlüssel nicht heran.

---

## Teil 1 – Vorbereitung (für beide Wege)

### `[GUI]` 1.1 Windows-Version prüfen
Drücke `Windows-Taste + R`, gib `winver` ein und drücke `Enter`. Es muss mindestens **Version 24H2** angezeigt werden. Ältere Versionen zuerst über Windows Update aktualisieren.

### `[GUI]` 1.2 Deine Daten sichern
Kopiere alles, was dir wichtig ist (Dokumente, Bilder, Musik, Videos, E-Mail-Archive, Passwortdatenbank), auf eine externe Festplatte. Danach die Festplatte vom Rechner trennen.

> [!NOTE]
> **Kurz erklärt: Warum vorher sichern?**  
> Die Einrichtung selbst löscht nichts – aber falls du dich vertippst oder die Neuinstallation wählst, ist eine Sicherung die einzige Rückversicherung. Eine Kopie, die nach dem Sichern vom Rechner getrennt wird, ist auch vor Erpressungssoftware (Ransomware) sicher.

### `[Normal]` 1.3 Liste deiner Programme sichern
`winget`, die Paketverwaltung von Windows, schreibt alle Programme, die es kennt, in eine Datei. Mit ihr installierst du sie später in einem Rutsch neu.

```powershell
winget export -o "$HOME\Downloads\meine-programme.json"
```

Kopiere die Datei `meine-programme.json` aus dem Ordner *Downloads* auf deine externe Festplatte.

### `[Erhöht]` 1.4 Wiederherstellungsschlüssel der Verschlüsselung notieren
Viele Rechner verschlüsseln die Festplatte automatisch (BitLocker bzw. „Geräteverschlüsselung“). Der folgende Befehl zeigt den 48-stelligen Schlüssel an, falls einer existiert.

```powershell
manage-bde -protectors -get C:
```

Steht dort ein „Numerisches Kennwort“, die Zahlenfolge aufschreiben (bzw. im Passwortmanager und auf Papier ablegen). Meldet der Befehl, dass keine Schutzvorrichtungen vorhanden sind, ist das Laufwerk nicht verschlüsselt.

> [!NOTE]
> **Kurz erklärt: Wozu der Schlüssel?**  
> Wenn Windows Veränderungen am Startvorgang bemerkt, fragt es nach diesem Schlüssel – sonst bleiben deine Daten gesperrt. Bisher lag er womöglich nur in deinem Microsoft-Konto. Wechselst du auf ein lokales Konto (Teil 3), musst du ihn selbst aufbewahren.

### `[GUI]` 1.5 Zugangsdaten bereitlegen
* Kennwort deines Windows-Kontos und – falls vorhanden – deines Microsoft-Kontos.
* Kennwörter für E-Mail, WLAN und wichtige Programme (am besten aus einem Passwortmanager).
* Bei **Weg 2**: Lizenzschlüssel gekaufter Programme. Windows selbst aktiviert sich meist automatisch wieder (digitale Lizenz).

---

## Teil 2 – Neuinstallation mit lokalem Konto (nur Weg 2)

> [!WARNING]
> **Achtung:** Bei der Neuinstallation wird die gesamte interne Festplatte gelöscht. Erst weitermachen, wenn Teil 1 vollständig erledigt ist!

### `[Erhöht]` 2.1 Installationsstick erstellen
Du brauchst einen USB-Stick mit mindestens 16 GB – sein Inhalt wird gelöscht.

**Methode A (Standard):** Am einfachsten mit dem Media Creation Tool: [microsoft.com/de-de/software-download/windows11](https://www.microsoft.com/de-de/software-download/windows11) $\rightarrow$ „Windows 11-Installationsmedien erstellen“ $\rightarrow$ herunterladen, starten, „USB-Speicherstick“ wählen.

**Methode B (Falls das Tool nicht startet):** Startet das Tool nicht (z. B. wegen eines bereits aktiven Administratorschutzes), erstellst du den Stick aus der ISO-Datei. Lade auf derselben Microsoft-Seite das „Windows 11-Datenträgerimage (ISO)“ herunter, lege die Datei in den Ordner `C:\ISO`, stecke **nur** den Ziel-USB-Stick an und führe folgendes Skript aus:

```powershell
$usb = @(Get-Disk | Where-Object BusType -eq 'USB')
$iso = @(Get-ChildItem 'C:\ISO\*.iso')

if ($usb.Count -ne 1) { throw "Abbruch: $($usb.Count) USB-Datentraeger gefunden, genau 1 erwartet." }
if ($iso.Count -ne 1) { throw "Abbruch: $($iso.Count) ISO-Dateien gefunden, genau 1 erwartet." }

$d = $usb[0]
"Ziel: $($d.FriendlyName), $([int]($d.Size / 1GB)) GB"

if ((Read-Host 'Stick wird GELOESCHT. Weiter? (JA)') -cne 'JA') { throw 'Abgebrochen' }

Clear-Disk -Number $d.Number -RemoveData -RemoveOEM -Confirm:$false
Initialize-Disk -Number $d.Number -PartitionStyle GPT
$size = [Math]::Min($d.Size - 16MB, 31GB)
$p = New-Partition -DiskNumber $d.Number -Size $size -AssignDriveLetter
Format-Volume -Partition $p -FileSystem FAT32 -NewFileSystemLabel 'WIN11' -Confirm:$false
$u = "$($p.DriveLetter):"
$m = ((Mount-DiskImage $iso[0].FullName -PassThru | Get-Volume).DriveLetter) + ':'

robocopy "$m\" "$u\" /E /XF install.wim /NFL /NDL /NJH /NP
$wim = "$m\sources\install.wim"
$swm = "$u\sources\install.swm"
dism /Split-Image /ImageFile:$wim /SWMFile:$swm /FileSize:3800
Dismount-DiskImage $iso[0].FullName | Out-Null
"Fertig. Stick-Laufwerk: $u"
```

> [!NOTE]
> **Kurz erklärt: Was macht das Skript?**  
> Es prüft, dass wirklich nur ein USB-Datenträger gesteckt ist, und fragt vor dem Löschen nach. Dann formatiert es den Stick passend für moderne Systeme (FAT32), kopiert die Windows-Dateien und teilt die über 4 GB große Installationsdatei in passende Stücke, da FAT32 keine Einzeldateien über 4 GB verarbeiten kann.

### `[Erhöht]` 2.2 Lokales Konto vorbereiten (Antwortdatei)
Windows 11 drängt bei der Installation auf ein Microsoft-Konto. Eine kleine „Antwortdatei“ auf dem Stick legt stattdessen ein lokales Konto an. Passe oben den Laufwerksbuchstaben an. Das Skript fragt Benutzernamen und ein Übergangskennwort ab.

```powershell
$stick = 'E:' # <-- Laufwerksbuchstabe des USB-Sticks anpassen

$name = Read-Host 'Benutzername (ohne Leerzeichen)'
$pw   = Read-Host 'Uebergangskennwort (nach Installation aendern)'

$xml = @"
<?xml version="1.0" encoding="utf-8"?>
<unattend xmlns="urn:schemas-microsoft-com:unattend"
xmlns:wcm="http://schemas.microsoft.com/WMIConfig/2002/State">
<settings pass="oobeSystem">
<component name="Microsoft-Windows-Shell-Setup" processorArchitecture="amd64"
publicKeyToken="31bf3856ad364e35" language="neutral" versionScope="nonSxS">
<OOBE>
<HideOnlineAccountScreens>true</HideOnlineAccountScreens>
<HideEULAPage>true</HideEULAPage>
<ProtectYourPC>3</ProtectYourPC>
</OOBE>
<UserAccounts><LocalAccounts>
<LocalAccount wcm:action="add">
<Name>$name</Name><Group>Administrators</Group>
<Password><Value>$pw</Value><PlainText>true</PlainText></Password>
</LocalAccount>
</LocalAccounts></UserAccounts>
</component>
</settings>
</unattend>
"@

Set-Content "$stick\autounattend.xml" $xml -Encoding utf8
Get-Item "$stick\autounattend.xml" | Select-Object FullName, Length
```

> [!NOTE]
> **Kurz erklärt: Was ist eine Antwortdatei?**  
> Windows liest beim Installieren automatisch eine Datei namens `autounattend.xml` vom Stick und beantwortet damit Fragen selbst. Diese hier überspringt die Zwangsanmeldung für das Online-Konto und schaltet Werbe- sowie Datensammel-Voreinstellungen ab. Alles andere – auch die Wahl der Festplatte – bleibt bei dir.  
> *Hinweis:* Das Kennwort steht temporär im Klartext auf dem Stick. Verwende daher nur ein Übergangskennwort!

### `[Erhöht]` 2.3 Treiber sichern (empfohlen)
Kopiert die Treiber deines jetzigen Windows-Systems auf den Stick. Fehlt nach der Neuinstallation z. B. der Netzwerktreiber, hast du ihn sofort griffbereit.

```powershell
$stick = 'E:' # <-- Laufwerksbuchstabe des Sticks anpassen
Export-WindowsDriver -Online -Destination "$stick\Drivers" | Measure-Object | Select-Object Count
```

### `[GUI]` 2.4 Vom Stick starten
Netzteil anschließen, externe Festplatten abziehen, Stick einstecken, Rechner neu starten und sofort die Taste für das Startmenü (Boot-Menü) mehrfach drücken:

| Hersteller | Taste (meist) |
| :--- | :--- |
| **Lenovo, Dell, Acer, Fujitsu** | `F12` |
| **HP** | `F9` (oder `Esc`, dann `F9`) |
| **Asus** | `F8` oder `Esc` |
| **MSI** | `F11` |
| **Microsoft Surface** | Lautstärke leiser gedrückt halten, dann einschalten |

*(Hinweis: Bei vielen Laptops muss zusätzlich die `Fn`-Taste gehalten werden, z. B. `Fn + F12`)*.  
Im Menü den USB-Stick auswählen.

### `[GUI]` 2.5 Windows installieren
1. Sprache und Tastatur: Deutsch $\rightarrow$ **Windows 11 installieren** $\rightarrow$ Haken setzen bei *„Ich stimme zu, dass alles gelöscht wird“*.
2. Product Key: **„Ich habe keinen Product Key“** wählen $\rightarrow$ genau die Edition wählen, die vorher installiert war (Home oder Pro).
3. Laufwerksauswahl: Alle vorhandenen Partitionen der internen Festplatte nacheinander löschen, bis nur noch **„Nicht zugewiesener Speicherplatz“** steht $\rightarrow$ **Weiter**.

> [!WARNING]
> **Achtung:** Steht bei der Laufwerksauswahl mehr als eine Festplatte, prüfe die Größe genau. Externe Platten vorher abzuziehen verhindert, dass versehentlich die Datensicherung gelöscht wird!

Windows installiert und startet mehrmals neu. Den Stick erst abziehen, wenn die Ersteinrichtung erscheint.

### `[GUI]` 2.6 Ersteinrichtung
* Land und Tastatur wählen, mit dem Internet verbinden.
* **Mit Antwortdatei:** Die Kontoabfrage entfällt; Windows meldet dich automatisch mit deinem lokalen Konto an.
* **Ohne Antwortdatei (nur Pro):** Bei der Kontoabfrage *Anmeldeoptionen* $\rightarrow$ *Stattdessen einer Domäne beitreten* $\rightarrow$ lokales Konto anlegen.
* **Ohne Antwortdatei (Home):** Zunächst mit dem Microsoft-Konto einrichten und später nach Teil 3 umstellen.
* Alle Datenschutz-Schalter auf **Aus** stellen.

### `[GUI]` 2.7 Nach dem ersten Start
1. **Übergangskennwort ändern:** `Strg + Alt + Entf` drücken $\rightarrow$ **Kennwort ändern**. Danach die Datei `autounattend.xml` vom Stick löschen.
2. Treiber vom Stick einspielen (siehe 2.7a), bevor Windows Update ausgeführt wird.
3. **Einstellungen** $\rightarrow$ **Windows Update** $\rightarrow$ **Erweiterte Optionen** $\rightarrow$ *„Updates für andere Microsoft-Produkte“* einschalten; danach so lange Updates installieren und neu starten, bis keine mehr ausstehend sind.
4. Gerätehersteller-Werkzeug für Treiber und BIOS-Updates installieren (z. B. Lenovo System Update, Dell Command Update, HP Support Assistant).

### `[Erhöht]` 2.7a Treiber vom Stick einspielen

```powershell
$stick = 'E:' # <-- Laufwerksbuchstabe des Sticks anpassen
pnputil /add-driver "$stick\Drivers\*.inf" /subdirs /install
```
Danach den Rechner neu starten.

---

## Teil 3 – Vorhandenes Konto auf lokal umstellen (optional, Weg 1)

### `[Normal]` 3.1 Kontotyp feststellen
Zeigt, ob du dich derzeit mit einem lokalen Konto oder einem Microsoft-Konto anmeldest:

```powershell
(Get-LocalUser $env:USERNAME).PrincipalSource
```
* `Local` = Bereits ein lokales Konto $\rightarrow$ **Teil 3 überspringen**.
* `MicrosoftAccount` = Anmeldung mit Microsoft-Konto.

> [!NOTE]
> **Kurz erklärt: Lokales Konto oder Microsoft-Konto?**  
> Beide Kontoarten funktionieren mit dem Administratorschutz. Ein lokales Konto hält deine Windows-Anmeldung unabhängig von Microsoft-Servern und sendet weniger Daten. Ein Microsoft-Konto synchronisiert dafür Einstellungen und speichert den BitLocker-Schlüssel online. Store, OneDrive oder Edge kannst du auch mit einem lokalen Konto nutzen – du meldest dich dann einfach nur in diesen einzelnen Apps an.

### `[GUI]` 3.2 Umstellen
1. Öffne **Einstellungen** $\rightarrow$ **Konten** $\rightarrow$ **Ihre Infos**.
2. Klicke auf **„Stattdessen mit einem lokalen Konto anmelden“**.
3. Benutzernamen und Kennwort festlegen $\rightarrow$ abmelden und neu anmelden.

> [!WARNING]
> **Achtung:** Vorher zwingend Schritt 1.4 ausführen! Nach der Umstellung liegt der Wiederherstellungsschlüssel für die Laufwerksverschlüsselung nicht mehr automatisch in deinem Microsoft-Konto.

---

## Teil 4 – Administratorschutz einrichten (für beide Wege)

### `[Erhöht]` 4.1 Voraussetzungen prüfen
Dein Konto muss Mitglied der Administratorgruppe sein. Der folgende Befehl listet alle Mitglieder auf – dein Benutzername muss in der Liste erscheinen:

```powershell
Get-LocalGroupMember -SID S-1-5-32-544 | Select-Object Name
```

### `[GUI]` 4.2 Windows Hello einrichten
Öffne **Einstellungen** $\rightarrow$ **Konten** $\rightarrow$ **Anmeldeoptionen**.
Richte eine **PIN** ein (mindestens 6 Ziffern), zusätzlich – falls Hardware vorhanden – Fingerabdruck oder Gesichtserkennung.

> [!NOTE]
> **Kurz erklärt: Warum Hello?**  
> Der Administratorschutz bestätigt jede Rechteerhöhung über Windows Hello. Die PIN ist dabei sehr sicher: Sie gilt nur für genau dieses eine Gerät und wird im Sicherheitschip (TPM) verifiziert – ein Angreifer kann mit der PIN auf einem anderen Gerät nichts anfangen.

### `[Erhöht]` 4.3 Notfall-Administratorkonto anlegen
Ein zweites Administratorkonto mit Kennwort dient als Rettungsanker, falls Windows Hello einmal versagen sollte. Lege ein langes Kennwort fest und bewahre es sicher auf (Passwortmanager und auf Papier!).

```powershell
$name = 'notfall-admin'
$pw = Read-Host "Kennwort fuer $name" -AsSecureString
New-LocalUser -Name $name -Password $pw -FullName 'Notfall-Administrator' -Description 'Nur fuer Notfaelle' -PasswordNeverExpires
Add-LocalGroupMember -SID S-1-5-32-544 -Member $name
Get-LocalGroupMember -SID S-1-5-32-544 | Select-Object Name
```
*Danach einmal mit dem Notfall-Konto anmelden und wieder abmelden.*

### `[Erhöht]` 4.4 Grundeinstellungen setzen
Strengste Benutzerkontensteuerung (UAC) für Administratoren setzen, automatische Sperre nach 10 Minuten Inaktivität aktivieren und Mindestlänge von 6 Zeichen für die Hello-PIN erzwingen:

```powershell
$sys = 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System'
$pin = 'HKLM:\SOFTWARE\Policies\Microsoft\PassportForWork\PINComplexity'

Set-ItemProperty -Path $sys -Name ConsentPromptBehaviorAdmin -Value 2 -Type DWord
Set-ItemProperty -Path $sys -Name InactivityTimeoutSecs -Value 600 -Type DWord

if (!(Test-Path $pin)) { New-Item -Path $pin -Force | Out-Null }
Set-ItemProperty -Path $pin -Name MinimumPINLength -Value 6 -Type DWord
```
*Ist deine bisherige PIN kürzer als 6 Stellen, ändere sie danach in den Anmeldeoptionen.*

> [!NOTE]
> **Kurz erklärt: Was ist die Benutzerkontensteuerung (UAC)?**  
> Das ist das Dialogfenster *„Möchten Sie zulassen, dass durch diese App Änderungen an Ihrem Gerät vorgenommen werden?“*. Mit Stufe 2 erscheint es bei jeder Systemänderung auf einem abgedunkelten, geschützten Bildschirm (Secure Desktop), den Schadsoftware nicht fernsteuern kann.

### `[Erhöht]` 4.5 Wiederherstellungspunkt erstellen
Ein Wiederherstellungspunkt speichert eine Momentaufnahme der Systemeinstellungen:

```powershell
powershell.exe -NoProfile -Command "Enable-ComputerRestore -Drive 'C:\'"
$c = "Checkpoint-Computer -Description 'Vor Adminschutz' -RestorePointType MODIFY_SETTINGS"
powershell.exe -NoProfile -Command $c
```

### `[Erhöht]` 4.6 Administratorschutz einschalten

**Weg A (über die Oberfläche):**  
Öffne **Windows-Sicherheit** $\rightarrow$ **Kontoschutz** $\rightarrow$ Schalter **Administratorschutz einschalten** $\rightarrow$ per Windows Hello bestätigen $\rightarrow$ Rechner neu starten.

**Weg B (über die Registry, falls der Schalter fehlt):**  
Fehlt der Schalter, installiere zuerst alle verfügbaren Windows-Updates. Erscheint er dann immer noch nicht, aktiviere den Modus manuell über die Registry:

```powershell
$sys = 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System'
Set-ItemProperty -Path $sys -Name TypeOfAdminApprovalMode -Value 2 -Type DWord
Restart-Computer
```

### `[Erhöht]` 4.7 Prüfen, ob der Schutz wirkt
Öffne nach dem Neustart ein **Terminal (Administrator)** (Bestätigung per Hello).

```powershell
whoami
$env:USERPROFILE
$sys = 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System'
(Get-ItemProperty -Path $sys).TypeOfAdminApprovalMode
```

* **Erfolg:** `whoami` zeigt `<PC-Name>\admin_<dein Name>`, der Profilordner endet auf `ADMIN_<dein Name>` und die letzte Zeile gibt `2` zurück.
* **Nicht aktiv:** Erscheint dein normaler Benutzername, ist der Schutz nicht aktiv – prüfe die Windows-Version und ausstehende Updates.

> [!NOTE]
> **Kurz erklärt: Warum heißt das Konto plötzlich `admin_...`?**  
> Das ist das von Windows verwaltete Hilfskonto. Erhöhte Programme laufen darin vollkommen getrennt von deiner normalen Sitzung und mit einem eigenen Profilordner. Darum sehen erhöhte Fenster manchmal andere Einstellungen als normale Fenster (siehe Teil 6).

### `[Erhöht]` 4.8 Rückweg, falls etwas nicht stimmt
Melde dich mit dem Notfallkonto aus Schritt 4.3 an, öffne ein **Terminal (Administrator)** und schalte den Schutz wieder ab. Alternativ kannst du den Wiederherstellungspunkt aus 4.5 nutzen (*Start* $\rightarrow$ *Wiederherstellungspunkt erstellen* $\rightarrow$ *Systemwiederherstellung*).

```powershell
$sys = 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System'
Set-ItemProperty -Path $sys -Name TypeOfAdminApprovalMode -Value 1 -Type DWord
Restart-Computer
```

---

## Teil 5 – Nacharbeiten und Extras

### `[Normal]` 5.1 Programme mit winget verwalten
`winget` installiert und aktualisiert Programme aus geprüften Quellen. Rufe es **immer im normalen Terminal** auf. Braucht eine Installation Adminrechte, fordert `winget` diese selbst per Hello an.

```powershell
winget upgrade                   # Zeigt verfuegbare Updates
winget upgrade --all             # Spielt alle Updates ein
winget install --id VideoLAN.VLC # Beispiel: Ein Programm installieren
```

> [!NOTE]
> **Kurz erklärt: Warum nicht im erhöhten Terminal ausführen?**  
> Im erhöhten Fenster läuft das Hilfskonto `admin_...`, dem die `winget`-App standardmäßig nicht zugeordnet ist. Aus dem normalen Fenster heraus erhöht `winget` nur den einzelnen Installationsprozess, der Rechte benötigt. Das ist auch aus Sicherheitssicht die sauberere Lösung.

### `[Normal]` 5.2 Programme nach einer Neuinstallation zurückholen
Kopiere die Datei `meine-programme.json` aus Schritt 1.3 von der externen Festplatte in deinen Ordner *Downloads*. Führe dann im normalen Terminal aus:

```powershell
winget import -i "$HOME\Downloads\meine-programme.json" --accept-package-agreements
```
*Programme, die `winget` nicht kennt, installierst du wie gewohnt manuell von der Herstellerseite.*

### `[Erhöht]` 5.3 Windows Terminal im erhöhten Modus registrieren
Startet „Terminal (Administrator)“ nur ein schlichtes blaues PowerShell-Fenster, kennt das Hilfskonto das neue Windows Terminal noch nicht. Einmalig registrieren und Einstellungen übertragen:

```powershell
# Registrieren des Windows Terminals für das Hilfskonto
Add-AppxPackage -RegisterByFamilyName -MainPackage Microsoft.WindowsTerminal_8wekyb3d8bbwe

# Einstellungen vom Hauptbenutzer in das Hilfskonto kopieren
$full = (Get-CimInstance Win32_ComputerSystem).UserName
$sid  = (New-Object Security.Principal.NTAccount $full).Translate([Security.Principal.SecurityIdentifier]).Value
$uhome = (Get-CimInstance Win32_UserProfile | Where-Object SID -eq $sid).LocalPath

$rel = 'AppData\Local\Packages\Microsoft.WindowsTerminal_8wekyb3d8bbwe\LocalState\settings.json'
Copy-Item "$uhome\$rel" "$env:USERPROFILE\$rel" -Force
```
*Danach Terminal (Administrator) neu öffnen und in den Einstellungen unter „Start“ die Standardterminalanwendung auf **Windows Terminal** festlegen.*

### `[Erhöht]` 5.4 Eigene Skripte schützen
Wer eigene PowerShell-Skripte erhöht oder automatisch (als `SYSTEM`) ausführt, sollte diese in einem Ordner ablegen, den nur Administratoren verändern dürfen:

```powershell
New-Item C:\Skripte -ItemType Directory -Force | Out-Null
icacls C:\Skripte /inheritance:r /grant "S-1-5-32-545:(OI)(CI)RX" "S-1-5-18:(OI)(CI)F" "S-1-5-32-544:(OI)(CI)F"
icacls C:\Skripte /setowner "S-1-5-32-544" /T /C
icacls C:\Skripte
```
*Erwartetes Ergebnis bei Rechteprüfung:* Nur SYSTEM (`S-1-5-18`) und Administratoren (`S-1-5-32-544`) besitzen Vollzugriff (`F`), normale Benutzer (`S-1-5-32-545`) haben nur Lese- und Ausführungsrechte (`RX`).

> [!NOTE]
> **Kurz erklärt: Warum ist das wichtig?**  
> Könnte dein normales Konto ein Skript verändern, das später im Hintergrund mit Adminrechten läuft, bräuchte Schadsoftware nur dieses Skript umzuschreiben – der Administratorschutz wäre damit ausgehebelt. **Wichtig:** Dateien in diesen Ordner immer *kopieren*, nicht *verschieben*, da beim Verschieben alte Berechtigungen beibehalten werden.

### `[Erhöht]` 5.5 Programme behandeln, die bei jeder Anmeldung nachfragen
Fragt Windows nach dem Anmelden jedes Mal nach Adminrechten, will ein Autostart-Programm erhöht starten (oft Treiber-Software für Touchpad, Grafik oder Audio).

1. Identifiziere die Anwendung im **Task-Manager** $\rightarrow$ **Autostart-Apps**.
2. Wird die Anwendung nicht benötigt, im Task-Manager deaktivieren.
3. Läuft die Anwendung als Hintergrunddienst, kann sie im erhöhten Terminal deaktiviert werden:

```powershell
Get-Service <Name>* | Select-Object Name, Status, StartType  # <Name> durch Teil des Programmnamens ersetzen
Set-Service <Dienstname> -StartupType Disabled
```

---

## Teil 6 – Typische Stolpersteine

| Beobachtung | Ursache & Lösung |
| :--- | :--- |
| **„Terminal (Administrator)“ öffnet nur ein einfaches PowerShell-Fenster** | Windows Terminal ist im Hilfskonto nicht registriert $\rightarrow$ **Schritt 5.3** ausführen. |
| **`winget` wird im erhöhten Fenster nicht gefunden** | `winget` stets im **normalen Terminal** verwenden $\rightarrow$ **Schritt 5.1**. |
| **Erhöhte Programme zeigen andere Einstellungen** | Programme laufen im Hilfskonto mit eigenem Profil. Einstellungen ggf. manuell übertragen (siehe Beispiel in **5.3**). |
| **Hello-Abfrage zeigt zuerst ein Kennwortfeld** | Finger auf den Sensor legen bzw. in die Kamera schauen. Über „Weitere Optionen“ kann die PIN gewählt werden. |
| **Ein Programm meldet „Bitte als Administrator anmelden“** | Das Programm erkennt das Hilfskonto-Konzept nicht (z. B. Media Creation Tool). Alternative suchen (**2.1**) oder Schutz ausnahmsweise kurz abschalten (**4.8**). |
| **In Skripten meldet `$env:USERNAME` den Wert „SYSTEM“ oder `admin_...`** | Eigenheit des Hilfskontos. Stattdessen `whoami` oder `[Environment]::UserName` verwenden. |
| **Bei jeder Anmeldung erscheint eine UAC-Abfrage** | Ein Autostart-Programm verlangt erhöhte Rechte $\rightarrow$ **Schritt 5.5** ausführen. |
| **Gesperrt nach falscher PIN-Eingabe** | Kennwort des Hauptkontos oder Notfallkonto (**4.3**) zur Anmeldung verwenden. |

---

## Kleines Glossar

| Begriff | Bedeutung |
| :--- | :--- |
| **Administrator** | Benutzerkonto, das Systemeinstellungen ändern und Programme für alle Benutzer installieren darf. |
| **Erhöhen (Elevation)** | Einem Programm kurzzeitig Administratorrechte gewähren (Bestätigungsdialog mit dem Schild-Symbol). |
| **Windows Hello** | Biometrische Anmeldung per PIN, Fingerabdruck oder Gesichtserkennung; fest an das physische Gerät gebunden. |
| **TPM (Trusted Platform Module)** | Physischer Sicherheitschip auf dem Mainboard, der kryptografische Schlüssel und die PIN-Prüfung vor Software-Angriffen abschirmt. |
| **BitLocker / Geräteverschlüsselung** | Verschlüsselt die gesamte Festplatte. Ohne erfolgreiche Anmeldung oder Wiederherstellungsschlüssel sind die Daten unlesbar. |
| **Registrierung (Registry)** | Zentrale Einstellungsdatenbank von Windows; viele Systemkonfigurationen werden hier hinterlegt. |
| **PowerShell / Terminal** | Textbasierte Befehlszeileneingabe; Windows Terminal ist das moderne Tab-basierte Programm dafür. |
| **winget** | Offizielle Paketverwaltung für Windows zum Installieren und Aktualisieren von Programmen per Befehl. |

---

## Checkliste

- [ ] **1.1 – 1.5:** Windows-Version geprüft, Daten und Programmliste gesichert, BitLocker-Schlüssel notiert.
- [ ] **2.1 – 2.7 (Nur Weg 2):** Installationsstick erstellt, Antwortdatei & Treiber hinterlegt, Installation durchgeführt, Übergangskennwort geändert, Updates eingespielt.
- [ ] **3.1 – 3.2 (Optional):** Benutzerkonto auf lokales Konto umgestellt.
- [ ] **4.1 – 4.2:** Admin-Konto vorhanden und Windows Hello eingerichtet.
- [ ] **4.3:** Notfall-Administratorkonto angelegt, Kennwort sicher aufbewahrt und Test-Anmeldung durchgeführt.
- [ ] **4.4 – 4.5:** Grundeinstellungen gesetzt, Wiederherstellungspunkt erstellt.
- [ ] **4.6 – 4.7:** Administratorschutz aktiviert: `whoami` zeigt `admin_...`, Modus steht auf `2`.
- [ ] **5.1 – 5.5:** Nacharbeiten erledigt (`winget`, Terminal-Registrierung, Skriptordner-Rechte, Autostarts).

---

> [!NOTE]
> **Hinweis:** Diese Anleitung beschreibt Funktionen von Windows 11 mit Stand **November 2026**. Microsoft passt Benutzeroberflächen und Bezeichnungen gelegentlich an; einzelne Menünamen können abweichen. Alle Schritte erfolgen auf eigene Verantwortung – durch Datensicherung, Notfallkonto und Wiederherstellungspunkt ist der Rückweg jedoch jederzeit abgesichert.
