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


{{<youtube Q0ltx0oAFd8>}}


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


{{<youtube hcMuHtBEBxs>}}

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

{{<youtube XbIl5VG1CGw>}}


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

{{<youtube yPSg1ylDpQs>}}

On a écrit beaucoup de code. Amusons nous avec l'apsect graphique du widget.

Créons un package `gui/widget`. A l'intérieur, créez un fxml `widget.fxml` et son controlleur `Widgetcontroller.java`

Voilà le contenu du fxml

```xml
<AnchorPane xmlns="http://javafx.com/javafx/11.0.1" xmlns:fx="http://javafx.com/fxml/1" fx:controller="org.gerblog.gui.widget.WidgetController">
   <children>
      <VBox AnchorPane.bottomAnchor="20.0" AnchorPane.leftAnchor="20.0" AnchorPane.rightAnchor="20.0" AnchorPane.topAnchor="20.0">
         <children>
            <Label text="Infos sur le Covid" />
            <HBox alignment="CENTER_LEFT" spacing="10.0">
               <children>
                  <FontIcon iconLiteral="fa-globe" />
                  <Text strokeType="OUTSIDE" strokeWidth="0.0" text="Cas: ... | Guéris: ... | Morts: ..." />
               </children>
            </HBox>
            <HBox alignment="CENTER_LEFT" layoutX="10.0" layoutY="27.0" spacing="10.0">
               <children>
                  <Text strokeType="OUTSIDE" strokeWidth="0.0" text="FR" />
                  <Text strokeType="OUTSIDE" strokeWidth="0.0" text="Cas: ... | Guéris: ... | Morts: ..." />
               </children>
            </HBox>
         </children>
      </VBox>
   </children>
</AnchorPane>
```

Si vous copiez directement ce code, ça va pas marcher!!!

Si vous lisez le contenu, vous vous apercevez que j'ai ajouté des choses nouvelles. D'abord, il y a le `FontIcon`.

Il faut ajouter une dépendace à notre projet dans `build.gradle`:

```gradle
  compile 'org.kordamp.ikonli:ikonli-javafx:11.4.0'
    compile 'org.kordamp.ikonli:ikonli-fontawesome-pack:11.4.0'
```

Cela nous permet d'utiliser les fontawesome qui produisent de icones (et ça c'est trop cool...).

## Un peu de CSS

C'est un peu compliqué de détailler cette partie ici. Je le fais dans la vidéo.

Vous allez ajouter un package `style` et un fichier `main_style.css` dont le contenu sera

```css
* {
    -fx-base: #232323;
    -fx-font-family: "Hack Nerd Font", "Noto Mono", Arial;
}

.main-title {
    -fx-font-weight: bold;
    -fx-font-size: 20pt;
}

.country-title {
    -fx-font-size: 16pt;
    -fx-fill: white;
}

.content-text {
    -fx-font-size: 14pt;
    -fx-fill: white;
}
```

après avoir affecté les classes et fait quelques changements le fxml devient

```xml
<AnchorPane fx:id="rootPane" stylesheets="@main_style.css" xmlns="http://javafx.com/javafx/10.0.2-internal" xmlns:fx="http://javafx.com/fxml/1" fx:controller="org.gerblog.gui.widget.WidgetController">
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

Et visuellement vous devez obtenir

![apercu](/img/apercu-widget.png)

> J'en ai profité pour nommer les éléments dont nous aurons besoin.

Il reste alors à ajouter les référeces de nos champs dans le controlleur et implémenter `Initializable`

```java
public class WidgetController implements Initializable {

    @FXML
    public AnchorPane rootPane;
    @FXML
    public Text textGlobalReport,textCountryCode,textCountryReport;

    @Override
    public void initialize(URL location, ResourceBundle resources) {

    }
}
```

Dans `Launch` on fait les modifiations pour le fxml

```java
Parent root = FXMLLoader.load(getClass().getResource("/org/gerblog/gui/widget/widget.fxml"));
Scene scene = new Scene(root);
```

Et pour que gradle ait accés au css et fxml on ajoute ces 2 lignes à build.gradle

```gradle
sourceSets.main.resources.srcDirs("src/main/java").includes.addAll(["**/*.fxml", "**/*.css"])
sourceSets.main.resources.srcDirs("src/main/resources").includes.addAll(["**/*.*"])
```

## On widgetise la fenêtre

{{<youtube G_D42piHSR8>}}

Pour enlever la bordure, il suffit d'ajouter un réglage au `primaryStage`

```java
primaryStage.initStyle(StageStyle.UNDECORATED);
```

Testez... La fenêtre n'a plus de bordure mais elle apparait toujours dans la barre des taches.

La technique pour faire disparaitre tout ça on va ajouter un `stage` supplémentaire


```java
public void start(Stage primaryStage) throws Exception {
    // le stage fantome
        primaryStage.initStyle(StageStyle.UTILITY);
        primaryStage.setOpacity(0);
        primaryStage.show();
    // le stage sans bordure contenu dans le premier
        Stage secondaryStage = new Stage();
        secondaryStage.initStyle(StageStyle.UNDECORATED);
        secondaryStage.initOwner(primaryStage);
    // On reprend le même code que précédemment en changeant juste le nom du stage
        Parent root = FXMLLoader.load(getClass().getResource("/org/gerblog/gui/widget/widget.fxml"));
        Scene scene = new Scene(root);
        secondaryStage.setScene(scene);
        secondaryStage.show();
    }
```

Affichons le en haut à droite en ajoutant ce code (que je détail dans la vidéo):

```java
//on aligne en haut à droite
        Rectangle2D visualBounds = Screen.getPrimary().getVisualBounds();
        secondaryStage.setX(visualBounds.getMaxX() - 25 - scene.getWidth());
        secondaryStage.setY(visualBounds.getMinY() + 25);
```

Comme vous l'avez peut être remarqué, la fenêtre n'est plus draggable. On corrige ça:
Le drag s'effectue en 2 étapes. On clique, puis on drag. Il faut donc récupérer la position initiale et ensuite faire un translation

```java
//déclaration position de départ
private double xOffset;
private double yOffset;

...

//ajout de la méthode de translation
 scene.setOnMousePressed(event -> {
            xOffset = secondaryStage.getX() - event.getScreneX();
            yOffset = secondaryStage.getY() - event.getScreneY();
        });
        scene.setOnMouseDragged(event -> {
            secondaryStage.setX(event.getScreneX() + xOffset);
            secondaryStage.setY(event.getScreneY() + yOffset);
        });
```

## Retour au controlleur pour récupérer les données

{{<youtube 26-42PlpTlg>}}

Nous allons utiliser un planificateur (Scheduler).

En haut du controlleur définissons notre planificateur

```java
private ScheduledExecutorService executorService;
```

Dans initialize appelons la méthode d'initialisation

```java
initializeSchedulerService();
```

et définissons cette méthode (et quelques ajustements...)

```java
private void initializeSchedulerService() {
        executorService = Executors.newSingleThreadScheduledExecutor();
        executorService.scheduleAtFixedRate(this::loadData, 0,20, TimeUnit.SECONDS);
    }

private void loadData() {
        DataProviderService dataProviderService = new DataProviderService();
        CovidDataModel covidDataModel = dataProviderService.getData("France");
        // attention au processus de javaFX
        Platform.runLater(()->{
            // déploiement des données dans l'affichage
            inflateData(covidDataModel);
        });

    }

    private void inflateData(CovidDataModel covidDataModel){
        GlobalData globalData = covidDataModel.getGlobalData();
        CountryData countryData = covidDataModel.getCountryData();
        textGlobalReport.setText(getFormatedData(globalData.getCases(),globalData.getRecovered(),globalData.getDeaths()));
        textCountryReport.setText(getFormatedData(countryData.getCases(),countryData.getRecovered(),countryData.getDeaths()));
        // redimentionnement du stage
        readjustStage(textCountryCode.getScene().getWindow());
    }


    // formatage du texte
    private String getFormatedData(long totalCases, long recoveredCases, long totalDeath){
        return String.format("Cas: %-8d | Guéris: %-7d | Morts: %-6d",totalCases,recoveredCases,totalDeath);
    }

    // redimentionnement du stage
    private void readjustStage(Window stage){
        stage.sizeToScene();
        Rectangle2D visualBounds = Screen.getPrimary().getVisualBounds();
        stage.setX(visualBounds.getMaxX() - 25 - textCountryCode.getScene().getWidth());
        stage.setY(visualBounds.getMinY() + 25);
    }
```

> plus de détails dans la vidéo

## Un fichier de configuration.

{{<youtube 0KQx-x7Z9sU>}}


Jusqu'ici tout est codé en dure dans notre programme. L'idée est de permettre de changer le pays et l'intervale de temps pour le rafraississement.

Tout va se faire dans un fichier Json. Nousavons donc besoin d'un POJO et d'un service que nous allons mettre dans un package `config`

Classe `ConfigModel.java`

```java
public class ConfigModel {
    private int refreshIntervalInSeconds;
    private String countryName;
    private String countryCode;

    public ConfigModel() {
        refreshIntervalInSeconds = 30;
        countryName = "France";
        countryCode = "FR";
    }

    //... plus les Getter et Setter
}
```

Classe `ConfigurationService`

```java
public class ConfigrationService {
    private final File SETTINGS_FILE = new File("settings.json");
    private Gson gson = new GsonBuilder().create();

    public ConfigModel getConfiguration() throws IOException {
        if(!SETTINGS_FILE.exists()){
            createSettingsFile();
        }
        return getConfigurationFromfile();
    }

    private void createSettingsFile() throws IOException {
        ConfigModel configModel = new ConfigModel();
        try ( Writer writer = new FileWriter(SETTINGS_FILE, false)){
            gson.toJson(configModel,writer);
        }
    }

    private ConfigModel getConfigurationFromfile() throws IOException {
        ConfigModel configModel = new ConfigModel();
        try ( Reader reader = new FileReader(SETTINGS_FILE)){
           return gson.fromJson(reader,ConfigModel.class);
        }
    }
}
```

En résumé, ce service crée le fichier de configuration s'il n'existe pas puis le lit et le transforme en objet.

Enfin on va modifier le controlleur pour prendre en compte ces changements:

```java
...
 @Override
    public void initialize(URL location, ResourceBundle resources) {
        try {
            configModel = new ConfigrationService().getConfiguration();
        } catch (IOException e) {
            e.printStackTrace();
        }
        initializeSchedulerService();
        textCountryCode.setText(configModel.getCountryCode());
    }

    private void initializeSchedulerService() {

        executorService = Executors.newSingleThreadScheduledExecutor();
        executorService.scheduleAtFixedRate(this::loadData, 0, configModel.getRefreshIntervalInSeconds(), TimeUnit.SECONDS);
    }

    private void loadData() {
        DataProviderService dataProviderService = new DataProviderService();
        CovidDataModel covidDataModel = dataProviderService.getData(configModel.getCountryName());
        // attention au processus de javaFX
        Platform.runLater(() -> {
            inflateData(covidDataModel);
        });

    }
...
```
Et voilà ...

## Derniers petits ajouts

{{<youtube 4w6NVuJWiVM>}}


On va ajouter un menu pour quitter et rafraichir manuellement

Ajoutez la méthode suivante au controlleur

```java
private void initializeContextMenu(){
        MenuItem exitItem = new MenuItem("Quitter");
        exitItem.setOnAction(event -> {System.exit(0);} );

        final ContextMenu contextMenu = new ContextMenu(exitItem);
    }
```

et on le fait apparaitre au clique droit sur le widget

```java
private void initializeContextMenu() {
        MenuItem exitItem = new MenuItem("Quitter");
        exitItem.setOnAction(event -> {
            System.exit(0);
        });
        MenuItem refreshItem = new MenuItem("Rafraichir");
        refreshItem.setOnAction(event -> {
            executorService.schedule(this::loadData,0,TimeUnit.SECONDS);
        });
        final ContextMenu contextMenu = new ContextMenu(exitItem, refreshItem);
        rootPane.addEventHandler(MouseEvent.MOUSE_PRESSED, event -> {
            if (event.isSecondaryButtonDown()) {
                contextMenu.show(rootPane, event.getScreenX(), event.getScreenY());
            } else {
                if (contextMenu.isShowing()) {
                    contextMenu.hide();
                }
            }
        });
    }
```

On donne un peu de style à tout ça en ajoutant ces 2 classe au `main-style.css`

```css
.context-menu {
    -fx-text-fill: white;
    -fx-font-size: 12pt;
    -fx-background-color: derive(-fx-base, 40%);
}

.menu-item:focused {
    -fx-background-color: mediumpurple;
}
```

## Dernière étape, distribution

Afin que notre application puisse fonctionner avec la jvm il va falloir tricher un peu.
Déplacez le `Launch.java` dans le gui et créez une classe `App.java`

{{<youtube HKFKaf6kVC8>}}

```java
package org.gerblog;

import org.gerblog.gui.Launch;

public class App {
    public static void main(String[] args) {
        Launch.main(args);
    }
}
```

La jvm va croire que l'on a à faire à une appli java et chargera les modules javafx ensuite.

Ensuite, tout est dans la vidéo.

> Pour trouver Bat to Exe converter
> https://github.com/99fk/Bat-To-Exe-Converter-Downloader
> ou
> https://www.majorgeeks.com/files/details/bat_to_exe_converter.html
> sur mac utilisez platypus
>Pour créer l'installeur: [innosetup](https://jrsoftware.org/isinfo.php)