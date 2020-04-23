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
  - Gradle
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

## Commençons par notre projet JavaFX

{{< youtube Z-36q571Nz8 >}}

Créez un projet Gradle et modifiez le fichier `build.gradle` comme ceci

```gradle
plugins {
    id 'java'
    id 'application'
    id 'org.openjfx.javafxplugin' version '0.0.8'
}

group 'org.gerblog'
version '1.0-SNAPSHOT'

repositories {
    mavenCentral()
}

dependencies {
    testCompile group: 'junit', name: 'junit', version: '4.12'

}
javafx {
    version = "14"
    modules = ['javafx.controls','javafx.base','javafx.graphics','javafx.fxml']
}
```

Ce sera grâce aux plugins `application` et `org.openjfx.javafxplugin` qui vont nous permettre de faire du javafx ainsi que la déclaration des modules nécessaires au fonctionnement de javaFX.

Créez un package dans le dossier java et créez y une classe `Launch.java`:

```zsh
java
└── main
    └── org
        └── gerblog
            └── Launch.class
```

Comme nous savons le faire, créons une simple fenêtre pour tester que notre application fonctionne.

```java
public class Launch extends Application {

    @Override
    public void start(Stage primaryStage) throws Exception {
        Parent root;
        Scene scene = new Scene(new StackPane(new Label("gradle et JavaFX")),200,200);
        primaryStage.setScene(scene);
        primaryStage.show();
    }
}
```

Et enfin il faut déclarer la classe principal dans le `build.gradle` en ajoutant `mainClassName = 'org.gerblog.Launch'`

A droite dans IntelliJ dans le menu gradle dans Tasks/Application double cliquez sur run.

## Utilisons Retrofit

**Retrofit** est une librairie qui va nous permettre de récupérer les données de l'api simplement.

**Retrofit** gère les appel https et un adapter qui va traduire le Json en objets java (POJO).

On commence donc par importer **Retrofit** dans notre projet. Ajoutez ces 2 lignes dans `build.gradle`

```gradle
dependencies {
    //...les autres dépendances

    compile 'com.squareup.retrofit2:retrofit:2.8.1'
    compile 'com.squareup.retrofit2:converter-gson:2.8.1'
}
```

Pour faire fonctionner **Retrofit** nous devons mettre en place le service de consommation de l'api. Créez un package `datafetch` et dans celui ci créez une classe `DataProviderService.java`

```java
public class DataProviderService {
    public void getData(String countryName){
        Retrofit retrofit = new Retrofit.Builder()
                .baseUrl("https://coronavirus-19-api.herokuapp.com/")
                .addConverterFactory(GsonConverterFactory.create())
                .build();
    }
```

La méthode getData() va nous permettre de récupérer les données.
Le builder de **Retrofit** utilise l'adresse de base de l'api. Et on lui ajout le convertisseur Gson.

Retrofit a besoin aussi d'une interfaces de connexion

Créez l'interface CovidApi.java

```java
public interface CovidApi {
    // la requête get pour avoir toutes les infos
    @GET("https://coronavirus-19-api.herokuapp.com/all")
    Call<JsonObject> getGlobalData();
    //La requête pour avoir uniquement le pays qui nous intéresse
    @GET("https://coronavirus-19-api.herokuapp.com/countries/{countryName}")
    Call<JsonObject> getCountryData(@Path(value="countryName") String countryName);
}
```

Retournons dans le service et faisons appel à l'interface pour créer l'objet de connexion

```java
...

CovidApi covidApi = retrofit.create(CovidApi.class);

        covidApi.getGlobalData()
        .enqueue(new Callback<JsonObject>() {
            @Override
            public void onResponse(Call<JsonObject> call, Response<JsonObject> response) {
                System.out.println(response.body());
            }

            @Override
            public void onFailure(Call<JsonObject> call, Throwable t) {

            }
        });
```

On a plus qu'a tester ça.

Dans `Launch.java` ajoutez un appel à notre `getData()` dans la méthode `start()`

```java
...
new DataProviderService().getData("France");
...
```

Vous devriez avoir en console les chiffres du jour.

## Faisons des POJO

D'abord POJO c'est Plain Old Java Object [POJO pour wikipedia](https://fr.wikipedia.org/wiki/Plain_old_Java_object)

En d'autre terme on va créer la correspondance en entre le json et un objet java.
Trop simple quand on a les bons outils. Je vous invite à utiliser une extension intelliJ (Json2Pojo), mais vous pouvez trouver d'autres outils en ligne qui marchent très bien pour convertir un objet json en objet java. (http://www.jsonschema2pojo.org ou https://codebeautify.org/json-to-java-converter ... il en existe beaucoup).

Après avoir converti et nettoyé notre objet vous devriez avoir ça pour l'objet correspondant à la requête all:

```java
public class GlobalData {

    private long cases;
    private long deaths;
    private long recovered;

    public long getCases() {
        return cases;
    }

    public void setCases(long cases) {
        this.cases = cases;
    }

    public long getDeaths() {
        return deaths;
    }

    public void setDeaths(long deaths) {
        this.deaths = deaths;
    }

    public long getRecovered() {
        return recovered;
    }

    public void setRecovered(long recovered) {
        this.recovered = recovered;
    }}
```

et pour la recherche par ville:

```java
public class CountryData {

    private String country;
    private Long active;
    private Long cases;
    private Long casesPerOneMillion;
    private Long critical;
    private Long deaths;
    private Long deathsPerOneMillion;
    private Long recovered;
    private Long testsPerOneMillion;
    private Long todayCases;
    private Long todayDeaths;
    private Long totalTests;

    public String getCountry() {
        return country;
    }

    public void setCountry(String country) {
        this.country = country;
    }

    public Long getActive() {
        return active;
    }

    public void setActive(Long active) {
        this.active = active;
    }

    public Long getCases() {
        return cases;
    }

    public void setCases(Long cases) {
        this.cases = cases;
    }

    public Long getCasesPerOneMillion() {
        return casesPerOneMillion;
    }

    public void setCasesPerOneMillion(Long casesPerOneMillion) {
        this.casesPerOneMillion = casesPerOneMillion;
    }

    public Long getCritical() {
        return critical;
    }

    public void setCritical(Long critical) {
        this.critical = critical;
    }

    public Long getDeaths() {
        return deaths;
    }

    public void setDeaths(Long deaths) {
        this.deaths = deaths;
    }

    public Long getDeathsPerOneMillion() {
        return deathsPerOneMillion;
    }

    public void setDeathsPerOneMillion(Long deathsPerOneMillion) {
        this.deathsPerOneMillion = deathsPerOneMillion;
    }

    public Long getRecovered() {
        return recovered;
    }

    public void setRecovered(Long recovered) {
        this.recovered = recovered;
    }

    public Long getTestsPerOneMillion() {
        return testsPerOneMillion;
    }

    public void setTestsPerOneMillion(Long testsPerOneMillion) {
        this.testsPerOneMillion = testsPerOneMillion;
    }

    public Long getTodayCases() {
        return todayCases;
    }

    public void setTodayCases(Long todayCases) {
        this.todayCases = todayCases;
    }

    public Long getTodayDeaths() {
        return todayDeaths;
    }

    public void setTodayDeaths(Long todayDeaths) {
        this.todayDeaths = todayDeaths;
    }

    public Long getTotalTests() {
        return totalTests;
    }

    public void setTotalTests(Long totalTests) {
        this.totalTests = totalTests;
    }}
```

Ajoutez à nos deux objets un @override de toString pour que ça nous affiche ce que l'on veut et pas la méthode par défaut de java.

Par exemple, voilà celle de `GlobalData.java`

```java
@Override
    public String toString() {
        return "GlobalData{" +
                "cases=" + cases +
                ", deaths=" + deaths +
                ", recovered=" + recovered +
                '}';
    }
```

## Récupérons tout ça avec Retrofit

Nous allons modifier les types dans l'interface `CovidApi.java` en remplaçant les JsonObject par l'objet qui correspond.

```java
public interface CovidApi {
    @GET("https://coronavirus-19-api.herokuapp.com/all")
    Call<GlobalData> getGlobalData();

    @GET("https://coronavirus-19-api.herokuapp.com/countries/{countryName}")
    Call<CountryData> getCountryData(@Path(value="countryName") String countryName);
}
```

Maintenant, le convertisseur va faire le travail pour nous et convertir le Json en POJO (งツ)ว.

Jusqu'ici la méthode getData du DataProviderService était vide, nous alons créer un objet pour réceptionner les valeurs et l'appeler `CoviDataModel`

```java
public class CovidDataModel {
    private GlobalData globalData;
    private CountryData countryData;

    public GlobalData getGlobalData() {
        return globalData;
    }

    public CountryData getCountryData() {
        return countryData;
    }
}
```

Nous devons changer le retour de `getData` dans le Provider de void à `CovidDataModel` et pouvoir renvoyer en même temps les deux valeurs (Celà impose l'utilisation de `CompletableFuture` une méthode qui permet d'attendre que les 2 résultats soient chargés).

Du coup pas mal de changements:

```java
public class DataProviderService {
    public CovidDataModel getData(String countryName) {
        Retrofit retrofit = new Retrofit.Builder()
                .baseUrl("https://coronavirus-19-api.herokuapp.com/")
                .addConverterFactory(GsonConverterFactory.create())
                .build();
        CovidApi covidApi = retrofit.create(CovidApi.class);
// premier Future
        CompletableFuture<GlobalData> callback1 = new CompletableFuture<>();

        covidApi.getGlobalData()
                .enqueue(new Callback<GlobalData>() {
                    @Override
                    public void onResponse(Call<GlobalData> call, Response<GlobalData> response) {
                        callback1.complete(response.body());
                    }

                    @Override
                    public void onFailure(Call<GlobalData> call, Throwable t) {
                        callback1.completeExceptionally(t);
                    }
                });
                // Deuxième Future
        CompletableFuture<CountryData> callback2 = new CompletableFuture<>();

        covidApi.getCountryData(countryName)
                .enqueue(new Callback<CountryData>() {
                    @Override
                    public void onResponse(Call<CountryData> call, Response<CountryData> response) {
                        callback2.complete(response.body());
                    }

                    @Override
                    public void onFailure(Call<CountryData> call, Throwable t) {
                        callback2.completeExceptionally(t);
                    }
                });
// L'obtention des Futures
        GlobalData globalData = callback1.join();
        CountryData countryData = callback2.join();
// retour du model
        return new CovidDataModel(globalData,countryData);
    }
}
```

Pour le model on lui ajoute un constructeur et une méthode toString

```java
public class CovidDataModel {
    private GlobalData globalData;
    private CountryData countryData;

    @Override
    public String toString() {
        return "CovidDataModel{" +
                "globalData=" + globalData +
                ", countryData=" + countryData +
                '}';
    }

    public CovidDataModel(GlobalData globalData, CountryData countryData) {
        this.globalData = globalData;
        this.countryData = countryData;
    }

    public GlobalData getGlobalData() {
        return globalData;
    }

    public CountryData getCountryData() {
        return countryData;
    }
}
```

et le launch...

```java
...
CovidDataModel dataModel = new DataProviderService().getData("France");
System.out.println(dataModel);
...
```

Maintenant si vous exécutez l'appli, elle va chercher les info sur l'api et construit un objet prenant les infos dont nous allons nous servir.

> Le résultat que vous devriez avoir en console

```zsh
CovidDataModel{globalData=GlobalData{cases=2661504, deaths=185504, recovered=730727}, countryData=CountryData{country='France', active=97880, cases=159877, casesPerOneMillion=2449, critical=5218, deaths=21340, deathsPerOneMillion=327, recovered=40657, testsPerOneMillion=7103, todayCases=0, todayDeaths=0, totalTests=463662}}
```

## Un peu de design maintenant

On a écrit beaucoup de code. Amusons nous avec l'apsect graphique du widget.



<!--## Maintenant, l'aspect graphique du widget

- Créez un nouveau projet Gradle javaFX (Je ferai un tuto sur cette technique)
- Créez votre package principal

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

## Rendre la fenêtre sans bordure et draggable

On va d'abord transformer l'affichage pour avoir une fenêtre un peu transparente et sans bordure.

Je vais ajouter une autre scene, un peu comme un conteneur qui sera utilisé plus tard. Ensuite, je vais définir la transparence et cacher la bordure.


```java
//Le primaryStage devient le conteneur
primaryStage.initStyle(StageStyle.UTILITY);
//On le rend transparent
primaryStage.setOpacity(0);
primaryStage.show();

//On défini un nouveau stage
Stage secondaryStage = new Stage();
//On enlève les bordures
secondaryStage.initStyle(StageStyle.UNDECORATED);
//On l'attache à primaryStage
secondaryStage.initOwner(primaryStage);
Parent root = FXMLLoader.load(App.class.getResource("widget.fxml"));
Scene scene = new Scene(root);
//On règle la transparence à 0.8
secondaryStage.setOpacity(0.8);
secondaryStage.setScene(scene);
secondaryStage.show();
```

Vous devriez avoir maintenant une fenêtre au centre de l'écran.
Par contre, nous avons 2 problèmes... On ne peut plus la déplacer et on ne peut plus la fermer non plus.

On met la fenêtre en haut à droite en ajoutant ça

```java
// Aligner la fenêtre en haut à droite
//on repère les limites de l'écran
Rectangle2D visualBounds = Screen.getPrimary().getVisualBounds();
// on change les coordonnées de la fenêtre
secondaryStage.setX(visualBounds.getMaxX() - 25 - scene.getWidth());
secondaryStage.setY(visualBounds.getMinY() + 25);
```

Pour faire glisser la fenêtre on va définir le décalage avec:


```java
private double xOffset;
private double yOffset;
```

en haut de la classe et ajouter l'évenement dans la méthode start:


```java
//On fait glisser la fenêtre avec la souris
//On récupère les offsets
scene.setOnMousePressed(event -> {
    xOffset = secondaryStage.getX() - event.getScreenX();
    yOffset = secondaryStage.getY() - event.getScreenY();
});
//On fait bouger la fenêtre
scene.setOnMouseDragged(event -> {
    secondaryStage.setX(event.getScreenX() + xOffset);
    secondaryStage.setY(event.getScreenY() + yOffset);
});
```

Testez, ça bouge...

![widget gif](/img/JavaFX_Json/widget1.gif)

Réglons maintenant le problème de fermeture de l'appli.

- On ajoute un menu contextuel à ma fenêtre dans le controlleur.
- On ajoute l'interface `Initializable` à notre controlleur et dans la méthode `initialize()` et on lance le menu.

```java
public class WidgetController implements Initializable {
   // N'oubliez pas de nommer l'AnchorPane qui sert d'ancre à notre menu
    @FXML
    public AnchorPane rootPane;

    @Override
    public void initialize(URL location, ResourceBundle resources) {
        initializeContextMenu();
    }

   private void initializeContextMenu() {
   // le bouton quitter et son action
       MenuItem exitItem = new MenuItem("Quitter");
       exitItem.setOnAction(event -> {
           System.exit(0);
       });
   // le menu qui s'active au clique droit et se cache au clique gauche
       final ContextMenu contextMenu = new ContextMenu(exitItem);
       rootPane.addEventHandler(MouseEvent.MOUSE_PRESSED, event -> {
           if(event.isSecondaryButtonDown()){
               contextMenu.show(rootPane,event.getScreenX(),event.getScreenY());
           } else {
            if (contextMenu.isShowing()){
                contextMenu.hide();
            }
        }
    });
    }
}
```

Maintenant vous avez la possibilité de quitter l'appli.

## On attaque l'api

Tout d'abord, je vais utiliser [retrofit2](https://square.github.io/retrofit/)

C'est une librairie qui va nous permettre de gérer efficacement la consommation d'api en json.

Vous devez l'**inclure** dans vos dépendances:

```xml
<dependency>
  <groupId>com.squareup.retrofit2</groupId>
  <artifactId>retrofit</artifactId>
  <version>(insert latest version)</version>
</dependency>
```

Pour aller plus vite nous allons utiliser un outils en ligne (on peu l'installer en local via homebrew également). Cet outil va nous générer le **POJO** à partir du json.

POJO, c'est quoi? C'est l'objet qui va correspondre à la représentation json mais en Java cette fois.

Créons notre classe -->


