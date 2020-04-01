---
title: Notre première fenêtre JavaFX
date: 2020-03-31T13:32:09.000Z
draft: false
description: JavaFX et maven
categories:
  - JavaFX
tags:
  - JavaFX
  - Maven
thumbnail: img/basejfx.png
lead: JavaFX et maven
comments: false
authorbox: true
toc: true
---
## Notre tout premier projet en JavaFX

Dans cette vidéo je vous montre comment créer un premier projet JavaFX FXML.
Je vais utiliser l'architecture maven pour ce projet car depuis le jdk 9 JavaFX n'est plus intégré nativement à la jvm et au jdk ce qui nous oblige à l'ajouter à nos projets.

Une option aurait été d'ajouter manuellement les librairies nécessaires mais maven peut faire le travail pour nous. De plus maven va gérer les dépendances du projet pour nous et rendre le projet migrable d'un poste de développement à l'autre. Et ça c'est trop bien.

{{< youtube VzFirU5GE2A>}}

---
---

## Créez le modèle dans IntelliJ

- Dans **File -> New -> Project** créez un nouveau projet **maven** (dans la marge gauche).
- Cochez **create from archetype** et cliquez sur **new**
- Renseignez les champs groupID: `org.openjfx` artifactid: `javafx-maven-artchetypes` version: `0.0.1` et validez
- choisissez cet archetype
- modifiez en double cliquant dessus la valeur de archetypeArtifact en `javafx-archetype-fxml`

voilà !!!

