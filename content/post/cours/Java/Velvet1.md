---
title: Velvet1
date: 2020-05-04T12:24:26.000Z
draft: false
description: Exemple d'utilisation du CSS avec javaFX
categories:
  - JavaFX
  - Persistance
tags:
  - JavaFX
  - Gradle
thumbnail: img/css.png
lead: Exemple d'utilisation du CSS avec javaFX
comments: false
authorbox: true
toc: true
mathjax: true
---

## Construction du squelette de l'appli

Commencez par créer un projet JavaFX avec Gradle.

{{<youtube OVz0EJK-TKU>}}

Ajoutez les plugins au build.gradle

```gradle
plugins {
    id 'java'
    id 'application'
    id 'org.openjfx.javafxplugin' version '0.0.8'
}
```

on définie les modules de javaFX la classe main

```gradle
javafx {
    version = "14"
    modules = ["javafx.controls","javafx.base","javafx.graphics","javafx.fxml"]
}

mainClassName = 'org.germain.App'
```

Créez les packages ainsi que `App.java`, `velvet.fxml` et `VelvetController.java` comme dans la vidéo.

Enfin, apportez les modification à build.gradle pour que gradle prenne en charge le fxml.

```gradle
sourceSets.main.resources.srcDirs("src/main/java").includes.addAll(["**/*.fxml", "**/*.css","**/*.png"])
sourceSets.main.resources.srcDirs("src/main/resources").includes.addAll(["**/*.*"])
```

## Une mini mise en page et un objet.

On va créer la base graphique de l'application:

{{<youtube Gh_RNr9TjSo>}}

Vous aurez besoin de l'image suivante: [disque](/download/disque.png) pour le vinyle et la pochette de [Dirt](/download/Dirt.jpeg)
