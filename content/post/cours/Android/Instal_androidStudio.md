---
title: Installation d'Android Studio
date: 2020-03-19T10:25:49.000Z
draft: false
description: Didacticiel sur androidStudio
categories:
  - Android
  - IDE
tags:
  - Android
  - Android Studio
thumbnail: img/androidbase.png
lead: Installation de l'environnement de dev
comments: false
authorbox: true
toc: true
---
## Android Studio, c'est quoi?

L'IDE **Android Studio** est financé par Google pour le développement sur ses plateformes Android (mobile, tablette, TV, montre, OS...). Mais cet IDE est basé sur IntelliJ IDEA développé par Jetbrain. Vous pourriez donc aussi bien travailler sur IntelliJ. Je vous conseil néanmoins de télécharger et d'installer Android Studio que je trouve beaucoup plus stable.

## Téléchargement et installation

**prérequis:** avoir un jdk installé sur sa machine

Tout d'abord, il faut télécharger l'installeur sur le site [developer.android.com](https://developer.android.com/studio)

Lancez alors la procédure d'installation en suivant les instructions.



## Émulation d'un mobile

Après avoir installé **Android Studio** il vous faudra créer un émulateur mobile.
**prérequis:** votre BIOS doit permette la virtualisation

<!-- > passez à la section utiliser mon mobile Android si votre ordinateur a une trop petit config pour faire de l'émulation. -->

Au premier démarrage vous aurez cette fenêtre:

![config1](/img/android/confAnd.png)    ![config2](/img/android/confAnd2.png)

sélectionnez **Configure** et **AVD Manager**. C'est là que vous aller créer votre mobile virtuel.

- Créez un nouveau mobile virtuel ![new](/img/android/newAnd.png)
- Choisissez une version de mobile ![mob](/img/android/newAnd2.png)
- Choisissez une version d'OS ![os](/img/android/newAnd3.png)
- Choisissez un nom ![machine](/img/android/newAnd4.png)

Et voilà vous êtes prêt.


<!-- ## Utiliser son mobile Android

Il faut d'abord passer son mobile en mode développeur: -->

