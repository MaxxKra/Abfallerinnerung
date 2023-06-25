# Abfallerinnerung

Hier zeige ich, wie ich meine Müllerinnerung mit PopUp umgesetzt habe.

![Abfallerinnerung_Popup_1](https://github.com/MaxxKra/Abfallerinnerung/assets/83531755/1045ea22-d2ff-4d54-b24d-c35bbf979b64)

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

