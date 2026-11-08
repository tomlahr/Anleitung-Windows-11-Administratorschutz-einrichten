# Windows 11 Administratorschutz – Einrichtungsanleitung

![Windows 11](https://img.shields.io/badge/OS-Windows%2011%20(24H2%2B)-blue?style=flat-svg&logo=windows)
![License](https://img.shields.io/badge/License-MIT-green.svg)
![Status](https://img.shields.io/badge/Status-Entwurf%20v1.0-orange)

Eine praxisnahe Schritt-für-Schritt-Anleitung, um Windows 11 Home und Pro vor unbemerkter Rechteausweitung durch Schadsoftware zu schützen.

---

## 🎯 Über dieses Projekt

Standardmäßig arbeiten Administratorkonten unter Windows 11 mit ständigen Root-Rechten – Schadsoftware kann dies unter Umständen ausnutzen. Der **Administratorschutz** trennt die administrative Ebene strikt ab: Erhöhte Rechte werden erst nach expliziter Authentifizierung über **Windows Hello** (PIN, Fingerabdruck oder Gesicht) und nur temporär an ein isoliertes Hilfskonto (`admin_...`) vergeben.

Dieses Repository bietet eine leicht verständliche, gründliche Anleitung, um diese Schutzfunktion sicher einzurichten – wahlweise auf einem bestehenden System oder im Rahmen einer sauberen Neuinstallation.

---

## 📚 Inhalt & Dokumentation

Die vollständige Anleitung findest du in der Hauptdatei:

👉 **[Zur Schritt-für-Schritt-Anleitung (Windows11_Administratorschutz.md)](./Windows11_Administratorschutz.md)**

### Übersicht der Abschnitte:

1. **Teil 1 – Vorbereitung:** Daten sichern, Programme exportieren (`winget`) & BitLocker-Schlüssel sichern.
2. **Teil 2 – Neuinstallation (Optional):** Boot-Stick mit lokaler Konto-Antwortdatei (`autounattend.xml`) und Treiber-Backup erstellen.
3. **Teil 3 – Konto umstellen (Optional):** Wechsel von Microsoft-Konto auf ein lokales Benutzerkonto.
4. **Teil 4 – Schutz einrichten:** Windows Hello konfigurieren, Notfall-Admin anlegen, Registry/UAC anpassen und Schutz aktivieren.
5. **Teil 5 – Nacharbeiten & Extras:** `winget`-Paketverwaltung nutzen, Skript-Ordner absichern, Autostarts aufräumen.
6. **Teil 6 – Troubleshooting & Glossar:** Lösungen für häufige Stolpersteine und Begriffserklärungen.

---

## ⚡ Schnellstart / Voraussetzungen

* **Betriebssystem:** Windows 11 Home oder Pro (Version **24H2** oder neuer).
* **Konto:** Benutzerkonto mit Administratorrechten.
* **Hardware:** TPM 2.0 & unterstützte Hardware für Windows Hello (PIN, Sensor oder Kamera).
* **Zeitaufwand:** ca. 20–45 Minuten (ohne Neuinstallation).

---

## 🛡️ Sicherheitshinweis

> [!WARNING]
> Ändere Systemeinstellungen und Registry-Einträge stets mit Sorgfalt. Lege vor der Aktivierung des Schutzmodus unbedingt das in der Anleitung beschriebene **Notfall-Administratorkonto** sowie einen **Wiederherstellungspunkt** an, um ein versehentliches Aussperren zu verhindern.

---

## 🤝 Mitwirken / Feedback

Verbesserungsvorschläge, Fehlerfunde in den PowerShell-Skripten oder Ergänzungen sind herzlich willkommen! Erstelle einfach ein **Issue** oder sende einen **Pull Request**.

---

## 📄 Lizenz

Dieses Projekt steht unter der [MIT-Lizenz](LICENSE) – frei nutzbar, anpassbar und weiterzugeben.
