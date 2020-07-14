---
title: "JavaFXelmts"
date: 2020-07-14T17:01:56+02:00
draft: false
description: Didacticiel sur les éléments de JFX
categories:
  - JavaFX
tags:
  - JavaFX
thumbnail: img/jfxjsonhead.png
lead: Didacticiel sur les éléments de JFX
comments: false
authorbox: true
toc: true
mathjax: true
---
## Les `Button` et `Label`

Les boutons et les labels sont les premiers élément que l'on utilise simplement dans JFX.
Vous pouvez les déposer simplement dans votre `pane` (que l'on verra après).
Il faut les nommer pour les retrouver ensuite dans le controlleur.

Dans l'exemple, nous avons 3 boutons.
![lesboutons](/img/lesboutons.png)

Le **bouton 1** a un nom `btn1` et est associé à une méthode `writeOnLabel` comme on le voit dans sa transcription en fxml.


```xml
<Button fx:id="btn1" mnemonicParsing="false" onAction="#writeOnLabel" text="Bouton 1" />
```

Le bouton 2 vise la même méthode et le bouton 3 n'est pas actif (`disable="true"`).

```xml
<Button fx:id="btn2" mnemonicParsing="false" onAction="#writeOnLabel" text="Bouton 2" />
<Button disable="true" mnemonicParsing="false" text="Bouton 3" />
```