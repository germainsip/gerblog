---
title: Notre première appli Android
date: 2020-03-24T08:26:26.000Z
draft: false
description: Notre première appli Android
categories:
  - Android
tags:
  - Android
thumbnail: img/androidbase.png
lead: Notre première appli Android
comments: false
authorbox: true
toc: true
---

## Notre première Appli

Nous allons ici commencer par faire ensemble une application très simple avec un champ où nous remplirons du texte et qui va se reporter dans un label après avoir appuyé sur un bouton.

{{< youtube EbYFno9dJQs >}}

---
---

Commencez par créer un projet Android dans Android Studio et nommez le tutoAnd1

Après le travail de **Gradle**, vous devriez obtenir l'arborescence suivante

![arbo1](/img/android/arboandro1.png)

## Le layout

Le **layout** est la vue. Vous le trouverez dans `res/layout`. Nous allons le modifier en rajoutant un `EditTex` et un `Button`.
Vous pouvez soit le faire de façon graphique ou directement dans le xml:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:padding="20dp"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/resultat"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="C'est ici que ça se passe!!!"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <EditText
        android:id="@+id/entre"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="160dp"
        android:hint="entrez votre texte ici"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <Button
        android:id="@+id/go"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="496dp"
        android:text="Go"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.0"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

Décomposons ce code. Tout d'abord, `ConstraintLayout` est une vue contrainte qui va nous permettre de contraindre nos éléments. Vous remarquerez que j'ai ajouté un **padding** dans les attributs d'origine afin que nos éléments ne soient pas collés aux bords.

Je vais décrire le `EditText` mais le principe est le même pour les autres éléments.

```xml
<EditText
  android:id="@+id/entre"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_marginTop="160dp"
  android:hint="entrez votre texte ici"
  app:layout_constraintEnd_toEndOf="parent"
  app:layout_constraintStart_toStartOf="parent"
  app:layout_constraintTop_toTopOf="parent"
  />
```

- `android:id="@+id/entre"` va nous permettre d'identifier notre élément et le récupérer dans la classe
- `android:hint="entrez votre texte ici"` affiche une indication à l'utilisateur, c'est l'équivalent du placeholder en html.
- Les autres attributs sont les contraintes de notre élément.

## La classe MainActivity

Vous pouvez déjà observer que notre classe hérite de `AppCompatActivity` ce qui implique qu'elle implémente la méthode `onCreate()`

```java
public class MainActivity extends AppCompatActivity
@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }

```

Cette méthode est appelée à la création de la vue.

Nous allons tout d'abord déclarer avant la méthode 3 variables pour nos 3 éléments.

```java
private TextView resultat;
private EditText entre;
private Button go;
```

Puis nous allons récupérer les éléments du layout à la façon du "javascript".

```java
resultat = findViewById(R.id.resultat);
entre = findViewById(R.id.entre);
go = findViewById(R.id.go);
```

La méthode `findViewById()` va chercher dans `R` l'`id` de l'élément que vous avez nommé précédemment.

## Un peu d'action maintenant

Afin de rendre réactive notre vue nous allons implémenter l'interface `View.OnClickListener` et la méthode `onClick()`.

L'IDE devrait vous proposer cette méthode. Une fois écrite, nous devons associer le listener à notre bouton:

```java
go.setOnClickListener(this);
```

et récupérer l'événement dans `onClick()`

```java
@Override
public void onClick(View v) {
    if (v == go) {
        resultat.setText(entre.getText());
    }
    }
```

La condition if n'était pas obligatoire ici car nous n'avons qu'un élément muni d'un **listener** mais je vous montre la technique pour la suite. Enfin, le reste est du java. (on récupère le texte de entre et on le met dans resultat).

Voilà il ne vous reste plus qu'à tester.


`
