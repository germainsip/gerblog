---
title: "JavaFX et sauvegarde"
date: 2020-03-16T16:05:18+01:00
draft: false
---

:exclamation: :exclamation: :exclamation: **en cours de rédaction** :exclamation: :exclamation: :exclamation:

## Sauvegardons des données en Json

Plusieurs bibliothèques existent pour exploiter le Json avec Java. Je vous propose d'en essayer 2.

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
│   │       └── ExempleController.java
│   └── module-info.java
└── resources
    └── org
        └── afpa
            └── exemple.fxml
```

Pour le **fxml**