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

```
<iframe width="560" height="315" src="https://www.youtube.com/embed/s3ozDPf48_0" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
```
