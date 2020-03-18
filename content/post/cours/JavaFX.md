---
title: JavaFX et sauvegarde Partie 1
date: 2020-03-16T15:05:18.000Z
draft: true
description: Didacticiel sur la persistance
categories:
  - JavaFX
  - Persistance
tags:
  - JavaFX
  - Json
  - Jackson
thumbnail: img/construction.jpg
lead: Didacticiel sur la persistance
comments: false
authorbox: true
toc: true
mathjax: true
---

:exclamation: :exclamation: :exclamation: **en cours de rédaction** :exclamation: :exclamation: :exclamation:

## Sauvegardons des données en Json

Plusieurs bibliothèques existent pour exploiter le Json avec Java. Je vous propose d'en essayer 2. Nous utiliserons Json.simple et Jackson. La première est très simple d'utilisation comme son nom l'indique et la deuxième nous donnera plus d'outils pour la gestion de nos objets. Je prend le partie de vous montrer l'application de ces outils avec JavaFX car le **bind** sera particulier dans ce cas.

## Commenceons par l'interface graphique

Créez un projet dans l'IDE de votre choix. J'utiliserai IntelliJ mais les manipulations sont les mêmes dans Netbeans.

Le projet est un projet **maven** suivant l'archetype (`javafx-archetype-fxml`) habituel maintenant:

- Supprimez les fichiers inutiles: `App.java`, `PrimaryController.java`,`SecondaryController.java`, `primary.fxml` et `secondary.fxml`.
- Créez la JavaFX application `App.java`.

```java
public class App extends Application {

    public static void main(String[] args) {
        launch(args);
    }

    @Override
    public void start(Stage primaryStage) {

    }
}
```

- Créez notre layout de test que nous allons nommer `exemple.fxml`

> **Dans IntelliJ:** clique droit sur **resources → FXML File** puis quand le fichier est créé, le nom du contrôleur est en rouge. IntelliJ vous propose de le créer pour vous. Choisissez bien le package principal.

Votre projet devrait avoir l'arborescence suivante.

``` zsh
.
├── java
│   ├── org
│   │   └── afpa
│   │       ├── App.java
│   │       └── Exemplejson.java
│   └── module-info.java
└── resources
    └── org
        └── afpa
            └── exemplejson.fxml
```

Pour le **fxml**, nous allons simplement ajouter un bouton et des label.

![layout1](/img/JavaFX_Json/jfxjson1.png)

<!-- ```xml
<BorderPane prefHeight="257.0" prefWidth="373.0" xmlns="http://javafx.com/javafx/11.0.1" xmlns:fx="http://javafx.com/fxml/1" fx:controller="org.afpa.ExempleController">
   <left>
      <VBox layoutX="48.0" layoutY="63.0" spacing="20.0">
         <children>
            <Button text="Sauver" />
         </children>
         <padding>
            <Insets bottom="10.0" left="10.0" right="10.0" top="10.0" />
         </padding>
      </VBox>
   </left>
   <top>
      <Label text="Mon appli d'Exemple" BorderPane.alignment="CENTER">
         <font>
            <Font name="System Bold" size="24.0" />
         </font>
      </Label>
   </top>
   <center>
      <HBox prefHeight="371.0" prefWidth="284.0">
         <VBox prefHeight="371.0" prefWidth="136.0" spacing="20.0" BorderPane.alignment="CENTER">
            <children>
               <Label text="Nom">
                  <font>
                     <Font name="System Bold" size="13.0" />
                  </font>
               </Label>
               <Label layoutX="10.0" layoutY="10.0" text="Prénom">
                  <font>
                     <Font name="System Bold" size="13.0" />
                  </font>
               </Label>
               <Label layoutX="10.0" layoutY="10.0" text="âge">
                  <font>
                     <Font name="System Bold" size="13.0" />
                  </font>
               </Label>
            </children>
            <padding>
               <Insets bottom="30.0" left="30.0" right="30.0" top="30.0" />
            </padding>
         </VBox>
         <VBox prefHeight="200.0" prefWidth="100.0" spacing="20.0" BorderPane.alignment="CENTER">
            <children>
               <Label text="Label" />
               <Label layoutX="10.0" layoutY="10.0" text="Label" />
               <Label layoutX="10.0" layoutY="10.0" text="Label" />
            </children>
            <padding>
               <Insets bottom="30.0" left="30.0" right="30.0" top="30.0" />
            </padding>
         </VBox>
      </HBox>
   </center>
</BorderPane>
``` -->
> J'ai utilisé un BorderPan et des Box
>
> ![arbo](/img/JavaFX_Json/jfxjson2.png)

Nommez les 3 label afin de les récupérer dans notre appli (`labelNom`, `labelPrenom`, `labelAge`). Et ajoutez une méthode au bouton que nous appelons `sauvBut`.

> si vous utilisez les automatismes de IntelliJ vos label seront déclarés comme ceci `public Label labelNom;`. Je vais les lier avec l'annotation `@FXML` et les rendre `private` pour respecter le fxml et l'encapsulation.

```java
@FXML
private Label labelNom;
@FXML
private Label labelPrenom;
@FXML
private Label labelAge;
@FXML
private Button sauvBut;

@FXML
private void sauvHandler(ActionEvent event) {
  // Nous allons ajouter notre code ici
    }
```

## Créons un objet Client prévu pour JavaFX

Créez une classe `Client.java` dans notre package principale. Comme nous travaillons avec JavaFX, il est plus intéressant d'utiliser des Properties.

```java
private StringProperty nom;
private StringProperty prenom;
private IntegerProperty age;
```

Ces objets sont utilisés par **JavaFX** pour le rechargement automatique de l'affichage. Cependant nos **Getter** et **Setter** seront un peu différents de d'habitude. Heureusement pour nous IntelliJ va très bien s'adapter (`alt inser` pour windows `cmd N` pour mac) et `Getter and Setter`. Voilà le fichier que devriez obtenir.

```java
package org.afpa;

import javafx.beans.property.IntegerProperty;
import javafx.beans.property.StringProperty;

public class Client {

    private StringProperty nom;
    private StringProperty prenom;
    private IntegerProperty age;

    public String getNom() {
        return nom.get();
    }

    public StringProperty nomProperty() {
        return nom;
    }

    public void setNom(String nom) {
        this.nom.set(nom);
    }

    public String getPrenom() {
        return prenom.get();
    }

    public StringProperty prenomProperty() {
        return prenom;
    }

    public void setPrenom(String prenom) {
        this.prenom.set(prenom);
    }

    public int getAge() {
        return age.get();
    }

    public IntegerProperty ageProperty() {
        return age;
    }

    public void setAge(int age) {
        this.age.set(age);
    }
}
```

Je vais m'attarder un peu sur ce code pour vous expliquer ce qu'a fait IntelliJ pour nous:

- Tout d'abord, Les **Getter**, par exemple `getNom()` va chercher la **StringProperty** nom et utilise la méthode `get()` pour la transformer en **String**.
- Pour les **Setter**, même histoire dans l'autre sens. `setNom()` prend une **String** en paramètre, va chercher la **StrinProperty** nom et avec la methode `set()` va transformer cette **String** en **StringProperty**.
- Enfin, il y a les méthodes comme `nomProperty()` qui assure le lien avec **JavaFX**.

Il nous faut aussi un constructeur:

```java
public Client(String nom, String prenom, Integer age) {
        this.nom = new SimpleStringProperty(nom);
        this.prenom = new SimpleStringProperty(prenom);
        this.age = new SimpleIntegerProperty(age);
    }
```

## On alimente et on affiche

J'aurais pu faire autrement mais je vais utiliser l'implémentation de l'interface `Initializable` qui ajoute une méthode `initilize` à l'appelle du controlleur. Cela donne ceci après création et alimentation des champs:

```java
@Override
    public void initialize(URL location, ResourceBundle resources) {
        Client cli1 = new Client("Paul","Auchon",35);

        labelNom.setText(cli1.getNom());
        labelPrenom.setText(cli1.getPrenom());
        labelAge.setText(String.valueOf(cli1.getAge()));
    }
```

Maintenant il ne vous reste qu'à lancer l'afficahge de nos préparatifs pour voir si tout fonctionne.

> Dans la prochaine partie nous verrons comment sauvegarder notre objet dans un json.


