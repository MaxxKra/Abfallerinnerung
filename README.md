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

![Screenshot_ics](https://github.com/MaxxKra/Abfallerinnerung/assets/83531755/6959075f-d531-4581-a2be-8a67d0063a62)


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

Der in dieses File einzufügende Code sowie der Name ist ebenso [hier](/../main/sensor/N%C3%A4chste%20Abholung.yaml) zu finden.

Kopiere diesen Code und füge ihn in das File `Nächste Abholung.yaml` ein

```yaml
platform: waste_collection_schedule
name:  "Nächste Abholung"
value_template: '{{value.types|join(", ")}}{% if value.daysTo == 0 %} Heute{% elif value.daysTo == 1 %} Morgen{% else %} in {{value.daysTo}} tagen{% endif %}'
```

Danach wieder Home Assistant neu Starten.

Somit ist der `sensor.nachste_abholung` angelegt.

Nun muss zunächst nachgesehen werden, wie die einzelnen Abholungen benannt sind.  
Dazu unter `Entwicklerwerkzeuge / ZUSTÄNDE` bei `Entität` in die Suchleiste  `Nächste Abholung` eingeben.  
Bei den Attributen sollten nun alle Abholtage mit den jeweiligen Namen angezeigt werden.

![Screenshot_Nächste_Abholung](https://github.com/MaxxKra/Abfallerinnerung/assets/83531755/53b581f6-20f9-4465-ac99-69d0d22486aa)

In meinem Fall sind die Abholungen wie folgt benannt:
  - Altpapier
  - Leichtverpackungen
  - Bioabfall
  - Restabfall

__Notiere dir die Bezeichnungen aus deinem Kalender und tausche diese im weiteren Verlauf der Programmierung aus!__

### 2.2. Sensoren jeweiliger Abholungen

Nun habe ich für jede der "Tonnen" einen eigenen Sensor und demnach ein eigenes File in meinem Sensor Ordner angelegt.

  **2.2.1**  [Altpapierabholung.yaml](/../main/sensor/Altpapierabholung.yaml)
```yaml
platform: waste_collection_schedule
name: "Altpapier"
value_template: '{% if value.daysTo == 0 %} Heute{% elif value.daysTo == 1 %} Morgen{% else %} in {{value.daysTo}} tagen{% endif %}'
types:
  - Altpapier
```

  **2.2.2**  [Biomüllabholung.yaml](/../main/sensor/Biom%C3%BCllabholung.yaml)
```yaml
platform: waste_collection_schedule
name: "Bioabfall"
value_template: '{% if value.daysTo == 0 %} Heute{% elif value.daysTo == 1 %} Morgen{% else %} in {{value.daysTo}} tagen{% endif %}'
types:
  - Bioabfall
```

  **2.2.3**  [Leichtmüllabholung.yaml](/../main/sensor/Leichtm%C3%BCllabholung.yaml)
```yaml
platform: waste_collection_schedule
name: "Leichtverpackungen"
value_template: '{% if value.daysTo == 0 %} Heute{% elif value.daysTo == 1 %} Morgen{% else %} in {{value.daysTo}} tagen{% endif %}'
types:
  - Leichtverpackungen
```

  **2.2.1**  [Restmüllabholung.yaml](/../main/sensor/Restm%C3%BCllabholung.yaml)
```yaml
platform: waste_collection_schedule
name: "Restabfall"
value_template: '{% if value.daysTo == 0 %} Heute{% elif value.daysTo == 1 %} Morgen{% else %} in {{value.daysTo}} tagen{% endif %}'
types:
  - Restabfall
```

Somit habe ich nach dem Neustart von Home Assistant 4 neue Sensoren zum Auswerten.
  - `sensor.altpapier`
  - `sensor.bioabfall`
  - `sensor.leichtverpackungen`
  - `sensor.restabfall`

## 3. Templates

Mit den zuvor erstellten Sensoren, kann jetzt ein Template zur Textanzeige angelegt werden.  
Zu diesem Zweck, habe ich in meinem Template Folder eine neue Datei angelegt und ihr den Namen [Müllabholung.yaml](/../main/template/M%C3%BCllabholung.yaml) gegeben.
In dieses File kommen folgende Codezeilen:

```yaml
sensor:

#-----------------------------------------------------------  
# Müllabholung Heute
#-----------------------------------------------------------

- name: Müllabholung Heute
  unique_id: mullabholung_heute
  icon: mdi:trash-can-outline
  state: >
    {% set ALTPAPIER = states.sensor.altpapier.state %}
    {% set LEICHTVERPACKUNG = states.sensor.leichtverpackungen.state %}
    {% set RESTABFALL = states.sensor.restabfall.state %}
    {% set BIOABFALL = states.sensor.bioabfall.state %}
    {% if ALTPAPIER == "Heute" and LEICHTVERPACKUNG == "Heute" and RESTABFALL == "Heute" and BIOABFALL == "Heute" %}
    die Altpapier, Leichtverpackung, Restabfall und Bioabfall
    {% elif LEICHTVERPACKUNG == "Heute" and RESTABFALL == "Heute" and BIOABFALL == "Heute" %} 
    die Leichtverpackung, Restabfall und Bioabfall
    {% elif ALTPAPIER == "Heute" and RESTABFALL == "Heute" and BIOABFALL == "Heute" %}
    die Altpapier, Restabfall und Bioabfall
    {% elif ALTPAPIER == "Heute" and LEICHTVERPACKUNG == "Heute" and BIOABFALL == "Heute" %}
    die Altpapier, Leichtverpackung und Bioabfall
    {% elif ALTPAPIER == "Heute" and LEICHTVERPACKUNG == "Heute" and RESTABFALL == "Heute" %}
    die Altpapier, Leichtverpackung und Restabfall
    {% elif ALTPAPIER == "Heute" and LEICHTVERPACKUNG == "Heute" %}
    die Altpapier und Leichtverpackung
    {% elif ALTPAPIER == "Heute" and RESTABFALL == "Heute" %}
    die Altpapier und Restabfall
    {% elif ALTPAPIER == "Heute" and BIOABFALL == "Heute" %}
    die Altpapier und Bioabfall
    {% elif LEICHTVERPACKUNG == "Heute" and RESTABFALL == "Heute" %}
    die Leichtverpackung und Restabfall
    {% elif LEICHTVERPACKUNG == "Heute" and BIOABFALL == "Heute" %}
    die Leichtverpackung und Bioabfall
    {% elif RESTABFALL == "Heute" and BIOABFALL == "Heute" %}
    die Restabfall und Bioabfall
    {% elif ALTPAPIER == "Heute" %}
    die Altpapier
    {% elif LEICHTVERPACKUNG == "Heute" %}
    die Leichtverpackung
    {% elif RESTABFALL == "Heute" %}
    die Restabfall
    {% elif BIOABFALL == "Heute" %}
    die Bioabfall
    {% else %}
    keine
    {%endif%}

#-----------------------------------------------------------  
# Müllabholung Text Template
#-----------------------------------------------------------
- name: Müllabholung Text
  unique_id: mullabholung_text
  icon: mdi:trash-can-outline
  state: >
    {% if states.calendar.ics.state == 'on' %}
    Du musst heute {{ states.sensor.mullabholung_heute.state }} Tonne rausstellen!
    {% else %}
    
    {%endif%}
```

Jetzt wird uns bei `sensor.mullabholung_text` auch schon ein Text als Zustand angezeigt

![Screenshot_mullabholung_text](https://github.com/MaxxKra/Abfallerinnerung/assets/83531755/84856c72-00a4-4456-b3ef-4f4578ce51ee)
