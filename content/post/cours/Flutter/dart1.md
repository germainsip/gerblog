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

![palette](img/palette.png)

Il vous sera demandé le dossier de destination pour la création du projet.

Un fois la procédure terminée, vous pouvez tester le modèle proposé.

...

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

