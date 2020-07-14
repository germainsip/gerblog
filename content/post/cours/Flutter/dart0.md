---
title: "Dart0"
date: 2020-05-11T15:45:32+02:00
draft: true
description: Dart pour ceux qui connaissent Java
categories:
  - Flutter
tags:
  - Java
  - Dart
  - Flutter
thumbnail: img/dartlogo.png
lead: Dart pour ceux qui connaissent Java
comments: false
authorbox: true
toc: true
mathjax: true
---





## Introduction
Dart est le langage de programmation de Flutter, la boîte à outils de l'interface utilisateur de Google pour créer de belles applications mobiles, Web et de bureau compilées en mode natif à partir d'une base de code unique.

C'est un langage de haut niveau moderne comparable à Swift ou Kotlin.

La plus simple approche est d'utiliser **DartPad**, l'environnement en ligne mis à disposition par Google.
[DartPad](https://dartpad.dev)

<iframe style="width:100%;height:600px;" src="https://dartpad.dev/embed-dart.html?id=a632eeebfb48dc3b10fb7888e13903ff&split=80&theme=dark"></iframe>

Vous pouvez essayer de modifier le code directement dans ces fenêtres et les exécuter en cliquant sur **Run**.

Ou si vous préférez, vous pouvez utiliser un IDE à la place, tel que WebStorm, IntelliJ avec le plug-in Dart ou Visual Studio Code avec l' extension [Dart Code ](https://marketplace.visualstudio.com/items?itemName=Dart-Code.dart-code).

## La syntax de base

Les types de **Dart** sont les mêmes que l'on trouve habituellement.

- les entiers, doubles et numériques:
On ve simplement déclarer le type devant le nom de la variable

```dart
int unEntier;
double unDouble;
// les deux sont initialisés à null
```

> Une particularité ici, tout est objet en dart et ainsi à l'initialisation l'objet est null.

L'affectation se fait comme dans la majorité des langages

```dart
int unEntier;
int unAutreEntier = 12; // à la déclaration

unEntier = 24; //ou après
```

On peut également utiliser le type `num` car `int` et `double` héritent de `num`

```dart
num unEntier = 5;
num unDouble = 326.3;
```

- Les chaines de caractères

Comme dans la majorité des langages nous trouvons aussi les chaines de caractères `String` et les méthodes qui sont associées à cette classe

```dart
String maChaine = 'Salut!'; //Déclaration et initialisation
print(maChaine); // affiche Salut!
print(maChaine.toLowerCase()); // affiche salut!
print(maChaine.toUpperCase()); // affiche SALUT!
```

- Les types dynamiques

En Dart on peut laisser typer le compilateur quand il n'y a pas d'ambiguïté.

```dart
String premièreChaine = 'première';
var secondeChaine = 'seconde';
```

Avec `var`le compilateur reconnaît ici que le type est `String`, néanmoins ce type ne pourra pas changer par la suite.
Nous pouvons aussi utiliser le mot clé `dynamique` qui permet de réaffecte un autre type.

```dart
var nom = 'paul';
dynamic age = 'douze';

age = 12; //changement de type en int
print(age);
```

- Les constantes
Deux types de constantes existent en Dart
- Les collections

- Les alternatives

- Les boucles

## Les fonctions

- Les paramètres optionnels [doc dart](https://dart.dev/guides/language/language-tour#optional-parameters)



## Créez une classe Dart

Au-dessus de la fonction main(), ajoutez une classe `Bicycle` avec trois variables d'instance. Supprimez également le contenu de main(), comme indiqué dans l'extrait de code suivant:

```dart
class Bicycle {
  int cadence;
  int speed;
  int gear;
}

void main() {
}
```

- La méthode principal de Dart est nommé `main()` ou, si vous avez besoin d' accéder à des arguments de ligne de commande, main(List<String> args).
- La méthode `main()` vit au plus haut niveau. Dans Dart, vous pouvez définir du code en dehors des classes. Les variables, fonctions, getters et setters peuvent toutes vivre en dehors des classes.
- Ni `main()` ni `Bicycle` ne sont déclarées comme public, car tous les identifiants sont publics par défaut. Dart n'a pas des mots clés pour `public`, `private` ou `protected`.

## Définition d'un constructeur pour `Bicycle`

Ajoutez le constructeur suivant à la classe `Bicycle`:
`Bicycle(this.cadence, this.speed, this.gear);`

- L'utilisation `this` dans la liste des paramètres d'un constructeur est un raccourci pratique pour attribuer des valeurs aux variables d'instance.
- Le code ci-dessus est équivalent à ce qui suit:

```dart
Bicycle(int cadence, int speed, int gear) {
  this.cadence = cadence;
  this.speed = speed;
  this.gear = gear;
}
```

## Instancions et affichons une instance de `Bicycle`

Ajoutez le code suivant à `main()`

```dart
void main() {
  var bike = new Bicycle(2, 0, 1);
  print(bike);
}
```

On peut supprimer le mot clé `new` (il est facultatif)

```dart
var bike = Bicycle(2, 0, 1);
```

Si vous exécutez ce code, vous devriez avoir la sortie suivante:

```zsh
Instance of 'Bicycle'
```

## Améliorons l'affichage

On peut, comme en java, substituer les méthodes avec `@override`

```dart
@override
String toString() => 'Bicycle: $speed mph';
```

Cette fois ci le `print` affiche:

```zsh
Bicycle: 0 mph
```

<!-- TODO: à compléter -->

