# Abfallerinnerung

Hier zeige ich, wie ich meine Müllerinnerung mit PopUp umgesetzt habe.

![Abfallerinnerung_Popup](/../main/GIFs/Abfallerinnerung_Popup.gif)

## Verwendete HACS Integrationen

### Integrationen:
  - https://github.com/mampfes/hacs_waste_collection_schedule.git

### Frontend:
  - https://github.com/thomasloven/hass-browser_mod.git
  - https://github.com/thomasloven/lovelace-card-mod.git
  - https://github.com/custom-cards/button-card.git
  - https://github.com/thomasloven/lovelace-layout-card.git

Diese Integrationen, insbesonders die "waste_collectopn_schedule" sind notwendig um die Erinnerung mittels Popup anzeigen zu lassen.
Stelle sicher, dass alle installiert und funktionsfähig sind.


## 1. Abfallkalender einrichten

Zur Einrichtung des Abfallkalenders gibt es mehrere Möglichkeiten. 
Ich habe mich, da es in meiner Heimatgemeinde einen Abfallkalender gibt, für die ICS Variante entschieden.
Für die ICS Datei, habe ich im "www" Ordner einen weiteren Ordner namens "muell" hinzugefügt und die Datei darin abgelegt.

Nun also, zu Beginn, den Code für den Müllkalender in die configuration.yaml einfügen.

```yaml
waste_collection_schedule:
  sources:
    - name: ics
      args:
        file: "www/muell/Abfallkalender.ics"
```

Danach Home Assistant neu Starten und in den Entwicklerwerkzeugen kontrollieren ob der Kalender angezeigt wird

## :bangbang: HINWEIS :bangbang:

Eine Abholung am jeweiligen Tag wird standardmäßig nur bis 10:00 Uhr angezeigt.  
Um dies zu ändern, kann man unter `/config/custom_components/waste_collection_schedule/__init__.py` die Zeit zum Anzeigen der Abholung, individuell anpassen.  
 Allerdings ist bei einem Update der Integration auch die Zeit wieder zurückgestellt.
 
![Waste_Time_1](/../main/GIFs/Waste_Time_1.gif)

## 2. Sensoren

Ich habe meine Sensoren und Templates in jeweilige Ordner als einzelne Dateien gespeichert.  
Wie man diese sogenannten `Sensor-Folder` und `Template-Folder` anlegt, habe ich in einem YouTube Video erklärt.

- [https://www.youtube.com/embed/s3ozDPf48_0](https://youtu.be/s3ozDPf48_0)

Alle nun folgenden Codes sind in der Struktur auf diese Variante des Sensor-Folders ausgerichtet.  
Es ist aber eine Änderung auf Sensor-File mit einem geringen Aufwand möglich.

### 2.1. Sensor Nächste Abholung

Legen wir zunächst den Hauptsensor für die Müllabholung an.
Dazu habe ich in meinem Sensor Ordner eine neue Datei angelegt.  
Dies hat den Namen `Nächste Abholung.yaml`.  

:exclamation: Umlaute und Leerzeichen sind im Dateinamen der einzelnen Files kein Problem :exclamation:

Der in dieses File einzufügende Code sowie der Name ist ebenso [hier](/../main/sensor/M%C3%BCllabholung.yaml) zu finden.

Kopiere diesen Code und füge ihn in das File `Nächste Abholung.yaml` ein

```yaml
platform: waste_collection_schedule
name:  "Nächste Abholung"
value_template: '{{value.types|join(", ")}}{% if value.daysTo == 0 %} Heute{% elif value.daysTo == 1 %} Morgen{% else %} in {{value.daysTo}} tagen{% endif %}'
```

Danach wieder Home Assistant neu Starten.

Nun kann in den Entwicklerwerkzeugen nachgesehen werden, wie die einzelnen Abholungen benannt sind.  
Dazu in der Suchleiste `Nächste Abholung` eingeben.  
Bei den Attributen sollten nun alle Abholtage mit den jeweiligen Namen angezeigt werden.

![Screenshot_Nächste_Abholung](https://github.com/MaxxKra/Abfallerinnerung/assets/83531755/53b581f6-20f9-4465-ac99-69d0d22486aa)

In meinem Fall sind die Abholungen wie folgt benannt:
  - Altpapier
  - Leichtverpackungen
  - Bioabfall
  - Restabfall

__Notiere dir die Bezeichnungen aus deinem Kalender und tausche diese im weiteren Verlauf der Programmierung aus!__
