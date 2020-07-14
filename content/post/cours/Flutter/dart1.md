---
title: Flutter et Dart
date: 2020-03-24T16:29:46.000Z
draft: true
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

On utilise `MaterialApp` pour construire l'application ce qui nous permet d'utiliser la bibliothèque d'éléments pré-construits.

## Allons un peu plus loin avec des imports de bibliothèques

Dans `pubspec.yaml` ajoutez les deux dépendances suivantes


```yaml
  list_french_words: ^0.1.0+1
  recase: ^3.0.0
```

la première va nous donner une liste de 340000 mots français et l'autre va nous permettre de formater les String à notre idée. Le dépot de dart est [ici](https://pub.dev)

dans notre `main.dart` ajoutons un mot français au hasard.

avec cette méthode:

```dart
String genMot() {
    var rand = new Random();// générateur de nombres aléatoires
    var numMot = rand.nextInt(list_french_words.length);//un nombre compris entre le début et la fin de la liste
    var mot = list_french_words[numMot];// on récupère le mot correspondant
    mot = mot.pascalCase; // on le formate en pascalCase
    return mot;
  }
```

Ajoutez cette méthode à la fin du `main.dart` (en faisant les import qui vont bien) et modifiez le texte à afficher.


```dart
import 'dart:math';
import 'package:flutter/material.dart';
import 'package:list_french_words/list_french_words.dart';
import 'package:recase/recase.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Salut Fluttos',
      home: Scaffold(
        appBar: AppBar(
          title: const Text('Salut Les Fluttos'),
        ),
        body:  Center(
          child: Text(genMot()),// ici !!!
        ),
      ),
    );
  }

  String genMot() {
    var rand = new Random();
    var numMot = rand.nextInt(list_french_words.length);
    var mot = list_french_words[numMot];
    mot = mot.pascalCase;
    return mot;
  }
}
```

![mots](/img/flutter/mots1.png)

## Stateless et StateFull

Pour faire un peu plus propre, nous allons utiliser les **widget**. D'abord, sortons notre méthode du fichier et créons un nouveau fichier dart `Mots.dart`

```dart
import 'dart:math';
import 'package:recase/recase.dart';
import 'package:list_french_words/list_french_words.dart';

String genMot() {
  var rand = new Random();
  var numMot = rand.nextInt(list_french_words.length);
  var mot = list_french_words[numMot];
  mot = mot.pascalCase;
  return mot;
}
```

Nous allons utiliser les widget. Les widget sont statefull ou stateless. C'est à dire:

- Stateless: pas d'état donc immuable lors de la vie de l'application
- Statefull: ajoute une notion d'état qui permet de faire évoluer le widget. Le widget crée un état et qui persiste et le widget peut être supprimé et généré avec l'état.

A la fin du `main.dart` tapez simplement `stful` et l'IDE génère le code pour vous. Il ne reste qu'à nommer le widget (RandomWords). Ensuite, nous faisons appel à la méthode que nous avons déplacé.


```dart
import 'dart:math';
import 'package:flutter/material.dart';
import 'package:list_french_words/list_french_words.dart';
import 'package:recase/recase.dart';

import 'Mots.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Salut Fluttos',
      home: Scaffold(
        appBar: AppBar(
          title: const Text('Salut Les Fluttos'),
        ),
        body:  Center(
          child: RandomWords(),// remplacement par le widget
        ),
      ),
    );
  }
}
// notre statefull widget
  class RandomWords extends StatefulWidget {
    @override
    _RandomWordsState createState() => _RandomWordsState();
  }
// son état
  class _RandomWordsState extends State<RandomWords> {
    @override
    Widget build(BuildContext context) {
      final word = genMot();
      return Text(word);
    }
  }
```

Si vous lancez ce code les changements ne sont pas visibles mais ils seront utils juste après quand nous allons générer plusieurs mots dans une liste.

## Faisons une liste déroulante

D'abord, on va créer une classe `Mot` qui nous permettra de générer nos mots.
Modifiez le fichier `Mots.dart`.

```dart
import 'dart:math';
import 'package:recase/recase.dart';
import 'package:list_french_words/list_french_words.dart';

class Mot {
  String mot;

//constructeur
  Mot(){
    this.mot = genMot();
  }
// Méthode de génération aléatoire
  String genMot() {
    var rand = new Random();
    var numMot = rand.nextInt(list_french_words.length);
    var mot = list_french_words[numMot];
    this.mot = mot.pascalCase;
    return mot;
  }
// Méthode de formatage
  String formatPas(){
    return mot.pascalCase;
  }
}
```

Nous allons maintenant pouvoir modifier la classe `_RandomWordsState` pour qu'elle génère des mots quand l'utilisateur fait défiler la liste.

On a d'abord besoin de 2 constantes:


```dart
class _RandomWordsState extends State<RandomWords> {
  final List<Mot> _suggestions = <Mot>[];
  final _biggerFont = TextStyle(fontSize: 18.0);
  ...
```

Puis on ajoute une méthode qui va créer la ListView.


```dart
 Widget _buildSuggestions() {
    return ListView.builder(
        padding: const EdgeInsets.all(16),
        // Le builder est appelé à chaque génération de mot et ajoute un séparateur pour les i impaires et une ligne avec un mot pour les i paires
        itemBuilder: (BuildContext _context, int i) {
          // ajout d'un séparateur avant chaque ligne
          if (i.isOdd) {
            return Divider();
          }
          //On récupère l'index grace à une division entière pour en lever les séparateurs
          final int index = i ~/ 2;
          // Si on atteint la fin des mot disponibles
          if (index >= _suggestions.length) {
            //on génère d'autre mots
            Iterable<Mot> generatedMot = [Mot(), Mot(), Mot(), Mot()];
            _suggestions.addAll(generatedMot);
          }
          return _buildRow(_suggestions[index]);
        });
  }
```

La méthode `_buildSuggestions` fait appelle à la méthode `_buildrow` qui construit les lignes

```dart
Widget _buildRow(Mot suggestion) {
    return ListTile(
      title: Text(
        suggestion.formatPas(),
        style: _biggerFont,
      ),
    );
  }
```

On modifie maintenant la méthode `build` du `_RandomWordsState` pour utiliser ce que nous venons d'écrire


```dart
@override
  Widget build(BuildContext context) {
    //final word = Mot(); on supprime ça
    //return Text(word.formatPas()); et ça
    return Scaffold(
      appBar: AppBar(
        backgroundColor: Colors.lightGreen, // un peu de style ça fait pas de mal
        title: Text('Salut Fluttos'),
      ),
      body: _buildSuggestions(), // notre méthode est appelée ici
    );
  }
```

On fait la même chose pour la méthode `build` de `MyApp`


```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false, // enlever le bandeau debug
      title: 'Salut Fluttos',
      home: RandomWords(), // notre travail précédent
    );
  }
}
```

Voilà !!! Vous devriez obtenir quelque chose comme ça

![flutlist](/img/flutter/flutlist.png)

## Un peu plus de style et des interactions

Nous allons ajouter des icones à notre design et faire un eu de mise en place pour la suite.

D'abord ajoutez la constante pour sauvegarder les interactions

```dart
...
class _RandomWordsState extends State<RandomWords> {
  final List<Mot> _suggestions = <Mot>[];
  final _saved = Set<Mot>();// ici
  final _biggerFont = TextStyle(fontSize: 18.0);
```

On ajoute aussi `alreadySaved` à la méthode `_buildRow` pour s'assurer que la mot n'a pas été ajouté aux favoris.

```dart
Widget _buildRow(Mot suggestion) {
    final alreadySaved = _saved.contains(suggestion); //ici
    ...
```

Enfin, on ajoute les icones qui auront deux états.


```dart
  Widget _buildRow(Mot suggestion) {
    final alreadySaved = _saved.contains(suggestion);
    return ListTile(
      title: Text(
        suggestion.formatPas(),
        style: _biggerFont,
      ),
      trailing: Icon(
        alreadySaved ? Icons.favorite : Icons.favorite_border, // un coeur plein si sauvé, un coeur vide sinon
        color: alreadySaved ? Colors.red : null, // rouge ou vide
      ),
    );
  }
```

Tada...

![coeur](/img/flutter/icons.png)

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

