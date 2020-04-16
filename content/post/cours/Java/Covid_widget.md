---
title: Covid_widget
date: 2020-04-16T08:10:03.000Z
draft: false
description: 'Tuto du moment, un widget pour les stats du covid-19'
categories:
  - JavaFX
  - Persistance
tags:
  - JavaFX
  - Json
  - Gson
  - Retrofit
  - Covid-19
thumbnail: img/corona.png
lead: 'Tuto du moment, un widget pour les stats du covid-19'
comments: false
authorbox: true
toc: true
mathjax: true
---
## Un Widget pour connaître les chiffres du covid

J'ai découvert grâce à [Muhammed Afsal Villan](https://github.com/afsalashyana) une api et une méthode pour faire un widget qui nous donnera les stats du COVID-19 en direct.

## D'abord l'API

A l'adresse suivant https://coronavirus-19-api.herokuapp.com vous avez une api faite par [Javier Aviles](https://github.com/javieraviles) qui nous donne les chiffres du covid-19.
Son utilisation est simple vous allez voire

Entrez https://coronavirus-19-api.herokuapp.com/countries/france dans insomnia et vous allez recevoir le json suivant:

```json
{
  "country": "France",
  "cases": 147863,
  "todayCases": 0,
  "deaths": 17167,
  "todayDeaths": 0,
  "recovered": 30955,
  "active": 99741,
  "critical": 6457,
  "casesPerOneMillion": 2265,
  "deathsPerOneMillion": 263,
  "totalTests": 333807,
  "testsPerOneMillion": 5114
}
```

## Je vais commencer par l'aspect graphique du widget

- Créez un nouveau projet Maven javaFX
- Vous allez supprimer les fichiers du modèle et suivez le schema suivant:

```zsh
.
├── java
│   └── org
│       └── gerblog
│           ├── gui
│           │   ├── widget
│           │   │   └── WidgetController.java
│           │   └── Launch.java
│           └── App.java
└── resources
    └── org
        └── gerblog
            ├── main_style.css
            └── widget.fxml
```

La classe `App.java` va servir à duper la JVM pour faire fonctionner JavaFX.

```java
public class App {
    public static void main(String[] args) {
        Launch.main(args);
    }
}
```

Pour le moment on va juste définir un simple affichage.

```java
public class Launch extends Application {

    @Override
    public void start(Stage primaryStage) throws Exception {
        Parent root = FXMLLoader.load(App.class.getResource("widget.fxml"));
        Scene scene = new Scene(root);
        primaryStage.setScene(scene);
        primaryStage.show();
    }

    public static void main(String[] args) {
        launch(args);
    }
}
```

N'oubliez pas de créer le fichier `widget.fxml` dans les ressources et le controlleur associé dans `gui/widget`.

Pour le moment rien de fou... Mais testez le quand même.

## Un peu de design

On va imbriquer des VBox et des Hbox pour la mise en page

```xml
<AnchorPane stylesheets="@main_style.css" xmlns="http://javafx.com/javafx/10.0.2-internal" xmlns:fx="http://javafx.com/fxml/1" fx:controller="org.gerblog.gui.widget.WidgetController">
   <children>
      <VBox AnchorPane.bottomAnchor="20.0" AnchorPane.leftAnchor="20.0" AnchorPane.rightAnchor="20.0" AnchorPane.topAnchor="20.0">
         <children>
            <Label styleClass="main-title" text="Les Chiffres du Covid-19" />
            <HBox alignment="CENTER_LEFT" spacing="20.0">
               <children>
                  <FontIcon iconColor="white" iconLiteral="fa-globe" iconSize="30" />
                  <Text fx:id="textGlobalReport" styleClass="content-text" strokeType="OUTSIDE" strokeWidth="0.0" text="Cas: ... | Guéris: ... | Morts : ..." />
               </children></HBox>
            <HBox spacing="20.0">
               <children>
                  <Text fx:id="textCountryCode" styleClass="country-title" strokeType="OUTSIDE" strokeWidth="0.0" text="FR" />
                  <Text fx:id="textCountryReport" styleClass="content-text" strokeType="OUTSIDE" strokeWidth="0.0" text="Cas: ... | Guéris: ... | Morts: ..." />
               </children>
            </HBox>
         </children>
      </VBox>
   </children>
</AnchorPane>
```

Si vous copiez directement ce code, ça va pas marcher!!!

Si vous lisez le contenu, vous vous apercevez que j'ai ajouté des choses nouvelles. D'abord, il y a le `FontIcon`.

Il faudra d'abord ajouter **ikonli** à vos dépendances dans le `pom.xml`


```xml
...le texte qui précède
<dependencies>
       ... autres dépendances
        <dependency>
            <groupId>org.kordamp.ikonli</groupId>
            <artifactId>ikonli-fontawesome-pack</artifactId>
            <version>11.4.0</version>
        </dependency>
        <dependency>
            <groupId>org.kordamp.ikonli</groupId>
            <artifactId>ikonli-javafx</artifactId>
            <version>11.4.0</version>
        </dependency>
        ... autres dépendances
    </dependencies>
...
```

ikonly nous permet d'ajouter des icones fontawesome (et ça c'est cool!!!).

Ensuite, vous voyez aussi que j'ai ajouté des `styleClass="..."` car nous allons utiliser le CSS.

En haut de `widget.fxml` on déclare l'emplacement du fichier de style et on va remplir le style dans `main-style.css`.

```css
* {
    -fx-base: #232323;
    -fx-font-family: 'Hack', 'Noto Mono', Arial;
}

.main-title {
    -fx-font-weight: bold;
    -fx-font-size: 20pt;
}


.content-text {
    -fx-font-size: 14pt;
    -fx-fill: white;
}

.country-title {
    -fx-font-size: 16pt;
    -fx-fill: white;
}
```

Si tout est bien en place chez vous, après compilation et lancement on devrait obtenir ça:

![wiget1](/img/JavaFX_Json/widget1.png)
