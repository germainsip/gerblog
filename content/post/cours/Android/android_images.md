---
title: "Android et les images"
date: 2020-04-02T11:10:15+02:00
draft: true
description: Android et les images
categories:
  - Android
tags:
  - Android
  - design
thumbnail: img/androidbase.png
lead: Ajoutons des images à nos applications
comments: false
authorbox: true
toc: true
---
## Ajoutons des images à tout ça

Téléchargez ces [images](/download/droidCafe.zip) ou utilisez les votres. Nous allons les intégrer à une application avec des images cliquables.

Créez un projet qui s'appel "Droid Cafe" en choisissant **Basic Activity** cette fois ci.

Nous ne toucherons pas à `activity_main.xml` mais à `content_main.xml`.

Dans `content_main.xml` commencez par supprimer le `fragment` (nous aborderons ces notions bien plus tard).

Remplacez le par un TextView :

```xml
<TextView
    android:id="@+id/textintro"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginTop="8dp"
    android:text="Les Desserts du Café"
    android:textSize="24sp"
    android:textStyle="bold"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toTopOf="parent" />
```

Copiez collez les 3 images dans le dossier res/drawable puis glissez depuis la palette une `ImageView`
L'IDE devrait vous proposer de choisir une ressource à lui associer. Choisissez le donuts. Ajoutez une contrainte liée au titre et réglez les 2 contraintes à 24dp.

Vous devriez avoir:


```xml
<ImageView
    android:id="@+id/donut"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginStart="24dp"
    android:layout_marginTop="24dp"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toBottomOf="@+id/textintro"
    app:srcCompat="@drawable/donut_circle" />
```

et visuellement

![donut1](/img/android/ajoutDonut.png)

Ajoutez une description en attribut et envoyez la dans les `strings.xml`