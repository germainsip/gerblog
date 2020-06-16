---
title: Retrofit
date: 2020-06-07T13:38:13.000Z
draft: false
---

## La librairie Retrofit2

Retrofit est une librairie qui simplifie la consommation d'api

On peut la trouver sur [github](https://square.github.io/retrofit/)

Nous allons l'ajouter a un projet gradle en ajoutant simplement `compile 'com.squareup.retrofit2:retrofit:2.8.1'` à nos dépendances ainsi qu'un convertisseur `compile 'com.squareup.retrofit2:converter-gson:2.8.1'` qui transformera nos objets `json` en objets Java.

Pour notre exemple, je vais utiliser une api toute faite [jsonplaceholder](https://jsonplaceholder.typicode.com)

Ce site permet d'intérroger directement une api avec l'url. Par exemple si vous allez à l'adresse `https://jsonplaceholder.typicode.com/todos/1` vous obtiendrez le todo 1 en json

```json
{
  "userId": 1,
  "id": 1,
  "title": "delectus aut autem",
  "completed": false
}
```

## Quelques réglages dans notre application

> pour rappel: votre `build.gradle` doit ressembler à ça

```gradle
plugins {
    id 'java'
    id 'application'
    id 'org.openjfx.javafxplugin' version '0.0.8'
}

group 'org.afpa'
version '1.0-SNAPSHOT'
compileJava.options.encoding = 'UTF-8'
sourceSets.main.resources.srcDir("src/main/java").includes.addAll(["**/*.fxml", "**/*.css","**/*.png"])
sourceSets.main.resources.srcDirs("src/main/resources").includes.addAll(["**/*.*"])

repositories {
    mavenCentral()
}

dependencies {
    testCompile group: 'junit', name: 'junit', version: '4.12'

    compile 'com.squareup.retrofit2:retrofit:2.8.1'
    compile 'com.squareup.retrofit2:converter-gson:2.8.1'
}

javafx {
    version = "14"
    modules = ['javafx.controls','javafx.base','javafx.graphics','javafx.fxml']
}

mainClassName = "org.afpa.App"

```

Nous allons d'abord créer un **POJO** (objet java) pour réceptionner nos données. J'ai d'abord choisi les posts.

L'objet que je récupère en json ressemble à ceci:

```json
{
    "userId": 1,
    "id": 1,
    "title": "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
    "body": "quia et suscipit\nsuscipit recusandae consequuntur expedita et cum\nreprehenderit molestiae ut ut quas totam\nnostrum rerum est autem sunt rem eveniet architecto"
  }
```

Donc mon objet java est:

```java

package org.afpa.DAL;

import com.google.gson.annotations.SerializedName;

public class Post {

    private Long id;
    private Long userId;
    private String title;
    @SerializedName("body")
    private String text;

    public Long getId() {
        return id;
    }

    public Long getUserId() {
        return userId;
    }

    public String getTitle() {
        return title;
    }

    public String getText() {
        return text;
    }
}
```

J'ai placé cet objet dans un package DAL et j'ai renommé le membre `body` en `text` grace à l'annotation `@SerializedName("body")`.

Retrofit a besoin que l'on lui crée une interface où nous mettons notre "query"


```java
package org.afpa.DAL;

import retrofit2.Call;
import retrofit2.http.GET;

import java.util.List;

public interface ApiService {
    @GET("posts")
    Call<List<Post>> getPosts();
}
```

Dans cette interface nous avons déclaré la méthode `getPosts()` qui va récupérer une liste de `Post` à partir de la liste json envoyée par l'API.

## Mise en place dans notre programme

Vous avez déjà fais du JavaFX, donc je vais aller un peu plus vite.

Je crée un fxml et son contrôleur dans un package `GUI`:

**retroexemple.fxml**

```xml
<?xml version="1.0" encoding="UTF-8"?>

<?import javafx.geometry.*?>
<?import javafx.scene.control.*?>
<?import javafx.scene.layout.*?>
<?import javafx.scene.text.*?>

<AnchorPane prefHeight="400.0" prefWidth="600.0" xmlns="http://javafx.com/javafx/10.0.2-internal" xmlns:fx="http://javafx.com/fxml/1" fx:controller="org.afpa.GUI.RetroexempleController">
   <children>
      <VBox prefHeight="400.0" prefWidth="600.0" spacing="10.0" AnchorPane.bottomAnchor="0.0" AnchorPane.leftAnchor="0.0" AnchorPane.rightAnchor="0.0" AnchorPane.topAnchor="0.0">
         <children>
            <Label text="Retrofit2 et JavaFX">
               <font>
                  <Font size="36.0" />
               </font>
            </Label>
            <TableView fx:id="receptionTab" prefHeight="357.0" prefWidth="600.0">
              <columns>
                <TableColumn fx:id="idCol" prefWidth="75.0" text="Id" />
                <TableColumn fx:id="userIdCol" prefWidth="75.0" text="UserID" />
                  <TableColumn fx:id="titreCol" prefWidth="75.0" text="Titre" />
                  <TableColumn fx:id="texteCol" prefWidth="75.0" text="Texte" />
              </columns>
               <columnResizePolicy>
                  <TableView fx:constant="CONSTRAINED_RESIZE_POLICY" />
               </columnResizePolicy>
            </TableView>
            <Label fx:id="messageErreur" layoutX="10.0" layoutY="10.0" textFill="#c91212">
               <font>
                  <Font size="18.0" />
               </font>
            </Label>
         </children>
      </VBox>
   </children>
   <padding>
      <Insets bottom="10.0" left="10.0" right="10.0" top="10.0" />
   </padding>
</AnchorPane>


```

**RetroExempleController.java**


```java
package org.afpa.GUI;

import javafx.collections.FXCollections;
import javafx.collections.ObservableList;
import javafx.fxml.Initializable;
import javafx.scene.control.TableColumn;
import javafx.scene.control.TableView;
import javafx.scene.control.cell.PropertyValueFactory;
import org.afpa.DAL.ApiService;
import org.afpa.DAL.Post;
import retrofit2.Call;
import retrofit2.Callback;
import retrofit2.Response;
import retrofit2.Retrofit;
import retrofit2.converter.gson.GsonConverterFactory;

import java.net.URL;
import java.util.List;
import java.util.ResourceBundle;

public class RetroexempleController implements Initializable {
    public TableView<Post> receptionTab;
    public TableColumn idCol;
    public TableColumn userIdCol;
    public TableColumn titreCol;
    public TableColumn texteCol;
    public Label messageErreur;

    ObservableList<Post> observableList = FXCollections.observableArrayList();

    @Override
    public void initialize(URL location, ResourceBundle resources) {

        chargeTableau();
    }


}

```

Pour le moment nous n'avons pas créé la méthode de chargement.
Que nous faut-il?

Il faut d'abord créer une instance de `Retrofit`


```java
Retrofit retrofit = new Retrofit.Builder()
                .baseUrl("https://jsonplaceholder.typicode.com/")
                .addConverterFactory(GsonConverterFactory.create())
                .build();
```

Dans celle-ci nous indiquons l'adresse de l'api, le convertisseur qui va convertir les objets json en objet java et on oublie pas le `.build()`.

On définie ensuite le `Call` généré par l'interface que nous avons écrite.

```java
Call<List<Post>> call = apiService.getPosts();
```

Enfin il nous faut utiliser le `.enqueue()` de **Retrofit**

```java
call.enqueue(new Callback<List<Post>>() {
            @Override
            public void onResponse(Call<List<Post>> call, Response<List<Post>> response) {

            }

            @Override
            public void onFailure(Call<List<Post>> call, Throwable t) {

            }
        });
```

Le `enqueue` prend en paramètre un `Callback` et implémente deux méthodes. La première est `onResponse` si tout se passe comme prévu. C'est ici que nous remplissons le tableau à partir du json contenu dans `response.body()` et en créant une `ObservableList<Post> observableList`.
La seconde est `onFailure()`, si ça se passe mal et on peut par exemple envoyer une alerte à l'utilisateur.

Finalement `chargeTableau()` ressemble à ça:

```java
private void chargeTableau() {
        Retrofit retrofit = new Retrofit.Builder()
                .baseUrl("https://jsonplaceholder.typicode.com/")
                .addConverterFactory(GsonConverterFactory.create())
                .build();

        ApiService apiService = retrofit.create(ApiService.class);
        Call<List<Post>> call = apiService.getPosts();

        call.enqueue(new Callback<List<Post>>() {
            @Override
            public void onResponse(Call<List<Post>> call, Response<List<Post>> response) {
                observableList = FXCollections.observableArrayList(response.body());
                idCol.setCellValueFactory(new PropertyValueFactory<>("id"));
                userIdCol.setCellValueFactory(new PropertyValueFactory<>("userId"));
                titreCol.setCellValueFactory(new PropertyValueFactory<>("title"));
                texteCol.setCellValueFactory(new PropertyValueFactory<>("text"));
                receptionTab.setItems(observableList);
            }

            @Override
            public void onFailure(Call<List<Post>> call, Throwable t) {
               Platform.runLater(() -> {
                    Alert alert = new Alert(AlertType.WARNING);
                    alert.setContentText("Erreur de connection!!!");
                    alert.showAndWait();
                });
            }
        });
    }
```

**Attention**, vous remarquerez que la gestion de l'alerte est encadrée par un `Platform.runLater()`. C'est parce que Java n'aime pas mélanger les processus et que nous devons lui indiquer que l'alerte est dans javaFX.

![ok](/img/retrofitOK.png)

et

![down](/img/retrofitDown.png)
