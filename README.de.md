# Portable Betterbird Updater

[English](README.md) | **Deutsch**

Ein kleines Windows-Programm, das **[Betterbird](https://www.betterbird.eu)** – die verbesserte Version von Thunderbird – in den beiden aktuellen Versionen **Release und Previous** herunterlädt und als **portable Version** einrichtet: Programmdateien in `App\`, E-Mails, Konten, Einstellungen und Add-ons in `Profile\`. Kein Installer, kein Dienst, nichts in `%APPDATA%` oder in der Registry.

![Hauptfenster](docs/screenshot.png)

## Download

**[Neueste Version](https://github.com/naderi/portable-betterbird-updater/releases/latest)** – `BetterbirdUpdater.exe` herunterladen, in einen leeren Ordner legen, in dem Betterbird landen soll (z. B. `D:\Apps\Betterbird`), und starten. Keine Installation nötig.

**Voraussetzungen:** Windows 11 (x64) mit .NET Framework 4.5 oder neuer (vorinstalliert).

## So funktioniert es

1. Betterbird bietet zwei Versionen an, beide auf Basis von Thunderbird ESR: **Release** (die aktuelle Hauptversion, z. B. 153) und **Previous** (die vorherige, z. B. 140). Der Updater fragt `https://www.betterbird.eu/downloads/get.php` nach dem aktuellen Setup beider Versionen; die Version steht in dessen Dateinamen.
2. Heruntergeladen wird das offizielle Setup `betterbird-<Version>.<Sprache>.win64.installer.exe` von betterbird.eu (ca. 90 MB). Betterbird wird für jede Sprache einzeln gebaut, daher bestimmt die Auswahl **Sprache**, welches Setup geladen wird.
3. Der Download wird per **SHA-256** gegen die Prüfsummendatei `sha256-<Hauptversion>.txt` von Betterbird geprüft.
4. Das Setup ist ein 7z-Selbstentpacker. Es wird **nicht ausgeführt**, sondern mit dem in Windows enthaltenen `tar.exe` entpackt; der Ordner `core\` (`betterbird.exe`, `xul.dll` …) wird zu `App\`.
5. Das Deinstallationsprogramm wird aus `App\` entfernt, und `App\distribution\policies.json` schaltet die eingebaute Update-Suche ab (siehe unten).

> Betterbird gibt es **nur für x64**, daher gibt es keine x86-Versionen. Sprachen mit Windows-Setup: Deutsch, Englisch (USA), Spanisch, Französisch, Italienisch, Japanisch, Niederländisch, Portugiesisch (Brasilien) und Russisch.
>
> **Voraussetzung:** Windows 11. Das `tar.exe` von Windows 10 kann keine LZMA-komprimierten 7z-Archive lesen; der Updater meldet dann *„Entpacken fehlgeschlagen (tar.exe braucht 7z-Unterstützung, Windows 11)“*.

## Bedienung

| Element | Funktion |
|---|---|
| **x64** je Version | Installiert bzw. aktualisiert diese Version. **Grün** mit Häkchen = aktuell, **orange** = Update verfügbar (oder in einer anderen Sprache installiert), neutral mit Download-Pfeil = nicht installiert. Der Tooltip zeigt Ordner, installierte Version und Sprache. |
| **Alle installieren / Alle updaten** | Installiert bzw. aktualisiert Release und Previous. Aktuelle Versionen werden übersprungen. |
| **Verknüpfung auf dem Desktop erstellen** | Legt nach der Installation eine Desktop-Verknüpfung an (zeigt auf `BetterbirdPortable.exe`). |
| **Zum Startmenü hinzufügen** | Legt nach jeder Installation bzw. Aktualisierung einen Startmenü-Eintrag an – im Startmenü-Ordner **„Betterbird Portable“** (mit *Für jede Version einen Ordner anlegen*) bzw. als Eintrag „Betterbird Portable“. Einzelne Einträge lassen sich über *Extras → Zum Startmenü hinzufügen* wieder entfernen. |
| **Für jede Version einen Ordner anlegen** | An: jede Version bekommt einen eigenen Ordner (`Betterbird Release x64`, `Betterbird Previous x64`). Aus: Installation direkt in den Ordner des Updaters; *Alle installieren* ist dann deaktiviert. |
| **Versionskontrolle ignorieren** | Lädt und installiert auch dann neu, wenn die Version bereits aktuell ist. |
| **Sprache** | Sprache des heruntergeladenen Betterbird (Standard: passend zur Windows-Sprache). Gilt ab der nächsten Installation; Versionen in einer anderen Sprache werden orange markiert und mit *Alle updaten* neu geladen. Das Profil bleibt erhalten. |
| **Beenden** | Schließt das Programm. Während eines Downloads wird daraus **Abbrechen**. |
| **ⓘ** (unten links) | Info-Fenster mit Version, Entwickler, Website und Programm-Updates. |

**Menü Extras**

- *Versionen neu abfragen* (F5)
- *Installationsordner öffnen*
- *Desktop-Verknüpfungen jetzt erstellen* – für alle installierten Versionen
- *Zum Startmenü hinzufügen* – mit **Für jede Version einen Ordner anlegen** ein Untermenü: *Alle installierten Versionen*, jede installierte Version einzeln (Häkchen = im Startmenü, ein Klick fügt hinzu bzw. entfernt) und *Alle aus dem Startmenü entfernen*. Ohne diese Option ein einzelner Menüpunkt, der den Eintrag „Betterbird Portable“ anlegt bzw. entfernt.
- *Portables Profil für jede Version* (`.\Profile`) / *Ein portables Profil für alle Versionen* (`..\Profile`) – wird sofort auf alle installierten Versionen angewendet
- *Heruntergeladene Installer behalten* / *Heruntergeladene Installer löschen* – die Setups liegen in `Update\`; ein vorhandenes Setup mit passender Prüfsumme wird wiederverwendet

**Menü Version Info:** Links zu den Betterbird-Versionshinweisen, zur Download-Seite und zum Info-Fenster.

Läuft noch ein Betterbird aus dem Zielordner, bittet der Updater darum, ihn zu schließen, statt Dateien im laufenden Betrieb zu überschreiben. Das Programm erscheint auf Deutsch, wenn Windows auf Deutsch eingestellt ist, sonst auf Englisch. Es folgt dem hellen bzw. dunklen Windows-Design und der Akzentfarbe.

## Ordnerstruktur

Mit **Für jede Version einen Ordner anlegen**:

```
<Updater-Ordner>\
├─ BetterbirdUpdater.exe
├─ BetterbirdUpdater.ini            Einstellungen des Updaters
├─ Profile\                         nur bei „ein Profil für alle Versionen“
├─ Betterbird Release x64\
│  ├─ BetterbirdPortable.exe        ← hiermit Betterbird starten
│  ├─ BetterbirdPortable.ini        Einstellungen des Starters
│  ├─ Profile\                      portables Profil: E-Mails, Konten, Einstellungen, Add-ons
│  └─ App\                          Programmdateien
│     ├─ betterbird.exe …
│     └─ updates\Version.log
└─ Betterbird Previous x64\
```

Ohne diese Option liegen `BetterbirdPortable.exe`, `BetterbirdPortable.ini`, `App\` und `Profile\` direkt im Ordner des Updaters. Ein Update ersetzt nur `App\`; `Profile\` wird nie angefasst.

## Der Starter `BetterbirdPortable.exe`

- startet `App\betterbird.exe` mit `-profile "<portables Profil>"`, sodass weder `%APPDATA%\Thunderbird` noch `profiles.ini` benutzt werden,
- hängt die Schalter aus `BetterbirdPortable.ini` und eigene Kommandozeilen-Argumente an (z. B. `mailto:`-Links),
- trägt das Betterbird-Icon.

> **Kein Auto-Update:** Der Updater legt `App\distribution\policies.json` an (`DisableAppUpdate`), sodass Betterbird nicht selbst nach Updates sucht. Die Richtlinie setzt außerdem `mail.shell.checkDefaultClient` auf `false`, damit Betterbird nicht bei jedem Start fragt, ob es Standard-Mailprogramm werden soll (in den Einstellungen änderbar). Aktualisiert wird ausschließlich über diesen Updater.

### BetterbirdPortable.ini

Liegt neben jeder `BetterbirdPortable.exe`. Zeilen, die mit `;` oder `#` beginnen, sind Kommentare.

```ini
ProfileDir=Profile
Flags=-offline
```

**`ProfileDir`** – Ordner des Profils, relativ zum Ordner des Starters oder absolut. `Profile` = eigenes Profil für diese Version (Standard), `..\Profile` = ein Profil im Ordner des Updaters für alle Versionen.

> Der Updater schreibt `ProfileDir` bei jeder Installation und beim Umschalten im Menü *Extras* neu; ändere das Profil daher besser dort. Nur die Zeile `Flags=` bleibt bei Updates erhalten. Ein **gemeinsames Profil** kann nicht von zwei Versionen gleichzeitig benutzt werden, und ein Profil, das Release benutzt hat, öffnet Previous nur noch mit `-allow-downgrade` – mit dem Risiko, Daten zu verlieren.

**`Flags`** – zusätzliche Kommandozeilen-Schalter, alle **in einer Zeile**, getrennt durch Leerzeichen:

| Schalter | Wirkung |
|---|---|
| `-offline` | startet im Offline-Modus |
| `-safe-mode` | Fehlerbehebungsmodus (Add-ons deaktiviert) |
| `-purgecaches` | leert die Startup-Caches (z. B. nach Problemen mit Add-ons) |
| `-allow-downgrade` | erlaubt das Öffnen eines Profils einer neueren Version |
| `-mail` / `-addressbook` / `-compose` | öffnet das Mail-Fenster, das Adressbuch bzw. eine neue Nachricht |

Argumente an `BetterbirdPortable.exe` werden ebenfalls weitergereicht, z. B. `BetterbirdPortable.exe mailto:info@example.com`.

## Updates des Updaters

Der Updater aktualisiert sich selbst über die [Releases dieses Repositorys](https://github.com/naderi/portable-betterbird-updater/releases):

- Einmal täglich sucht er im Hintergrund nach einer neuen Version (abschaltbar im Info-Fenster: *Automatisch suchen (einmal täglich)*). Gibt es eine, bekommt der ⓘ-Knopf einen orangen Punkt.
- Im Info-Fenster: *Nach Updates suchen* → *Update herunterladen* → *Neu starten & aktualisieren*.
- Ein Download wird nur verwendet, wenn seine Signatur (`.sig`) zum eingebauten Schlüssel passt; alles andere wird abgelehnt. Die laufende EXE wird in `.old` umbenannt, die neue übernimmt ihren Namen, und das Programm startet neu. `BetterbirdUpdater.ini` und alle Daten daneben bleiben unverändert.

## Haftungsausschluss

Dies ist ein unabhängiges Projekt. Es steht in keiner Verbindung zum Betterbird-Projekt, zu MZLA Technologies oder Mozilla und wird von ihnen weder unterstützt noch gesponsert. Thunderbird ist eine Marke der Mozilla Foundation. Die mit diesem Updater heruntergeladenen Programme unterliegen ihren eigenen Lizenzbedingungen.

## Lizenz

Freeware – kostenlos nutzbar, aber **nicht zum Verkauf**. Siehe [LICENSE](LICENSE).

## Unterstützung

Wenn Portable Betterbird Updater dir Zeit spart, kannst du die Entwicklung auf Ko‑fi unterstützen. ☕

<a href='https://ko-fi.com/N7N123QIX0' target='_blank'><img height='36' style='border:0px;height:36px;' src='https://storage.ko-fi.com/cdn/kofi2.png?v=6' border='0' alt='Buy Me a Coffee at ko-fi.com' /></a>

---

© 2026 [Ali Naderi](https://github.com/naderi) · [digitalent.dev](https://digitalent.dev/de/)
