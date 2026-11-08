# Windows 11 sicherer machen:<br>Administratorschutz einrichten

*Schritt-für-Schritt-Anleitung für Windows 11 Home und Pro (24H2, 25H2, 26H2) – optional mit Neuinstallation und lokalem Konto*  
*Version 1.2 · Entwurf · Stand November 2026*

## So benutzt du diese Anleitung

Diese Anleitung führt dich in kleinen Schritten zu einem Windows, in dem Schadsoftware nicht mehr unbemerkt Administratorrechte bekommt. Du brauchst keine Vorkenntnisse – nur etwas Zeit, Sorgfalt und ein Konto mit Administratorrechten.

| Kennzeichen | Bedeutung |
|---|---|
| **[GUI]** | Bedienung mit der Maus in Einstellungen oder Programmen. |
| **[Normal]** | Rechtsklick auf **Start** → **Terminal**. Befehl hineinkopieren, Enter drücken. |
| **[Erhöht]** | Rechtsklick auf **Start** → **Terminal (Administrator)**. Windows fragt nach Bestätigung. |
| Codeblock | Befehle zum Kopieren. Einfügen im Terminal mit Strg+V oder Rechtsklick. Fragt das Terminal wegen mehrerer Zeilen nach, „Trotzdem einfügen“ wählen. Alle Befehle stehen zusätzlich in der Datei **Befehle_Administratorschutz.txt** – von dort kopiert es sich am zuverlässigsten. |
| 💡 Kurz erklärt | „Kurz erklärt“: was ein Schritt bewirkt – zum Verstehen, nicht zum Abarbeiten. |
| ⚠️ Achtung | „Achtung“: Hier kann man Daten verlieren oder sich aussperren. |

> **Tipp zum Kopieren:** Öffne die Datei **Befehle_Administratorschutz.txt** gleich zu Beginn im **Editor** (Doppelklick oder Rechtsklick → Öffnen mit → Editor bzw. Notepad++) und lass sie neben dieser Anleitung geöffnet. Kopiere die Befehle **aus dem Editor**, nicht aus dem PDF oder Markdown-Viewer – manche Betrachter fügen beim Kopieren unsichtbare Formatierungszeichen, falsche Anführungszeichen oder zusätzliche Zeilenumbrüche ein, und dann meldet PowerShell Fehler. Die Blöcke in der Textdatei tragen dieselben Nummern wie die Schritte hier (z. B. „2.1 Installationsstick erstellen“).

**Zwei Wege durch die Anleitung:**

- **Weg 1 – vorhandenes Windows behalten:** Teil 1 → (Teil 3) → Teil 4 → Teil 5.
- **Weg 2 – Windows neu installieren:** Teil 1 → Teil 2 → Teil 4 → Teil 5. Alle Daten auf dem Rechner werden dabei gelöscht.

> 💡 **Kurz erklärt: Was ist der Administratorschutz?**  
> Normalerweise trägt ein Administratorkonto den „Generalschlüssel“ ständig bei sich – Windows fragt nur höflich nach („Ja/Nein“). Mit Administratorschutz bleibt der Schlüssel im Tresor: Erst wenn du per **Fingerabdruck, Gesicht oder PIN** bestätigst, gibt Windows für genau diese eine Aufgabe einen kurzlebigen Schlüssel aus – und zwar an ein getrenntes, von Windows verwaltetes Hilfskonto. Schadsoftware, die heimlich in deiner Sitzung läuft, kommt an diesen Schlüssel nicht heran. Wer Linux oder macOS kennt: Das Prinzip ähnelt **sudo** – Adminrechte gibt es nur auf ausdrückliche Bestätigung und nur für die eine Aufgabe.

## Teil 1 – Vorbereitung (für beide Wege)

### [GUI] 1.1 Windows-Version prüfen

Taste **Windows+R**, `winver` eingeben, Enter. Es muss mindestens **Version 24H2** angezeigt werden. Ältere Versionen zuerst über Windows Update aktualisieren.

### [GUI] 1.2 Deine Daten sichern

Kopiere alles, was dir wichtig ist (Dokumente, Bilder, Musik, Videos, E-Mail-Archive, Passwortdatenbank), auf eine **externe Festplatte**. Danach die Platte abziehen.

> 💡 **Kurz erklärt: Warum vorher sichern?**  
> Die Einrichtung selbst löscht nichts – aber falls du dich vertippst oder die Neuinstallation wählst, ist eine Sicherung die einzige Rückversicherung. Eine Kopie, die nach dem Sichern vom Rechner getrennt wird, ist auch vor Erpressungssoftware sicher.

### [Normal] 1.3 Liste deiner Programme sichern

winget, die Paketverwaltung von Windows, schreibt alle Programme, die es kennt, in eine Datei. Mit ihr installierst du sie später in einem Rutsch neu.

```powershell
winget export -o "$HOME\Downloads\meine-programme.json"
```

Die Datei aus dem Ordner Downloads auf die externe Platte kopieren.

### [Erhöht] 1.4 Wiederherstellungsschlüssel der Verschlüsselung notieren

Viele Rechner verschlüsseln die Festplatte automatisch (BitLocker bzw. „Geräteverschlüsselung“). Der folgende Befehl zeigt den 48-stelligen Schlüssel an, falls einer existiert.

```powershell
manage-bde -protectors -get C:
```

Steht dort ein „Numerisches Kennwort“, die 48-stellige Zahlenfolge **auf Papier notieren** und sicher aufbewahren – zusätzlich gern im Passwortmanager. Meldet der Befehl, dass keine Schutzvorrichtungen vorhanden sind, ist das Laufwerk nicht verschlüsselt.

> 💡 **Kurz erklärt: Wozu der Schlüssel?**  
> Wenn Windows Veränderungen am Startvorgang bemerkt, fragt es nach diesem Schlüssel – sonst bleiben deine Daten gesperrt. Bisher lag er womöglich nur in deinem Microsoft-Konto. Wechselst du auf ein lokales Konto (Teil 3), musst du ihn selbst aufbewahren. Warum Papier? Sperrt Windows, kommst du womöglich auch nicht an den Passwortmanager auf diesem Rechner heran.

### [GUI] 1.5 Zugangsdaten bereitlegen

- Kennwort deines Windows-Kontos und – falls vorhanden – deines Microsoft-Kontos.
- Kennwörter für E-Mail, WLAN und wichtige Programme (am besten aus einem Passwortmanager).
- Bei Weg 2: Lizenzschlüssel gekaufter Programme. Windows selbst aktiviert sich meist automatisch wieder (digitale Lizenz).

## Teil 2 – Neuinstallation mit lokalem Konto (nur Weg 2)

> ⚠️ **Achtung:** Bei der Neuinstallation wird die **gesamte interne Festplatte gelöscht**. Erst weitermachen, wenn Teil 1 vollständig erledigt ist.

### [Erhöht] 2.1 Installationsstick erstellen

Du brauchst einen USB-Stick mit mindestens 16 GB – sein Inhalt wird gelöscht. Am einfachsten mit dem **Media Creation Tool**: microsoft.com/de-de/software-download/windows11 → „Windows 11-Installationsmedien erstellen“ → herunterladen, starten, „USB-Speicherstick“ wählen.

**Startet das Tool nicht** (es verträgt sich nicht mit einem bereits aktiven Administratorschutz), erstellst du den Stick aus der ISO-Datei: Auf derselben Microsoft-Seite „Download von Windows 11-Datenträgerimages (ISO)“ herunterladen.

**Vorher den Ordner C:\ISO anlegen**, falls es ihn noch nicht gibt: im Explorer **Dieser PC** → Laufwerk **C:** öffnen → Rechtsklick → **Neu → Ordner** → Name **ISO**. Oder im Terminal: `New-Item C:\ISO -ItemType Directory -Force`. Dann die ISO-Datei dort hineinlegen, nur den Stick anstecken und:

```powershell
if (-not (Test-Path 'C:\ISO')) { throw 'Ordner C:\ISO fehlt - anlegen und ISO hineinlegen' }
$usb = @(Get-Disk | Where-Object BusType -eq USB)
$iso = @(Get-ChildItem 'C:\ISO\*.iso')
if ($usb.Count -ne 1) { throw "Abbruch: $($usb.Count) USB-Datentraeger, erwartet 1" }
if ($iso.Count -ne 1) { throw "Abbruch: $($iso.Count) ISO-Dateien, erwartet 1" }
$d = $usb[0]; "Ziel: $($d.FriendlyName), $([int]($d.Size/1GB)) GB"
if ((Read-Host 'Stick wird GELOESCHT. Weiter? (JA)') -cne 'JA') { throw 'Abgebrochen' }
Clear-Disk -Number $d.Number -RemoveData -RemoveOEM -Confirm:$false
Initialize-Disk -Number $d.Number -PartitionStyle GPT
$size = [Math]::Min($d.Size - 16MB, 31GB)
$p = New-Partition -DiskNumber $d.Number -Size $size -AssignDriveLetter
Format-Volume -Partition $p -FileSystem FAT32 -NewFileSystemLabel 'WIN11' -Confirm:$false
$u = "$($p.DriveLetter):"
$m = ((Mount-DiskImage $iso[0].FullName -PassThru) | Get-Volume).DriveLetter + ':'
robocopy "$m\" "$u\" /E /XF install.wim /NFL /NDL /NJH /NP
$wim = "$m\sources\install.wim"; $swm = "$u\sources\install.swm"
dism /Split-Image /ImageFile:$wim /SWMFile:$swm /FileSize:3800
Dismount-DiskImage $iso[0].FullName | Out-Null
"Fertig. Stick-Laufwerk: $u" 
```

> 💡 **Kurz erklärt: Was macht das Skript?**  
> Es prüft, dass wirklich nur ein USB-Datenträger steckt, und fragt vor dem Löschen nach. Dann formatiert es den Stick so, dass jeder moderne Rechner davon starten kann (FAT32), kopiert die Windows-Dateien und teilt die eine, über 4 GB große Datei in passende Stücke – FAT32 kann keine größeren Dateien speichern.

### [Erhöht] 2.2 Lokales Konto vorbereiten (Antwortdatei)

Windows 11 drängt bei der Installation auf ein Microsoft-Konto. Eine kleine „Antwortdatei“ auf dem Stick legt stattdessen ein **lokales Konto** an. Trage oben im Skript den Laufwerksbuchstaben deines Sticks ein (im Explorer nachsehen). Das Skript fragt Benutzernamen und ein Übergangskennwort ab.

```powershell
$stick = 'E:'    # <-- Laufwerksbuchstabe des Sticks anpassen
$name  = Read-Host 'Benutzername (ohne Leerzeichen)'
$pw    = Read-Host 'Uebergangskennwort (nach der Installation aendern)'
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

> 💡 **Kurz erklärt: Was ist eine Antwortdatei?**  
> Windows liest beim Installieren automatisch eine Datei namens autounattend.xml vom Stick und beantwortet damit Fragen selbst. Diese hier beantwortet nur die Kontofrage und schaltet Werbe- und Datensammel-Voreinstellungen ab. Alles andere – auch die Wahl der Festplatte – bleibt bei dir. Das Kennwort steht im Klartext auf dem Stick: Deshalb nur ein Übergangskennwort verwenden.

### [Erhöht] 2.3 Treiber sichern (empfohlen)

Kopiert die Treiber deines jetzigen Windows auf den Stick. Fehlt nach der Neuinstallation z. B. der Netzwerktreiber, hast du ihn griffbereit.

```powershell
$stick = 'E:'    # <-- Laufwerksbuchstabe des Sticks anpassen
Export-WindowsDriver -Online -Destination "$stick\Drivers" | Measure-Object | Select-Object Count
```

### [GUI] 2.4 Vom Stick starten

Netzteil anschließen, externe Festplatten **abziehen**, Stick einstecken, Rechner neu starten und sofort die Taste für das Startmenü drücken:

| Hersteller | Taste (meist) |
|---|---|
| Lenovo, Dell, Acer, Fujitsu | F12 |
| HP | F9 (oder Esc, dann F9) |
| Asus | F8 oder Esc |
| MSI | F11 |
| Microsoft Surface | Lautstärke leiser gedrückt halten, dann einschalten |

Bei vielen Notebooks liegen die F-Tasten auf Sonderfunktionen (Lautstärke, Helligkeit). Dann **Fn** gedrückt halten und zusätzlich die F-Taste drücken, z. B. **Fn+F12**. Im Menü den USB-Stick auswählen. Fragt der Rechner nach einem BIOS-Kennwort, ist eines gesetzt – das brauchst du dann.

### [GUI] 2.5 Windows installieren

- Sprache und Tastatur **Deutsch** → „Windows 11 installieren“ → Haken bei „Ich stimme zu, dass alles gelöscht wird“. Wer die gewohnte Oberfläche möchte: unten „Vorherige Version von Setup“.
- Product Key: „Ich habe keinen Product Key“ → die Edition wählen, die vorher installiert war (Home oder Pro).
- Laufwerksauswahl: die Partitionen der **internen** Festplatte löschen, dann „Nicht zugewiesener Speicher“ wählen → Weiter.
- Windows installiert und startet mehrmals neu. Den Stick erst abziehen, wenn die Ersteinrichtung erscheint.

> ⚠️ **Achtung:** Steht bei der Laufwerksauswahl mehr als eine Festplatte, prüfe die Größe genau. Externe Platten vorher abzuziehen verhindert, dass versehentlich die Sicherung gelöscht wird.

### [GUI] 2.6 Ersteinrichtung

- Land und Tastatur wählen, mit dem Internet verbinden.
- Mit Antwortdatei: Die Kontoabfrage entfällt; Windows meldet dich mit deinem lokalen Konto an.
- Ohne Antwortdatei, **nur Pro**: bei der Kontoabfrage „Anmeldeoptionen“ → „Stattdessen einer Domäne beitreten“ → lokales Konto anlegen.
- Ohne Antwortdatei unter **Home**: mit dem Microsoft-Konto einrichten und später nach Teil 3 umstellen.
- Alle Datenschutz-Schalter auf **Aus**.

### [GUI] 2.7 Nach dem ersten Start

- Übergangskennwort ändern: **Strg+Alt+Entf** → „Kennwort ändern“. Danach die Datei autounattend.xml auf dem Stick löschen.
- Treiber vom Stick einspielen, **bevor** Windows Update läuft (Befehl unten).
- Einstellungen → Windows Update → Erweiterte Optionen → „Updates für andere Microsoft-Produkte“ an; dann so lange Updates installieren und neu starten, bis keine mehr kommen.
- Gerätehersteller-Werkzeug für Treiber und BIOS installieren (z. B. Lenovo System Update, Dell Command Update, HP Support Assistant).

### [Erhöht] 2.7a Treiber vom Stick einspielen

```powershell
$stick = 'E:'    # <-- Laufwerksbuchstabe des Sticks anpassen
pnputil /add-driver "$stick\Drivers\*.inf" /subdirs /install
```

Danach neu starten.

### [Erhöht] 2.8 Verschlüsselung prüfen und neuen Schlüssel sichern

Mit der Neuinstallation ist der alte Wiederherstellungsschlüssel **ungültig**. Prüfe, ob das neue System verschlüsselt ist:

```powershell
manage-bde -status C:
manage-bde -protectors -get C:
```

**„Schutzstatus: Der Schutz ist aktiviert“:** Das neue „Numerische Kennwort“ auf Papier notieren (wie 1.4). Mit lokalem Konto lädt Windows es **nicht** automatisch ins Microsoft-Konto hoch.

**Nicht verschlüsselt, Windows Pro:** Systemsteuerung → BitLocker-Laufwerkverschlüsselung → „BitLocker aktivieren“, den Schlüssel dabei ausdrucken bzw. notieren.

**Nicht verschlüsselt, Windows Home:** Einstellungen → Datenschutz und Sicherheit → **Geräteverschlüsselung**, falls angeboten, einschalten, dann den Schlüssel mit dem Befehl oben anzeigen und notieren.

## Teil 3 – Vorhandenes Konto auf lokal umstellen (optional, Weg 1)

### [Normal] 3.1 Kontotyp feststellen

Zeigt, ob du dich mit einem lokalen Konto oder einem Microsoft-Konto bei Windows anmeldest.

```powershell
(Get-LocalUser $env:USERNAME).PrincipalSource
```

**Local** = lokales Konto, Teil 3 überspringen. **MicrosoftAccount** = Anmeldung mit Microsoft-Konto.

> 💡 **Kurz erklärt: Lokales Konto oder Microsoft-Konto?**  
> Beide funktionieren mit dem Administratorschutz. Ein lokales Konto hält deine Windows-Anmeldung unabhängig von Microsoft-Servern und sendet weniger Daten. Ein Microsoft-Konto synchronisiert dafür Einstellungen und speichert den Wiederherstellungsschlüssel online. Store, OneDrive oder Edge kannst du auch mit lokalem Konto nutzen – du meldest dich dann nur in diesen Apps an.

### [GUI] 3.2 Umstellen

Einstellungen → Konten → Ihre Infos → „Stattdessen mit einem lokalen Konto anmelden“ → Benutzername und Kennwort festlegen → abmelden und neu anmelden.

> ⚠️ **Achtung:** Vorher **Schritt 1.4** erledigen und den Schlüssel auf Papier haben. Nach der Umstellung liegt der Wiederherstellungsschlüssel nicht mehr automatisch in deinem Microsoft-Konto; neue Schlüssel (z. B. nach dem späteren Einschalten von BitLocker) musst du selbst notieren.

## Teil 4 – Administratorschutz einrichten (für beide Wege)

### [Erhöht] 4.1 Voraussetzungen prüfen

Dein Konto muss Administrator sein. Der Befehl zeigt alle Mitglieder der Administratorgruppe – dein Name muss dabei sein.

```powershell
Get-LocalGroupMember -SID 'S-1-5-32-544' | Select-Object Name
```

### [GUI] 4.2 Windows Hello einrichten

Einstellungen → Konten → Anmeldeoptionen: **PIN** einrichten (mindestens 6 Ziffern), dazu – falls vorhanden – **Fingerabdruck** oder **Gesichtserkennung**.

> 💡 **Kurz erklärt: Warum Hello?**  
> Der Administratorschutz bestätigt jede Erhöhung über Windows Hello. Die PIN ist dabei sicherer als sie klingt: Sie gilt nur für dieses eine Gerät und wird im Sicherheitschip (TPM) geprüft – ein Dieb kann mit ihr woanders nichts anfangen.

### [Erhöht] 4.3 Notfall-Administratorkonto anlegen

Ein zweites Administratorkonto mit Kennwort ist dein Rettungsweg, falls Hello einmal versagt. Lege ein langes Kennwort fest. Danach einmal mit diesem Konto anmelden und wieder abmelden.

```powershell
$name = 'notfall-admin'
$pw = Read-Host "Kennwort fuer $name" -AsSecureString
New-LocalUser -Name $name -Password $pw -FullName 'Notfall-Administrator' `
  -Description 'Nur fuer Notfaelle' -PasswordNeverExpires | Out-Null
Add-LocalGroupMember -SID 'S-1-5-32-544' -Member $name
Get-LocalGroupMember -SID 'S-1-5-32-544' | Select-Object Name
```

> ⚠️ **Achtung:** Das Kennwort des Notfallkontos **sofort auf Papier notieren** und sicher hinterlegen. Gerade wenn Hello versagt, ist der Passwortmanager auf diesem Rechner womöglich nicht erreichbar – ein Notfallkonto ohne greifbares Kennwort nützt nichts.

### [Erhöht] 4.4 Grundeinstellungen setzen

Strengste Benutzerkontensteuerung für Administratoren, automatische Sperre nach 10 Minuten ohne Eingabe und Mindestlänge 6 für die Hello-PIN.

```powershell
$sys = 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System'
$pin = 'HKLM:\SOFTWARE\Policies\Microsoft\PassportForWork\PINComplexity'
Set-ItemProperty $sys -Name ConsentPromptBehaviorAdmin -Value 2 -Type DWord
Set-ItemProperty $sys -Name InactivityTimeoutSecs -Value 600 -Type DWord
New-Item $pin -Force | Out-Null
Set-ItemProperty $pin -Name MinimumPINLength -Value 6 -Type DWord
```

Ist deine PIN kürzer als 6 Stellen, ändere sie danach in den Anmeldeoptionen.

> 💡 **Kurz erklärt: Was ist die Benutzerkontensteuerung (UAC)?**  
> Das ist das Fenster „Möchten Sie zulassen, dass durch diese App Änderungen …“. Mit Stufe 2 erscheint es bei jeder Systemänderung auf einem abgedunkelten, geschützten Bildschirm, den andere Programme nicht fernsteuern können.

### [Erhöht] 4.5 Wiederherstellungspunkt erstellen

Ein Wiederherstellungspunkt ist eine Momentaufnahme der Systemeinstellungen – damit kannst du den Zustand von vor der Umstellung zurückholen.

```powershell
powershell.exe -NoProfile -Command "Enable-ComputerRestore -Drive 'C:\'"
$c = "Checkpoint-Computer -Description 'vor Adminschutz' -RestorePointType MODIFY_SETTINGS"
powershell.exe -NoProfile -Command $c
```

### [Erhöht] 4.6 Administratorschutz einschalten

**Am einfachsten:** Windows-Sicherheit öffnen → **Kontoschutz** → Schalter **Administratorschutz** einschalten → mit Hello bestätigen → neu starten.

**Fehlt der Schalter**, zuerst alle Windows-Updates installieren. Erscheint er dann immer noch nicht, geht es über die Registrierung:

```powershell
$sys = 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System'
Set-ItemProperty $sys -Name TypeOfAdminApprovalMode -Value 2 -Type DWord
Restart-Computer
```

### [Erhöht] 4.7 Prüfen, ob er wirkt

Nach dem Neustart ein Terminal (Administrator) öffnen. Bei Fingerabdruck einfach den Finger auf den Sensor legen – das Kennwortfeld muss nicht angeklickt werden.

```powershell
whoami
$env:USERPROFILE
$sys = 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System'
(Get-ItemProperty $sys).TypeOfAdminApprovalMode
```

**Erfolg:** whoami zeigt `<PC-Name>\admin_<dein Name>`, der Profilordner endet auf `ADMIN_<dein Name>`, die letzte Zeile zeigt **2**. Erscheint dein normaler Name, ist der Schutz nicht aktiv – Windows-Version und Updates prüfen.

> 💡 **Kurz erklärt: Warum heißt das Konto plötzlich „admin_…“?**  
> Das ist das von Windows verwaltete Hilfskonto. Erhöhte Programme laufen darin – getrennt von deiner normalen Sitzung, mit eigenem Profilordner. Darum sehen erhöhte Fenster manchmal andere Einstellungen als normale (siehe Teil 6).

### [Erhöht] 4.8 Rückweg, falls etwas nicht stimmt

Mit dem Notfallkonto aus 4.3 anmelden, Terminal (Administrator) öffnen und den Schutz abschalten. Alternativ den Wiederherstellungspunkt aus 4.5 verwenden (Start → „Wiederherstellungspunkt erstellen“ → Systemwiederherstellung).

```powershell
$sys = 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System'
Set-ItemProperty $sys -Name TypeOfAdminApprovalMode -Value 1 -Type DWord
Restart-Computer
```

## Teil 5 – Nacharbeiten und Extras

### [Normal] 5.1 Programme mit winget verwalten

winget installiert und aktualisiert Programme aus geprüften Quellen. Rufe es **immer im normalen Terminal** auf – für Installationen, die Adminrechte brauchen, fragt es selbst per Hello nach.

```powershell
winget upgrade                          # zeigt verfuegbare Updates
winget upgrade --all                    # spielt alle Updates ein
winget install --id VideoLAN.VLC        # Beispiel: ein Programm installieren
```

> 💡 **Kurz erklärt: Warum nicht erhöht?**  
> Im erhöhten Fenster läuft das Hilfskonto admin_…, dem die winget-App nicht zugeordnet ist – dort fehlt winget oft ganz. Aus dem normalen Fenster heraus erhöht winget nur den einzelnen Installer, der es braucht. Das ist auch sicherer.

### [Normal] 5.2 Programme nach einer Neuinstallation zurückholen

Die Datei aus Schritt 1.3 von der externen Platte in den Ordner Downloads kopieren, dann:

```powershell
winget import -i "$HOME\Downloads\meine-programme.json" --accept-package-agreements
```

Programme, die winget nicht kennt, installierst du wie gewohnt von der Herstellerseite.

### [Erhöht] 5.3 Windows Terminal auch im erhöhten Modus

Startet „Terminal (Administrator)“ nur ein schlichtes PowerShell-Fenster, kennt das Hilfskonto Windows Terminal noch nicht. Einmal registrieren:

```powershell
Add-AppxPackage -RegisterByFamilyName -MainPackage Microsoft.WindowsTerminal_8wekyb3d8bbwe
```

Danach Terminal (Administrator) neu öffnen. In dessen Einstellungen → Start „Standardterminalanwendung = Windows Terminal“ wählen. Deine Einstellungen aus dem normalen Terminal übernimmst du so:

```powershell
$full = (Get-CimInstance Win32_ComputerSystem).UserName
$sid  = (New-Object Security.Principal.NTAccount $full).Translate(
          [Security.Principal.SecurityIdentifier]).Value
$uhome = (Get-CimInstance Win32_UserProfile | Where-Object SID -eq $sid).LocalPath
$rel = 'AppData\Local\Packages\Microsoft.WindowsTerminal_8wekyb3d8bbwe\LocalState\settings.json'
Copy-Item "$uhome\$rel" "$env:USERPROFILE\$rel" -Force
```

> 💡 **Kurz erklärt: Was machen die ersten drei Zeilen?**  
> Sie ermitteln den Profilordner des *angemeldeten* Benutzers. Im erhöhten Fenster zeigt $env:USERPROFILE nämlich auf das Hilfskonto – die Einstellungen liegen aber bei dir.

### [Erhöht] 5.4 Eigene Skripte schützen

Wer eigene PowerShell-Skripte erhöht oder automatisch (als „SYSTEM“) laufen lässt, legt sie in einen Ordner, den nur Administratoren ändern dürfen:

```powershell
New-Item C:\Skripte -ItemType Directory -Force | Out-Null
icacls C:\Skripte /inheritance:r /grant:r "*S-1-5-18:(OI)(CI)F" "*S-1-5-32-544:(OI)(CI)F" `
  "*S-1-5-32-545:(OI)(CI)RX"
icacls C:\Skripte /setowner "*S-1-5-32-544" /T /C
icacls C:\Skripte
```

Erwartet: nur SYSTEM und Administratoren mit Vollzugriff (F), Benutzer nur Lesen/Ausführen (RX).

> 💡 **Kurz erklärt: Warum ist das wichtig?**  
> Könnte dein normales Konto ein Skript ändern, das später mit Adminrechten läuft, bräuchte Schadsoftware nur dieses Skript umzuschreiben – der Administratorschutz wäre umgangen. Dateien in diesen Ordner deshalb **kopieren, nicht verschieben**: Beim Verschieben behält eine Datei ihre alten, lockeren Rechte.

### [Erhöht] 5.5 Programme, die bei jeder Anmeldung nachfragen

Fragt Windows nach dem Anmelden jedes Mal nach Adminrechten, will ein Autostart-Programm erhöht starten – oft ein Hilfsprogramm von Touchpad, Grafik oder Audio. Nicht routinemäßig bestätigen, sondern herausfinden, welches es ist: Task-Manager → Autostart-Apps, bzw. die Abfrage zeigt den Programmnamen.

Wird es nicht gebraucht, im Task-Manager deaktivieren. Läuft es als Dienst, im Terminal (Administrator):

```powershell
$teil = 'Synaptics'          # <-- Teil des Programmnamens anpassen
Get-Service "*$teil*" | Select-Object Name, Status, StartType
$dienst = 'SynTPEnhService'   # <-- Dienstname aus der Ausgabe oben eintragen
Set-Service $dienst -StartupType Disabled
```

## Teil 6 – Typische Stolpersteine

| Beobachtung | Ursache und Lösung |
|---|---|
| „Terminal (Administrator)“ öffnet nur ein einfaches Fenster | Windows Terminal ist im Hilfskonto nicht registriert – Schritt 5.3. |
| winget wird im erhöhten Fenster nicht gefunden | winget im normalen Terminal verwenden (5.1). |
| Erhöhte Programme zeigen andere Einstellungen | Sie laufen im Hilfskonto mit eigenem Profil. Einstellungen ggf. übertragen (Beispiel 5.3). |
| Hello-Abfrage zeigt zuerst ein Kennwortfeld | Finger auflegen bzw. in die Kamera schauen; „Weitere Optionen“ zeigt die PIN. |
| Ein Programm meldet „Bitte als Administrator anmelden“ | Es erkennt das Hilfskonto nicht (z. B. Media Creation Tool). Alternative suchen (2.1) oder ausnahmsweise Schutz kurz abschalten (4.8). |
| In Skripten meldet $env:USERNAME „SYSTEM“ | Eigenheit des Hilfskontos – stattdessen `whoami` oder `[Environment]::UserName` verwenden. |
| Bei jeder Anmeldung eine Abfrage | Autostart-Programm mit Adminrechten – Schritt 5.5. |
| Gesperrt nach falscher PIN | Kennwort des Kontos oder Notfallkonto (4.3) verwenden. |

## Kleines Glossar

| Begriff | Bedeutung |
|---|---|
| Administrator | Konto, das Systemeinstellungen ändern und Programme für alle installieren darf. |
| Erhöhen | Einem Programm kurzzeitig Administratorrechte geben (die Abfrage mit dem Schild-Symbol). |
| Windows Hello | Anmeldung per PIN, Fingerabdruck oder Gesicht; an dieses Gerät gebunden. |
| TPM | Sicherheitschip im Rechner, der Schlüssel und PIN-Prüfung vor Software abschirmt. |
| BitLocker / Geräteverschlüsselung | Verschlüsselt die Festplatte; ohne Anmeldung oder Wiederherstellungsschlüssel sind die Daten unlesbar. |
| Registrierung (Registry) | Zentrale Einstellungsdatenbank von Windows; die Befehle dieser Anleitung setzen dort Werte. |
| PowerShell / Terminal | Textfenster für Befehle; Windows Terminal ist das moderne Programm dafür. |
| winget | Paketverwaltung von Windows: installiert und aktualisiert Programme per Befehl. |

## Checkliste

- [ ] 1.1–1.5 Version geprüft, Daten und Programmliste gesichert, Wiederherstellungsschlüssel notiert
- [ ] 2.1–2.7 (nur Weg 2) Stick, Antwortdatei, Treiber, Installation, Kennwort geändert, Updates
- [ ] 3.1–3.2 (optional) Konto auf lokal umgestellt
- [ ] 4.1–4.2 Administratorkonto und Windows Hello vorhanden
- [ ] 4.3 Notfallkonto angelegt, Kennwort sicher aufbewahrt, einmal angemeldet
- [ ] 4.4–4.5 Grundeinstellungen gesetzt, Wiederherstellungspunkt erstellt
- [ ] 4.6–4.7 Administratorschutz aktiv: whoami zeigt admin_…, Wert 2
- [ ] 5.1–5.5 winget, Terminal, Skriptordner, Autostarts erledigt

Hinweis: Diese Anleitung beschreibt Funktionen von Windows 11 mit Stand November 2026. Microsoft ändert Oberflächen und Funktionen gelegentlich; einzelne Menünamen können abweichen. Alle Schritte erfolgen auf eigene Verantwortung – mit Sicherung, Notfallkonto und Wiederherstellungspunkt ist der Rückweg jederzeit offen.
