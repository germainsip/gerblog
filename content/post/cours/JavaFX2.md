---
title: JavaFX et sauvegarde Partie 2
date: 2020-03-19T10:32:50.000Z
draft: false
description: 2eme Didacticiel sur la persistance
categories:
  - JavaFX
  - Persistance
tags:
  - JavaFX
  - Json
  - Jackson
  - JSON.simple
  - flux de fichier
thumbnail: img/jfxjsonhead.png
lead: 2eme Didacticiel sur la persistance
comments: false
authorbox: true
toc: true
mathjax: true
---


:movie_camera: **La vidéo arrive! Juste le temps de la faire** :movie_camera:

> maj: 21/03/2020

**Prérequis:** avoir suivi la première partie [JavaFX et sauvegarde Partie 1](https://germainsip.github.io/post/cours/javafx/)

Comme promis, nous allons d'abord utiliser JSON.simple. Le but ici est uniquement de vous montrer comment sauvegarder quelques information sur notre application, dans la prochaine partie nous les importerons.

## Importons Json.simple

Il faut tout d'abord importer la bibliothèque. Dans le pom fait un ajout de dépendance, soit avec clique droit sur `<dependencies>` et Generate... ou avec le raccourcit clavier puis Depedency.
Vous choisirez json.simple.

Dans le `pom.xml` ça nous ajoute

```xml
<dependency>
    <groupId>com.googlecode.json-simple</groupId>
    <artifactId>json-simple</artifactId>
    <version>1.1.1</version>
</dependency>
```

## Mise en place

Dans notre contrôleur nous avons déjà une méthode `sauvHandler()` que nous allons utiliser pour la sauvegarde.
Je vais sauvegarder bêtement les noms des champs et le label de titre.

Commencez par nommer les label:

- le label nom: `champNom`
- le label prenom: `champPrenom`
- le label titre: `champTitre`
- le label âge: `champAge`

et déclarez les dans le **contrôleur**

![champs](/img/JavaFX_Json/jfxjson3.png)

## Utilisation de JSON.simple

Dans la méthode, créons notre objet json:

```java
 @FXML
    private void sauvHandler(ActionEvent event) {
        JSONObject sauvegarde = new JSONObject();
        sauvegarde.put("titre", champTitre.getText());
        sauvegarde.put("champNom",champNom.getText());
        sauvegarde.put("champPrenom",champPrenom.getText());
        sauvegarde.put("champAge",champAge.getText());
    }
```

## Modification de App pour afficher la fenêtre de sauvegarde

Afin d'afficher un **popup de sauvegarde** nous devons rendre accessible le stage à partir du contrôleur.

Faites la modification suivante dans `App.java`:

```java
 public static void main(String[] args) {
        launch(args);
    }
    // Déclaration du stage
    private static Stage stage;
    // getter qui rend accessible notre stage
    public static Stage getStage() {
        return stage;
    }

    @Override
    public void start(Stage primaryStage) {
        // Récupération du stage
        App.stage = primaryStage;
```

## Écriture du Json dans un fichier

Nous allons maintenant laisser l'utilisateur dire où il va faire sa sauvegarde.
Utilisons `FileChooser` et sa fenêtre popup à la suite dans notre méthode `sauvHandler`

```java
// Déclaration du FileChoser
FileChooser fileChooser = new FileChooser();
// Ajout d'un filtre d'extention
FileChooser.ExtensionFilter extensionFilter = new FileChooser.ExtensionFilter("Json file (*.json", "*.json");
fileChooser.getExtensionFilters().add(extensionFilter);

Affichage du popup
File file = fileChooser.showSaveDialog(App.getStage());
```

Si le fichier est bien choisi ou nommé par l'utilisateur nous l'écrivons grace à un `FileWriter`.

```java
if (file != null) {
    //Si l'extension n'est pas bien renseignée on l'ajoute
    if (!file.getPath().endsWith(".json")) file = new File(file.getPath() + ".json");
    //On écrit le fichier avec un try..catch en cas d'erreur
    try (FileWriter fichier = new FileWriter(file)) {
        fichier.write(sauvegarde.toJSONString());
        fichier.flush();
        }catch (IOException e){
          //On affiche l'erreur en console
          System.out.println(e.getMessage());
        }
}
```

## Testons tout ça

Lancez note appli et cliquez sur sauver puis choisissez un emplacement de sauvegarde et validez.

Vous devriez obtenir un fichier dont le contenu est:

```json
{"champPrenom":"Prénom","titre":"Mon appli d'Exemple","champAge":"âge","champNom":"Nom"}
```

Voilà nous savons sauver des données.

Dans la prochaine partie nous verrons comment les charger.
