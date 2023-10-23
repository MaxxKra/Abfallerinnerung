# Abfallerinnerung

Hier zeige ich, wie ich meine Müllerinnerung mit PopUp umgesetzt habe, so dass automatisch jeden Tag der Müllkalender abgefragt und wenn eine Abholung ansteht, das Popup am vorgesehenen Dashboard angezeigt wird.

![Abfallerinnerung_Popup](/../main/GIFs/Abfallerinnerung_Popup.gif)


[!["Buy Me A Coffee"](https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png)](https://www.buymeacoffee.com/bastler)


[![paypal](https://www.paypalobjects.com/en_US/i/btn/btn_donateCC_LG.gif)](https://www.paypal.me/kramlmaxx)



## Verwendete HACS Integrationen

### Integrationen:
  - https://github.com/mampfes/hacs_waste_collection_schedule.git

### Frontend:
  - https://github.com/thomasloven/hass-browser_mod.git
  - https://github.com/thomasloven/lovelace-card-mod.git
  - https://github.com/custom-cards/button-card.git
  - https://github.com/thomasloven/lovelace-layout-card.git

Diese Integrationen, insbesonders die `waste_collectopn_schedule` sind notwendig um die Erinnerung mittels Popup anzeigen zu lassen.
Stelle sicher, dass alle installiert und funktionsfähig sind.

## 1. Abfallkalender einrichten

Zur Einrichtung des Abfallkalenders gibt es mehrere Möglichkeiten. 
Ich habe mich, da es in meiner Heimatgemeinde einen Abfallkalender gibt, für die ICS Variante entschieden.
Für die ICS Datei, habe ich im `www` Ordner einen weiteren Ordner namens `muell` hinzugefügt und die Datei darin abgelegt.

Nun also, zu Beginn, den Code für den Müllkalender in die configuration.yaml einfügen.

```yaml
waste_collection_schedule:
  sources:
    - name: ics
      args:
        file: "www/muell/Abfallkalender.ics"
```

Danach Home Assistant neu Starten und in den Entwicklerwerkzeugen kontrollieren ob der Kalender angezeigt wird

![Screenshot_ics](/../main/GIFs/Screenshot_ics.png)


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

![Screenshot_Nächste_Abholung](/../main/GIFs/Screenshot_N%C3%A4chste_Abholung.png)

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

![Screenshot_mullabholung_text](/../main/GIFs/Screenshot_mullabholung_text.png)


## 4. Abfall-Karte

Als nächstes habe ich mir für eine Dashboard-Ansicht eine Karte gebastelt.  
Diese Karte zeigt alle 4 Tonnen mit den jeweiligen Abholtagen und als Überschrift den Text aus dem `sensor.mullabholung_text`.  
Aufgebaut ist diese Ansicht auf einer `Picture-Elements-Card` mit `custom:button-card`.

![Screenshot_Abfallkarte_1](/../main/GIFs/Screenshot_Abfallkarte_1.png)

Den Code für diese Karte findest du [hier!](/../main/Abfallkarte.yaml)  

Die verwendeten Bilder sind:
  - `Back_trans.png`
  - `Papier.png`
  - `Bio.png`
  - `Rest.png`
  - `Kunststoff.png`

Zu Finden für:
  - Österreich: [Bilder-AT](/../main/Bilder/AT)
  - Deutschland: [Bilder-DE](/../main/Bilder/DE)

Bei Interesse an dieser Karte, speichere dir die Bilder unter `www/muell`, kopiere dir den Code und füge ihn als `Manuell` zu deinem Dashboard hinzu.

Die beste Ansicht hatte ich mit der `Raster-Karte` mit zwei Spalten oder der `Layout-Karte` mit einer Größe von 50% der Bildbreite.
Wenn die Schriften zu groß für die Karte sein sollte kann sie natürlich angepasst werden.

### :bangbang: TIPP :bangbang:
Die Schrift ist in `vw` angegeben.  
VW bezieht sich auf die Bildschirmgröße und passt sich dadurch dem Browser-Fenster an.  
Alternativ können natürlich auch `px` als Pixel oder `em` was 16px entspricht, verwendet werden.


## 5. Erinnerung - Popup - Karte

### 5.1. Helfer anlegen

Zunächst habe ich für die Auslösung des Popup´s einen Helfer erstellt.  
Dieser Helfer hat folgende Attribute:
  - Type: Taste
  - Name: "Müll Erinnerung"
  - Symbol: mdi:delete-circle-outline
  - Entity_ID: input_button.mull_erinnerung

![Helfer_Abfallerinnerung](/../main/GIFs/Helfer_Abfallerinnerung.gif)

Um mit diesem Helfer den notwendigen More-Info Dialog für das Popup zu öffnen, ist eine Automatisierung notwendig.  
Dafür ist folgendes notwendig bzw. von mir so angelegt worden:
  - Name: "Popup Müllerinnerung Button
  - Entity_ID: input_button.mull_erinnerung
  - Browser_ID: die ID deines Browser aus Browser-Mod

![Automatisierung_Button](/../main/GIFs/Screenshot_Button_M%C3%BCllerinnerung.png)


Hier ist der Code der gesamten Automatisierung zum Kopieren.  
Es muss nur die `browser_id` geändert werden.  

```yaml
alias: Button Müllerinnerung
description: ""
trigger:
  - platform: state
    entity_id:
      - input_button.mull_erinnerung
condition: []
action:
  - service: browser_mod.more_info
    data:
      entity: input_button.mull_erinnerung
      large: false
      ignore_popup_card: false
      browser_id: SHB-PC_Vivaldi
mode: single
```


### 5.2. Bilder speichern

Nun sollten die notwendigen Bilder in den `www/muell` Ordner gespeichert werden.  
Auch wie zuvor sind die Bilder getrennt für AT und DE zum Download verfügbar.

Zu Finden für:
  - Österreich: [Bilder-AT](/../main/Bilder/AT)
  - Deutschland: [Bilder-DE](/../main/Bilder/DE)

Verwendet werden für das Popup folgende Bilder:

  | Bild Name | Bild Beschreibung |
  | --- | --- |
  | AP.png | Altpapier |
  | AP_BI.png | Altpapier und Bioabfall |
  | AP_LE.png | Altpapier und Leichtverpackung |
  | AP_LE_BI.png | Altpapier, Leichtverpackung und Bioabfall|
  | AP_LE_RE.png | Altpapier, Leichtverpackung und Restabfall |
  | AP_LE_RE_BI.png | Altpapier, Leichtverpackung, Restabfall und Bioabfall |
  | AP_RE.png | Altpapier und Restabfall |
  | AP_RE_BI | Altpapier, Restabfall und Bioabfall |
  | BI.png | Bioabfall |
  | LE.png | Leichtverpackung |
  | LE_BI.png | Leichtverpackung und Bioabfall |
  | LE_RE.png | Leichtverpackung und Restabfall |
  | LE_RE_BI.png | Leichtverpackung, Restabfall und Bioabfall |
  | RE.png | Restabfall |
  | RE_BI.png | Restabfall und Bioabfall |
  | Keine.png | Keine Abholung |
  | bg_muell_trans.png | Hintergrundbild transparent |
  | 1x1_transparent.png | 1x1 Pixel transparent |

Mit diesen Bildern sind alle Variationen der Abholung bedacht, egal ob nur eine Tonne oder mehrere an einem Tag abgeholt werden.

### 5.3. Popup Karte erstellen

Nachdem das erledigt ist, kann die eigentliche PopUp-Karte programmiert werden.  
Dazu einfach den Code für die Popup-Karte kopieren.

Den Code für die Popup-Karte findest du [hier!](/../main/PopUp/popup_muellerinnerung.yaml)

![Popup-Karte-kopieren](/../main/GIFs/Popup-Karte-kopieren.gif)

Nun den kopierten Code als neue Karte zu dem Dashboard hinzufügen, auf welchem die Popup-Nachricht angezeigt werden soll.  
In meinem Fall handelt es sich um mein Hauptdashboard, welches mittels "Layout-Card" programmiert wurde. Aus diesem Grund habe ich für das Popup eine eigene "Area" im Layout geschaffen.  

Es kann natürlich auch als eigene Karte im Raster, als Zeile oder Spalte im horizontalen oder vertikalen Stapel, usw. eingefügt werden.
Auch hier kann natürlich die Schriftgröße wie zuvor bei der Abfallerinnerung angepasst werden.

![Popup-Karte-import](/../main/GIFs/Popup-Karte-import.gif)

### :bangbang: MERKE :bangbang:
Ein Aufruf dieser Popup-Karte erfolgt über den More-Info Dialog.
Das bedeutet, dass der Helfer-Button die zuvor eingerichtete Automatisierung auslösen muss, welche den More-Info Dialog am Dashboard öffnet.  
Wenn allerdings im Browser mehrere Fenster von Homeassistant geöffnet sind, wird auch der More-Info Dialog auf allen Fenstern geöffnet!

## 6. Automatisierung

Um die Erinnerung, also das Popup automatisch anzuzeigen, habe ich eine Automatisierung erstellt.

Dafür gibt es auch wieder mehrere Möglichkeiten.
Wenn noch kein regelmäßiger Auslöser in Home Assistant vorhanden ist, kann ein Zeitplan erstellt werden.

Dazu einfach einen Helfer anlegen

![Zeitplan_Abfallerinnerung](/../main/GIFs/Zeitplan_Abfallerinnerung.gif)

Dieser Helfer hat folgende Attribute:
  - Type: Zeitplan
  - Name: "Abfallerinnerung"
  - Symbol: mdi:home-clock-outline
  - Entity_ID: schedule.abfallerinnerung

Mit diesem Helfer kann ein Zeitplan für jeden Tag der Woche zu jeder halben Stunde erstellt werden.  
In meinem Fall wird die Entität `schedule.abfallerinnerung` von Montag bis Freitag um 04:00 Uhr aktiv also `on` .  
Dieser Status bzw. Zustand kann nun als Auslöser für eine Automatisierung herangezogen werden, welche den Helfer-Taster `input_button.mull_erinnerung` betätigt.

![Screenshot_Zeitplan](/../main/GIFs/Screenshot_Zeitplan.png)

So wird mittels dieser Automatisierung die Taste `input_button.mull_erinnerung` betätigt, wenn der Zeitplan `schedule.abfallerinnerung` von `off` zu `on` wechselt.  
Natürlich kann diese Automatisierung variiert werden.  

## 7.Kontakt

Bei Fragen oder Anregungen nehmt bitte Kontakt mit mir auf:  
Email: youtube@smarthomebastler.at  
Discord: https://discord.gg/Wt42rpwQye  
Website: https://smarthomebastler.at
