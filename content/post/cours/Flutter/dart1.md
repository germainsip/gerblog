---
title: Flutter et Dart
date: 2020-03-24T16:29:46.000Z
draft: false
categories:
  - Flutter
tags:
  - Flutter

thumbnail: img/flutter/flutt_dart.png
lead: Introduction à Flutter en passant par Dart
comments: false
authorbox: true
toc: true
---

## Bienvenue dans le monde merveilleux de Flutter


### Qu'est-ce que Flutter

C'est un Framework développé par Google pour développer des applications pour Android, iOS, Windows, Mac, Linux, et le web.

> En clair, codez une fois et compilez pour tout!
> (*pour exporter vers des ios il est nécessaire d'avoir un Mac*)

### Environnement de développement

La doc de Flutter, en anglais,  [installer Flutter](https://flutter.dev/docs/get-started/install) vous explique la démarche d'installation pour les 3 os (Windows, MacOS et Linux).
> Si vous n'êtes pas à l'aise avec l'anglais demandez une démo à votre formateur.

En ce qui concerne l'éditeur, vous pouvez choisir IntelliJ, Android Studio ou Visual studio code. Pour la mise ne place, suivez la doc [réglage de l'éditeur](https://flutter.dev/docs/get-started/editor?tab=vscode).

## Premier projet avec Flutter

Si vous avez choisi Vscode, placez vous dans votre dossier de travail et affichez le la palette de commande (ctrl+shift+p ou cmd+shift+p) et tapez flutter puis choisissez New Project.

Il vous sera demandé le dossier de destination pour la création du projet.

Un fois la procédure terminée, vous pouvez tester le modèle proposé.

Si vous utilisez AndroidStudio(ou autre outil Jetbrain) ce sera new projet flutter.

## Dart le langage de Flutter

Suivez l'article sur [le langage Dart](../dart0/) si vous voulez comprendre les différences avec Java.

## Écrivez votre première application Flutter

Après avoir généré un nouveau projet Flutter, remplacez le contenu de `main.dart` par

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Salut Flutter',
      home: Scaffold(
        appBar: AppBar(
          title: const Text('Salut les Flutterinos'),
        ),
        body: const Center(
          child: const Text('Il est vivant!!!'),
        ),
      ),
    );
  }
}
```

![flut1](/img/flutter/flutt1.png)

ou sur ios

![flutt1](/img/flutter/flutt1ios.png)

On utilise `MaterialApp` pour construire l'application ce qui nous permet d'utiliser la bibliothèque d'éléments pré-construits.

## Allons un peu plus loin avec des imports de bibliothèques

Dans `pubspec.yaml` ajoutez les deux dépendances suivantes


```yaml
  list_french_words: ^0.1.0+1
  recase: ^3.0.0
```

la première va nous donner une liste de 340000 mots français et l'autre va nous permettre de formater les String à notre idée. Le dépot de dart est [ici](https://pub.dev)

dans notre `main.dart` ajoutons un mot français au hasard.

Créons d'abord une classe Mots qui va nous générer tout ça `Mots.dart`dans un nouveau package model:

```dart
import 'dart:math';

import 'package:list_french_words/list_french_words.dart';

class Mots {
  final nb = list_french_words.length;

  random(){
    var rd = new Random();
    int rdFix = rd.nextInt(nb-1);
    return list_french_words[rdFix];
  }
}
```

Ajoutez cette méthode à la fin du `main.dart` (en faisant les import qui vont bien) et modifiez le texte à afficher.


```dart
import 'package:app_one/model/Mots.dart';
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Salut Flutter',
      home: Scaffold(
        appBar: AppBar(
          title: Text('Salut les Flutterinos'),
        ),
        body: Center(
          child: Text(
            new Mots().random(),
            style: TextStyle(fontSize: 24.0),
          ),
        ),
      ),
    );
  }
}

```

![mots](/img/flutter/mots1ios.png)

## Stateless et StateFull

Pour faire un peu plus propre, nous allons utiliser les **widget**.

Nous allons utiliser les widget. Les widget sont statefull ou stateless. C'est à dire:

- **Stateless**: pas d'état donc immuable lors de la vie de l'application
- **Statefull**: ajoute une notion d'état qui permet de faire évoluer le widget. Le widget crée un état et qui persiste et le widget peut être supprimé et généré avec l'état.

A la fin du `main.dart` tapez simplement `stful` et l'IDE génère le code pour vous. Il ne reste qu'à nommer le widget (RandomWords). Ensuite, nous déplaçons le code au bon endroit.


```dart
import 'package:app_one/model/Mots.dart';
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Salut Flutter',
      home: Scaffold(
        appBar: AppBar(
          title: Text('Salut les Flutterinos'),
        ),
        body: Center(
          child: RandomWords(),
        ),
      ),
    );
  }
}

class RandomWords extends StatefulWidget {
  @override
  _RandomWordsState createState() => _RandomWordsState();
}

class _RandomWordsState extends State<RandomWords> {
  @override
  Widget build(BuildContext context) {

    return Text(
      new Mots().random(),
      style: TextStyle(fontSize: 24.0),
    );
  }
}
```

Si vous lancez ce code les changements ne sont pas visibles mais ils seront utils juste après quand nous allons générer plusieurs mots dans une liste.

## Faisons une liste déroulante

D'abord, on va créer une méthode qui nous permettra de générer nos mots.
Modifiez le fichier `Mots.dart`.

```dart
import 'dart:math';

import 'package:list_french_words/list_french_words.dart';
import 'package:recase/recase.dart';

class Mots {
  final nb = list_french_words.length;
/// génère un mot aléatoirement
  random(){
    var rd = new Random();
    int rdFix = rd.nextInt(nb-1);
    return list_french_words[rdFix];
  }

  /// génère une liste aléatoire de 10 mots
  /// de type ReCase
   generateMots() {
    var list = <ReCase>[];
    for (int i=0; i<11;i++){
      list.add(new ReCase(random()));
    }
    return list;
  }
}
```

Nous allons maintenant pouvoir modifier la classe `_RandomWordsState` pour qu'elle génère des mots quand l'utilisateur fait défiler la liste.

On a d'abord besoin de 2 constantes:


```dart
class _RandomWordsState extends State<RandomWords> {
  final List<Mot> _suggestions = <Mot>[];
  ...
```

Puis on ajoute une méthode qui va créer la ListView.


```dart
  Widget _buildSuggestions() {
    var mot = new Mots();
    return ListView.builder(
        padding: EdgeInsets.all(16.0),
        itemBuilder: /*1*/ (context, i) {
          if (i.isOdd) return Divider(); /*2*/

          final index = i ~/ 2; /*3*/
          if (index >= _suggestions.length) {
            _suggestions.addAll(mot.generateMots()); /*4*/
          }
          return _buildRow(_suggestions[index]);
        });
  }
```

La méthode `_buildSuggestions` fait appelle à la méthode `_buildrow` qui construit les lignes

```dart
Widget _buildRow(ReCase suggestion) {
    return ListTile(
      title: Text(
        suggestion.pascalCase,
        style: TextStyle(fontSize: 24.0),
      ),
    );
  }
```

On modifie maintenant la méthode `build` du `_RandomWordsState` pour utiliser ce que nous venons d'écrire


```dart
@override
  Widget build(BuildContext context) {
    return _buildSuggestions();
  }
```

Voilà !!! Vous devriez obtenir quelque chose comme ça

![flutlist](/img/flutter/flutlistios.png)

## Un peu plus de style et des interactions

Nous allons ajouter des icônes à notre design et faire un peu de mise en place pour la suite.

D'abord ajoutez la constante pour sauvegarder les interactions

```dart
...
class _RandomWordsState extends State<RandomWords> {
  final _suggestions = <ReCase>[];
  final _saved = <ReCase>{};
```

On ajoute aussi `alreadySaved` à la méthode `_buildRow` pour s'assurer que la mot n'a pas été ajouté aux favoris.

```dart
Widget _buildRow(ReCase suggestion) {
    final alreadySaved = _saved.contains(suggestion); //ici
    ...
```

Enfin, on ajoute les icones qui auront deux états.

```dart
  Widget _buildRow(ReCase suggestion) {
    final alreadySaved =_saved.contains(suggestion);
    return ListTile(
      title: Text(
        suggestion.pascalCase,
        style: TextStyle(fontSize: 24.0),
      ),
      trailing: Icon(   // ici..
        alreadySaved ? Icons.favorite : Icons.favorite_border,
        color: alreadySaved ? Colors.red : null,
      ),
    );
  }
```

Tada...

![coeur](/img/flutter/coeurios.png)

> Pour le moment nous n'avons pas d'interactions.

Pour activer nos coeurs, nous allons ajouter activer `onTap` pour que l'utilisateur puisse toucher le coeur et `setState` pour sauvegarder l'état

```dart
Widget _buildRow(Mot suggestion) {
    final alreadySaved = _saved.contains(suggestion);
    return ListTile(
      title: Text(
        suggestion.formatPas(),
        style: _biggerFont,
      ),
      trailing: Icon(
        alreadySaved ? Icons.favorite : Icons.favorite_border,
        color: alreadySaved ? Colors.red : null,
      ),
      onTap: () {//d'ici
        setState(() {
          if (alreadySaved) {
            _saved.remove(suggestion);
          } else {
            _saved.add(suggestion);
          }
        });
      },// jusque là
    );
  }
```

Je ne fais pas de screen mais maintenant les cœurs sont cliquables et s'affichent en rouge.

## On ajoute une vue avec les éléments sélectionnés

Pour faire ça on va d'abord ajouter une action sur notre `appBar` dans la méthode build de `_RandomWordsState`

```dart
 @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Mot aléatoires'),
        actions: [ //ici
          IconButton(icon: Icon(Icons.list), onPressed: _pushSaved),
        ],
      ),

      body: _buildSuggestions(),
    );
  }
```

La fonction `_pushSaved` est donc à créer:

```dart
void _pushSaved() {
    Navigator.of(context).push(
      MaterialPageRoute<void>(
        builder: (BuildContext context) {
          final tiles = _saved.map(
                (ReCase pair) {
              return ListTile(
                title: Text(
                  pair.pascalCase,
                  style: TextStyle(fontSize: 24.0),
                ),
              );
            },
          );
          final divided = ListTile.divideTiles(
            context: context,
            tiles: tiles,
          ).toList();

          return Scaffold(
            appBar: AppBar(
              title: Text('Suggestions sauvées'),
            ),
            body: ListView(children: divided),
          );
        },
      ),
    );
  }
```

Cette méthode va créer une nouvelle page à partir des mots que nous aurons sélectionnés.

## Dernier make-up

Nous avons deux barres et une couleur inadaptée. Pour les barres, on va modifier la méthode `build` de `MyApp`

```dart
class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Mes Mots',
      home: RandomWords()
    );
  }
}
```

Enfin un peu d'esthetique:

```dart
@override
  Widget build(BuildContext context) {

    return MaterialApp(
      title: 'Mes Mots',
      debugShowCheckedModeBanner: false, // cacher le ruban debug
      theme: ThemeData(
        primaryColor: Colors.white // le blanc c'est plus chic
      ),
      home: RandomWords()
    );
  }
```

 Et voilà

![screen1](/img/flutter/screen1.png)     ![screen1](/img/flutter/screen2.png)


