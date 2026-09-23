# Entwickler-Dokumentation: Grand Slam Baseball Manager v.6.2

## Projektübersicht

Grand Slam Baseball Manager ist eine umfassende Baseball-Managementsimulation mit Fokus auf dem deutschen Ligasystem. Das Spiel bietet eine Vielzahl an Management-Funktionen, einem realistischen Wirtschaftssystem sowie einer detaillierten Spielsimulation. Es wurde mit Python und Tkinter implementiert und folgt einer modularen Architektur.

## Architektur

Das Projekt ist modular aufgebaut und folgt einer objektorientierten Struktur:

### Hauptkomponenten:

1. **Hauptmodul (`main.py`)**: Initialisiert das Spiel, enthält die Menüstruktur und koordiniert alle anderen Module.

2. **Datenmodul (`game_data.py`)**: Zentrale Datenspeicherung und -verwaltung für alle Spielelemente.

3. **Team-Manager (`team_manager.py`)**: Verwaltet die Teamzusammensetzung und Kaderplanung.

4. **Liga-System (`league_system.py`)**: Handhabt das 3-Liga-System mit Auf- und Abstieg.

5. **Spielsimulation (`game_simulation_new_fixed.py`)**: Kernstück der Baseballspiel-Simulation mit Wettereffekten und Tagesform.

6. **Stadion-System (`baseballpark_system.py`)**: Verwaltung des Stadions mit Upgrades und Finanzen.

7. **Coach-System (`coach_system.py`)**: Rekrutierung und Verwaltung von Coaches mit Leistungsboosts.

8. **Transfer-Markt (`transfer_market.py`)**: Plattform für den Kauf und Verkauf von Spielern.

9. **Draft-System (`draft_system.py`)**: Spielerrekrutierung durch verschiedene Draft-Optionen.

### Datenmodell:

- **Team-Daten**: Enthält Roster, Liga, Budget, Coach-Informationen, Statistiken, usw.
- **Spieler-Attribute**: 6 Hauptattribute (Power, Contact, Speed, Defense, Arm, Leadership)
- **Coach-Attribute**: Liga-Beschränkung, Boost-Prozentsatz, Spezialisierung, Vertragsdauer
- **Stadion-Daten**: 10 Upgrade-Kategorien mit progressiven Leistungssteigerungen
- **Liga-Daten**: 3 Ligen mit eigenen Teams, Spielplänen und Anforderungen

## Technische Details

### Technologien:
- **Programmiersprache**: Python 3.x
- **GUI-Framework**: Tkinter mit ttk für moderne Widgets
- **Bildverarbeitung**: PIL/Pillow für Logo und Grafiken
- **Datenspeicherung**: JSON für Speicherstände und Datenbankdateien

### Design-Patterns:
- **MVC-ähnliche Struktur**: Trennung von Daten (Model), Anzeige (View) und Logik (Controller)
- **Callback-Pattern**: Für die Navigation zwischen verschiedenen Spielbereichen
- **Factory-Pattern**: Für die Generierung von Spielern, Coaches und Teams

### Wichtige Systeme:

#### Coach-Boost-System:
Die Coach-Boosts werden als Multiplikatoren auf die Teamstärke angewendet. Der effektive Boost hängt von der Tagesform des Coaches ab:
```python
effective_boost = base_boost * daily_form_factor  # (daily_form_factor zwischen 0.8 und 1.0)
team_rating = base_team_rating * (1 + effective_boost/100)
```

Für KI-Gegnerteams wird ein vereinfachtes Coach-Boost-System verwendet, das keinen tatsächlichen Coach-Objekte erfordert:
```python
# Liga-basierter Boost für KI-Teams
if liga == "Baseball Bundesliga":
    boost_percentage = 8  # 8% für höchste Liga
elif liga == "Regionalliga Katzenelnbogen":
    boost_percentage = random.choice([6, 7])  # 6-7% für mittlere Liga
else:  # Kreisliga
    boost_percentage = 5  # 5% für niedrigste Liga
```

#### Spielsimulation:
Die Baseball-Simulation berücksichtigt folgende Faktoren:
- Teamstärke (berechnet aus Spielerattributen)
- Coach-Boost (prozentuale Verstärkung)
- Tagesform (-10% bis +10% zufällige Schwankung)
- Wetterbedingungen (beeinflussen bestimmte Attribute)
- Heimvorteil (~5% Bonus für das Heimteam)

Die Ereignisgenerierung nutzt gewichtete Wahrscheinlichkeiten, die durch den Stärkeunterschied zwischen Schlagmann und Pitcher beeinflusst werden:
```python
# Stärkeunterschied normalisieren (-50 bis +50 -> -0.25 bis +0.25)
normalized_diff = max(-0.25, min(0.25, strength_diff / 200))

# Treffer-Wahrscheinlichkeiten anpassen
for event in ["single", "double", "triple", "homerun", "walk"]:
    adjusted_probs[event] = base_probs[event] * (1 + normalized_diff)
    
# Out-Wahrscheinlichkeiten anpassen
for event in ["strikeout", "groundout", "flyout"]:
    adjusted_probs[event] = base_probs[event] * (1 - normalized_diff)
```

#### Stadion-Upgrade-System:
Stadion-Upgrades folgen einer exponentiellen Kostenkurve:
```python
upgrade_cost = base_price * (2.0 ** current_level)
```

Dies sorgt für eine steilere Progression als in früheren Versionen (die einen Faktor von 1.5 verwendeten) und schafft ein ausgewogeneres Wirtschaftssystem.

#### Liga-Steuersystem:
Liga-Steuern skalieren mit der Stadiongröße und dem Budget, um ein ausgewogenes Wirtschaftssystem zu gewährleisten:
```python
# Beispiel für Steuerberechnung
base_tax = 10000  # Grundsteuer
capacity_tax = stadium_capacity * 0.5  # €0,50 pro Sitzplatz
budget_tax = team_budget * 0.02  # 2% vom Budget
total_tax = base_tax + capacity_tax + budget_tax
```

## Erweiterbarkeit

Das System wurde mit Erweiterbarkeit im Sinn entwickelt:

### Hinzufügen neuer Features:
1. **Neue Spielermechaniken**: Erweitern Sie die Spielerklasse mit neuen Attributen und passen Sie die Simulationslogik an.
2. **Zusätzliche Ligen**: Fügen Sie neue Ligen zur Liga-Konfiguration hinzu und definieren Sie Auf-/Abstiegsregeln.
3. **Neue Stadion-Upgrades**: Erweitern Sie das Upgrade-System in `baseballpark_system.py` und `game_data.py`.

### Beispiel zum Hinzufügen eines neuen Stadion-Upgrades:
```python
# In game_data.py:
self.stadium_data['new_upgrade'] = {'level': 1, 'name': 'Neues Upgrade', 'some_bonus': 0.01}

# In baseballpark_system.py, UPDATE_OPTIONS Dictionary:
'new_upgrade': {
    'name': 'Neues Upgrade',
    'description': 'Beschreibung des neuen Upgrades',
    'base_price': 200000,
    'icon': '🔧',  # Unicode-Icon zur Darstellung
    'position': (x, y)  # Position im Stadion-Visualisierungsbereich
}
```

## Debugging-Tipps

1. **Speicherproblemen**: Überprüfen Sie das Format der Speicherdateien mit einem JSON-Validator.
2. **UI-Probleme**: Nutzen Sie `print`-Statements für Widget-Hierarchie und -Eigenschaften.
3. **Spielsimulationsprobleme**: Aktivieren Sie Debug-Logs in `game_simulation_new_fixed.py` durch Setzen der `DEBUG`-Konstante.

## Bekannte Einschränkungen

1. **Performance bei großen Ligen**: Das System kann bei sehr großen Ligen (>20 Teams) etwas langsamer werden.
2. **Speichergröße**: Speicherdateien können bei vielen Saisons relativ groß werden.
3. **Bildschirmauflösung**: Das UI ist für Auflösungen ab 1280x720 optimiert. Bei kleineren Auflösungen kann es zu Darstellungsproblemen kommen.

## Zukünftige Entwicklungsrichtungen

- **Online-Multiplayer**: Möglichkeit, gegen andere Manager anzutreten.
- **Erweiterte Statistiken**: Tiefere Baseball-Statistiken wie WHIP, OPS, etc.
- **Historischer Modus**: Spielen mit historischen Teams und Spielern.
- **Internationaler Wettbewerb**: Turnier-Modus für internationale Spiele.
- **Mobilversion**: Anpassung für mobile Plattformen mit Touch-Interfaces.