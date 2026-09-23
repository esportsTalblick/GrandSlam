# Grand Slam Baseball Manager 1 — Browser Port v0.1

Diese Version ist eine **echte statische Browser-App** und kann auf GitHub Pages laufen. Sie benötigt keinen Python-Server.

## GitHub Pages
1. Neues GitHub-Repository erstellen.
2. Den **Inhalt dieses Ordners** in das Repository laden (nicht den Ordner selbst als zusätzliche Ebene).
3. `Settings` → `Pages` → `Deploy from a branch`.
4. Branch `main`, Ordner `/ (root)` auswählen und speichern.
5. Nach dem Deployment die angezeigte GitHub-Pages-Adresse öffnen.

## Wichtig zur 1:1-Portierung
Das Originalprojekt ist eine Python/Tkinter-Desktop-Anwendung. Tkinter kann nicht direkt auf GitHub Pages ausgeführt werden. Deshalb ersetzt diese Version die Desktop-Oberfläche durch HTML/CSS/JavaScript und bildet die dokumentierten Manager-Systeme browserseitig ab.

Die Original-Python-Dateien liegen unter `legacy-python/` zur Referenz. Diese v0.1 ist **noch keine mathematisch bitgenaue 1:1-Portierung jeder Python-Funktion**. Sie ist die funktionierende Browser-Basis, auf der die vorhandenen Python-Systeme Schritt für Schritt 1:1 nachgebildet werden können.
