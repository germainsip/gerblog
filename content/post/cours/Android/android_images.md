---
title: Android et les images
date: 2020-04-02T09:10:15.000Z
draft: false
description: Android et les images
categories:
  - Android
tags:
  - Android
  - design
thumbnail: img/androidbase.png
lead: Ajoutons des images à nos applications (en cours de rédaction)
comments: false
authorbox: true
toc: true
---
## Ajoutons des images à tout ça


{{< youtube O8OaBudUmXY>}}

---
---

:hourglass_flowing_sand:  Article en cours de rédaction  :hourglass_flowing_sand:

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

Ajoutez une description en attribut et envoyez la dans les `strings.xml`. Vous pourrez ensuite ajouter un `TextView` et reprendre cette déscription directement. Je vous laisse faire la mise en page. Mais je vous donne quand même le résultat en xml si vous êtes bloqués.


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

    <ImageView
        android:id="@+id/donut"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginStart="24dp"
        android:layout_marginTop="24dp"
        android:contentDescription="Les Donuts sont fourrés et avec un joli glaçage"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/textintro"
        app:srcCompat="@drawable/donut_circle" />

    <ImageView
        android:id="@+id/ice_cream_sandwich"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginStart="24dp"
        android:layout_marginTop="24dp"
        android:contentDescription="Les sandwiches à la crème glacée ont des gaufrettes au chocolat et une garniture à la vanille."
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/donut"
        app:srcCompat="@drawable/icecream_circle" />

    <ImageView
        android:id="@+id/froyo"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginStart="24dp"
        android:layout_marginTop="24dp"
        android:contentDescription="Le FroYo est un yogourt glacé libre-service de première qualité."
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/ice_cream_sandwich"
        app:srcCompat="@drawable/froyo_circle" />

    <TextView
        android:id="@+id/donut_Description"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginStart="24dp"
        android:layout_marginTop="24dp"
        android:layout_marginEnd="24dp"
        android:text="TextView"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.503"
        app:layout_constraintStart_toEndOf="@+id/donut"
        app:layout_constraintTop_toTopOf="@+id/donut" />

    <TextView
        android:id="@+id/ice_cream_sandwichs_description"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginStart="24dp"
        android:layout_marginTop="24dp"
        android:layout_marginEnd="24dp"
        android:text="TextView"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toEndOf="@+id/ice_cream_sandwich"
        app:layout_constraintTop_toTopOf="@+id/ice_cream_sandwich" />
    <TextView
        android:id="@+id/froyo_decription"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginStart="24dp"
        android:layout_marginTop="24dp"
        android:layout_marginEnd="24dp"
        android:text="TextView"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toEndOf="@+id/froyo"
        app:layout_constraintTop_toTopOf="@+id/froyo" />
```

Pensez bien à extraire les textes dans `strings.xml` et de les récupérer dans vos `TextView` pour obtenir le visuel suivant:

![visuel droid cafe](/img/android/donytcafe.png)

## Un peu d'action

---
> vidéo à venir

---

Dans un premier temps, nous allons afficher un `Toast` qui affiche quelle image a été cliquée.

Commençons par écrire nos messages dans `strings.xml`


```xml
<string name="donut_order_message">Vous avez commandé un Donut</string>
<string name="ice_cream_order_message">Vous avez commandé une Glace</string>
<string name="froyo_order_message">Vous avez commandé un Froyo</string>
```

Dans `MainActivity` ajoutons la métohde `displayToast()`.

```java

```
