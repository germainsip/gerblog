---
title: "JavaFXelmts"
date: 2020-07-14T17:01:56+02:00
draft: false
description: Didacticiel sur les éléments de JFX
categories:
  - JavaFX
tags:
  - JavaFX
thumbnail: img/JavaFX_Logo.png
lead: Didacticiel sur les éléments de JFX
comments: false
authorbox: true
toc: true
mathjax: true
---
## Les éléments de JavaFX

Vous pouvez trouver un exemple d'utilisation d'une selection d'éléments de JavaFX sur mon dépot [ici](https://github.com/germainsip/gegeLib)

![desktop](/img/gegelib.png)

## Les `Button` et `Label`

Les boutons et les labels sont les premiers élément que l'on utilise simplement dans JFX.
Vous pouvez les déposer simplement dans votre `pane` (que l'on verra après).
Il faut les nommer pour les retrouver ensuite dans le contrôleur.

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

Il faut faire la même chose pour le label

```xml
 <Label fx:id="res" text="Résultat">
```

Il faut ensuite faire le lien avec le contrôleur (L'ide le fait pour vous!!!).

Dans le contrôleur on va retrouver nos éléments:

```java
public Button btn1;
public Button btn2;
public Label res;
```

et notre méthode à activer. Elle va récupérer le texte du bouton et l'envoyer au label.

```java
/**
     * Cette méthode récupère le texte du bouton cliqué
     * et envoie le texte "Le [bouton n°] vient d'être cliqué"
     * au label res
     * @param actionEvent
     */
    public void writeOnLabel(ActionEvent actionEvent) {
        // récupération du bouton cliqué
        Button btnActive = (Button) actionEvent.getSource();
        //construction du message avec le texte du bouton
        String message = "Le "+btnActive.getText()+" vient d'être cliqué!";
        //envoie du message dans le label
        res.setText(message);
    }
```

Le `ToogleButton` est un peu différent. Il admet deux états: sélectionné ou non. En Java ce sera `isSelected()`. Le Toogle est déclaré comme ceci:

```xml
               <ToggleButton fx:id="toggleBtn" mnemonicParsing="false" onAction="#toggleAction" text="ToggleButton" />

```

Il est donc associé à la méthode `toogleAction()` dans le contrôleur:

```java
 public void toggleAction(ActionEvent actionEvent) {
        // à l'activation du bouton toogle on vérifie s'il est sélectionné ou non
       if(toggleBtn.isSelected()){
           //s'il l'est on encadre le texte en vert
           res.setStyle("-fx-border-color: chartreuse; -fx-border-width: 5");
       }else {
           // sinon on rétablie le comportement par défaut
           res.setStyle("");
       }
    }
```

## Les Champs texte

Quand on a besoin de faire des formulaires, on a besoin de champs textuels pour entrer des données. Les `TextField`sont là pour ça.
Il en existe plusieurs:
- Le `TextField`

```xml
<TextField fx:id="nomField" />
```

Il a d'autres attributs accessibles par SceneBuilder ou directement dans le FXML. Comme par exemple le prompt ( équivalent du placeholder en html)

```xml
<TextField fx:id="prenomField" promptText="prénom" />
```

Ajoutons un envoie vers un label et une vérification avec un bouton et une méthode `sendHandle()` (vue précédemment).

```java
public void sendHandle() {

        Boolean flagChampNom,flagChampPrenom,flagChampTel;

        if (Verification.verifTexte(nomField.getText()) ) {
            nomField.setStyle("");
            flagChampNom = true;
        } else {
          // si le test ne passe pas on change la couleur de bordure
            nomField.setStyle("-fx-border-color: darkred");
            flagChampNom = false;
        }if (Verification.verifTexte(prenomField.getText()) ) {
            prenomField.setStyle("");
            flagChampPrenom = true;
        } else {
            nomField.setStyle("-fx-border-color: darkred");
            flagChampPrenom = false;
        }if (Verification.verifNum(telField.getText()) ) {
            telField.setStyle("");
            flagChampTel = true;
        } else {
          // si le test ne passe pas on change la couleur de bordure
            telField.setStyle("-fx-border-color: darkred");
            flagChampTel = false;
        }

        if (flagChampNom && flagChampPrenom && flagChampTel) sendField();
    }

    private void sendField() {
        nomLab.setText(nomField.getText());
        prenomLab.setText(prenomField.getText());
        telLab.setText(telField.getText());
        passLab.setText(passField.getText());
    }
```

Comme vous pouvez le remarquer, il n'y a pas de méthode `verifTexte()` ou `verifNum()`. J'ai préféré organiser le projet en rangeant cette méthode dans un package séparé `org.gerblog.tools.Verification` (soit org/gerblog/tools/Verification.java)

```java
public class Verification {
    final static Pattern texte = Pattern.compile("\\p{IsAlphabetic}*$");
    final static Pattern num = Pattern.compile("\\p{Digit}*$");

    public static boolean verifTexte(String champ){
        return texte.matcher(champ).matches();
    }

    public static boolean verifNum(String champ){
        return num.matcher(champ).matches();
    }
}
```

## Les Slider

Les slider sont des curseurs dont on peut récupérer la valeur.
<!-- à compléter -->

## Les Diagrammes et les Histogrammes

On peu également afficher des graphiques en JavaFX.

- Des `PieChart` ou diagramme circulaire.

![pie](/img/pieelmt.png)

- Des `BarChart` ou diagrammes en baton.

![histo](/img/histoelmt.png)

<!-- à compléter -->
