---
title: "Voyage entre les Intent"
date: 2020-03-27T11:57:32+01:00
draft: true
description: Les intent d'android
categories:
  - Android
tags:
  - Android
  - Intent
thumbnail: img/androidbase.png
lead: Voyage entre les Intent
comments: false
authorbox: true
toc: true
---
## Les Activités et les `Intent`

Nous avons vue que les vues étaient appelées des activités en Android. Afin d'ouvrir une nouvelle activité et transférer des information d'une activité à l'autre nous allons avoir besoin des `Intent` qui vont permettre de faire naviguer des informations d'une vue à l'autre.

> vidéo en cours d'upload

Nous allons faire une simple application qui dans un premier temps ouvrira une deuxième activité au clique sur un bouton et dans un deuxième temps nous verrons comment envoyer un message sur la deuxième activité puis enfin renvoyer une réponse sur la première.

## C'est partie

De la manière que nous l'avons fait créons un projet nommé DeuxActivites.
Dans `activity_main.xml` enlevez le TextView et ajoutez un bouton à qui on donnera le nom `button_main`


```xml
<Button
  android:id="@+id/button_main"
  android:layout_width="wrap_content"
  android:layout_height="wrap_content"
  android:text="@string/button_main"
  android:layout_marginBottom="16dp"
  android:layout_marginRight="16dp"
  android:onClick="launchSecondActivity"
  app:layout_constraintBottom_toBottomOf="parent"
  app:layout_constraintRight_toRightOf="parent"
  />
```

Pensez bien à renvoyer le text dans `strings.xml`. Pour le moment est souligné en rouge car la méthode n'existe pas. A l'aide de l'ampoule rouge créez la méthode dans `MainActivity.java`.

Dans `MainActivity.java` ajoutez `Log.d(LOG_TAG,"clique sur bouton");` dans la méthode `launchSecondActivity()` et ajoutez `private static final String LOG_TAG = MainActivity.class.getSimpleName();` en haut de la classe.

Exécutez votre appli. Si tout se déroule bien au clique sur le bouton vous devriez lire dans l'onglet run en bas de l'IDE

```zsh
D/MainActivity: clique sur bouton
```
> la méthode log est l'équivalent en Java du println que nous utilisons pour faire des impressions en console. La grosse différence est que nous signons nos événements par une String.

## Créons la seconde activité

Pour android, il ya deux façons de faire du lien entre les activités:

- explicite: nous connaissons déjà la classe cible de l'intent.
- implicite: nous ne connaissons pas nécessairement le nom de la cible mais nous avons une action générale à effectuer.

Cliquez droit sur le dossier **app** puis **new -> activity -> Empty Activity** et nommez la nouvelle activité `SecondActivity`.

Dans **AndroidManifest** nous définissons plus précisément notre activité. Ces information seront utilisées pour afficher la navigation en haut dans la barre de navigation.

dans la Manifest remplacez

```xml
<activity android:name=".SecondActivity"></activity>
```

par

```xml
<activity
  android:name=".SecondActivity"
  android:label="Seconde Activité"
  android:parentActivityName=".MainActivity">
</activity>
```

Puis extrayez le label dans `strings.xml`

## Petite mise en page

dans `activity_second.xml` faite la mise en page suivante en ajoutant un TextView

```xml
<TextView
    android:id="@+id/text_header"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginTop="16dp"
    android:layout_marginStart="8dp"
    android:text="Message reçu"
    android:textAppearance="@style/TextAppearance.AppCompat.Medium"
    android:textStyle="bold"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toTopOf="parent"/>
```

## Définissons l'Intent

Enfin dans `MainActivity.java` créez l'**intent** dan la méthode `launchSecondActivity()`

L'intent prend deux paramètres, le context et la classe de l'activité à charger.

`Intent intent = new Intent(this, SecondActivity.class);`

Il faut ensuite démarrer l'activité: `startActivity(intent);`

Vous pouvez maintenant lancer votre activité.



