---
title: Une simple Api en PHP
date: 2020-04-09T09:29:38.000Z
draft: false
description: Création d'API en php
categories:
  - API
tags:
  - php
  - API
thumbnail: img/api_php.png
lead: Créons notre API en PHP (pas terminé pour le moment)
comments: false
authorbox: true
toc: true
---

{{<youtube bdx9MwiD76M>}}

---

:hand: en cours de rédaction :hand:

---

## Ça sert à quoi une API?

Tout d'abord API c'est "Application Programming Interface". En d'autre termes c'est une interface qui permet le transfert de données d'une application à une (des) autre(s).

C'est une technique importante puisque nous avons besoin de faire interagir nos sites web avec nos applications desktop et nos application mobiles.

Par exemple, on peut intégrer les données d'une entreprise dans notre base de donnée grace à l'API de l'INSEE "sirene". Je pense vous écrire un support pour faire ça avec Java.

## Qu'allons nous faire ici?

Nous allons construire une API (interface sans GUI) pour faire du CRUD à partir d'une autre application.

## Les prérequis et environnement de développement

- Je vais utiliser un simple éditeur de texte (vscode) avec coloration syntaxique php et un plugin pour écrire en php (intelliphense par exemple)
- Un serveur apache avec php sur votre machine (WAMP, MAMP, XAMP, LAMP, Laragon, apache2... au choix).
- Une base de donnée Maria ou Mysql.
- Un logiciel pour vérifier que notre API fonctionne (postman ou autre)

## C'est partie

On va commencer par créer une base une base de donnée.

```sql
CREATE DATABASE api_db;

USE api_db;
```

Créer la table `categories`

```sql
CREATE TABLE IF NOT EXISTS `categories` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(256) NOT NULL,
  `description` text NOT NULL,
  `created` datetime NOT NULL,
  `modified` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB  DEFAULT CHARSET=utf8 AUTO_INCREMENT=19 ;
```

L'alimenter

```sql
INSERT INTO `categories` (`id`, `name`, `description`, `created`, `modified`) VALUES
(1, 'Mode', 'Catégorie pour tout ce qui touche à la mode.', '2019-06-01 00:35:07', '2019-05-30 17:34:33'),
(2, 'Electronics', 'Gadgets, drones et plus.', '2019-06-01 00:35:07', '2019-05-30 17:34:33'),
(3, 'Motors', 'Sports motorisés et plus', '2019-06-01 00:35:07', '2019-05-30 17:34:54'),
(5, 'Movies', 'Produits cinématographiques.', '2019-01-08 13:27:26', '2019-01-08 13:27:26'),
(6, 'Books', 'Livres Kindle, livres audio et plus encore.', '2019-01-08 13:27:47', '2019-01-08 13:27:47'),
(13, 'Sports', "Si si le code c'est du sport", '2019-01-09 02:24:24', '2019-01-09 01:24:24');
```

Créer la table `products`

```sql
CREATE TABLE IF NOT EXISTS `products` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(32) NOT NULL,
  `description` text NOT NULL,
  `price` decimal(10,0) NOT NULL,
  `category_id` int(11) NOT NULL,
  `created` datetime NOT NULL,
  `modified` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB  DEFAULT CHARSET=latin1 AUTO_INCREMENT=65 ;
```

Et l'alimenter

```sql
INSERT INTO `products` (`id`, `name`, `description`, `price`, `category_id`, `created`, `modified`) VALUES
(1, 'LG P880 4X HD', 'My first awesome phone!', '336', 3, '2014-06-01 01:12:26', '2014-05-31 17:12:26'),
(2, 'Google Nexus 4', 'The most awesome phone of 2013!', '299', 2, '2014-06-01 01:12:26', '2014-05-31 17:12:26'),
(3, 'Samsung Galaxy S4', 'How about no?', '600', 3, '2014-06-01 01:12:26', '2014-05-31 17:12:26'),
(6, 'Bench Shirt', 'The best shirt!', '29', 1, '2014-06-01 01:12:26', '2014-05-31 02:12:21'),
(7, 'Lenovo Laptop', 'My business partner.', '399', 2, '2014-06-01 01:13:45', '2014-05-31 02:13:39'),
(8, 'Samsung Galaxy Tab 10.1', 'Good tablet.', '259', 2, '2014-06-01 01:14:13', '2014-05-31 02:14:08'),
(9, 'Spalding Watch', 'My sports watch.', '199', 1, '2014-06-01 01:18:36', '2014-05-31 02:18:31'),
(10, 'Sony Smart Watch', 'The coolest smart watch!', '300', 2, '2014-06-06 17:10:01', '2014-06-05 18:09:51'),
(11, 'Huawei Y300', 'For testing purposes.', '100', 2, '2014-06-06 17:11:04', '2014-06-05 18:10:54'),
(12, 'Abercrombie Lake Arnold Shirt', 'Perfect as gift!', '60', 1, '2014-06-06 17:12:21', '2014-06-05 18:12:11'),
(13, 'Abercrombie Allen Brook Shirt', 'Cool red shirt!', '70', 1, '2014-06-06 17:12:59', '2014-06-05 18:12:49'),
(26, 'Another product', 'Awesome product!', '555', 2, '2014-11-22 19:07:34', '2014-11-21 20:07:34'),
(28, 'Wallet', 'You can absolutely use this one!', '799', 6, '2014-12-04 21:12:03', '2014-12-03 22:12:03'),
(31, 'Amanda Waller Shirt', 'New awesome shirt!', '333', 1, '2014-12-13 00:52:54', '2014-12-12 01:52:54'),
(42, 'Nike Shoes for Men', 'Nike Shoes', '12999', 3, '2015-12-12 06:47:08', '2015-12-12 05:47:08'),
(48, 'Bristol Shoes', 'Awesome shoes.', '999', 5, '2016-01-08 06:36:37', '2016-01-08 05:36:37'),
(60, 'Rolex Watch', 'Luxury watch.', '25000', 1, '2016-01-11 15:46:02', '2016-01-11 14:46:02');
```

Vous pouvez aussi télécharger le script de création [ici](/download/ma_base.sql).

## Connectons nous à la base donnée (Partie 2)

{{<youtube t34fVoy4Bbs>}}

Notre projet aura cette forme à la fin de cette partie:

``` cmd
api
├── config
│   └── database.php
├── objects
│   └── product.php
└── product
    └── read.php

```

Dans le dossier `api` que vous avez créé dans le dossier de votre serveur. Créons un dossier `config`.
Puis, créons le fichier `database.php` qui va nous permettre de nous connecter.

**database.php**

``` PHP
<?php
class Database {
   // On spécifie les coordonnées de connexion
   private $host = "localhost";
   private $db_name = "api_db";
   private $dsn ;
   private $username = "root";
   private $password = "VOTRE_MOT_DE_PASSE";
   public $conn;


   // connexion à la base
   public function getConnection() {

       $this->conn = null;
       $this->dsn = "mysql:host=". $this->host. ";dbname=" . $this->db_name;
       try{
           $this->conn = new PDO($this->dsn, $this->username, $this->password);
       $this->conn ->exec("set names utf8");
       } catch (PDOException $exception) {
           echo "Conneciton error: " . $exception->getMessage();
       }
       return $this->conn;
   }
}
```

## Accédons à nos produits

On va créer une classe pour nos produits:

- Dans le dossier `api` créez un dossier `objects` et dans ce dossier créez la classe `product.php`

```php
<?php
class Product {

    // connexion à la base
    private $conn;
    private $table_name = "products";

    // membres de l'objet
    public $id;
    public $name;
    public $description;
    public $price;
    public $categorie_id;
    public $category_name;
    public $created;

    // un constructeur avec $db comme connexion à la base
    public function __construct($db) {
        $this->conn = $db;
    }
}
?>
```

Il nous faut aussi un fichier pour lire nos objets:

Ajoutez dans le dossier `product` un fichier `read.php`

D'abord il nous faut les headers:

```php
<?php
// Les headers dont nous avons besoin
header("Access-Control-Allow-Origin: *");
header("Content-Type: application/json; charset=UTF-8");
```

On prépare la connection en incluant nos import et en instanciant nos objets:

```php
<?php

// ... le code qui précède dans read.php

// inclusion de database et product
include_once '../config/database.php';
include_once '../objects/product.php';

// instanciation de database et de product
$database = new Database();
$db = $database->getConnection();

// On initialise product
$product = new Product($db);
```

On va lire les résultats de la méthode `read()`de `Product`

```php
<?php
...
// le query
$stmt = $product->read();
$num = $stmt->rowCount();

// on regarde si on a plus d'un résultat
if($num>0){

    // tableau de produits
    $products_arr = array();
    $products_arr["records"]=array();

    // on récupère le contenu de la table
    while ($row = $stmt->fetch(PDO::FETCH_ASSOC)){
        extract($row);

        $product_item = array(
            "id" => $id,
            "name" => $name,
            "description" => $price,
            "category_id" => $category_id,
            "category_name" => $category_name
        );

        array_push($products_arr["records"], $product_item);
    }

    // on envoie la réponse http à 200 OK
    http_response_code(200);

    // on renvoie la réponse en Json
    echo json_encode($products_arr);

}
```

Il nous faut maintenant ajouter la méthode `read()` à `Product.php`

```php
<?php

...

// lecture des produits
public function read() {
  // requête select avec jointure
  $query = "SELECT
            c.name as category_name, p.id, p.name, p.description, p.price, p.category_id, p.created
            FROM "
           . $this->table_name . " p
            LEFT JOIN
            categories c ON p.category_id = c.id
            ORDER BY
            p.created DESC";

  // on prépare la requête
  $stmt = $this->conn->prepare($query);

  // on execute la requête
  $stmt->execute();

  return $stmt;
}
```

Et on va avertir l'utilisateur si aucun produit n'a été trouvé.

A la suite de notre `if` dans `read.php`

```php
<?php
//fin du if
else {
// on renvoie le code 404 Not found
    http_response_code(404);

// On averti l'utilisateur
    echo json_encode(array("message"=> "Aucun produits trouvés."));
}

```

Il ne vous reste qu'à tester tout ça avec Postman ou Insomnia.

Dans le client entre l'adresse `localhost/api/product/read.php` et validez par **send**

## Créons des produits (Partie 3)

C'est le **C** de CRUD.

L'accès va se faire via le fichier `create.php` dans le dossier `product`

```php
<?php
header("Access-Control-Allow-Origin: *");
header("Content-Type: application/json; charset=UTF-8");
header("Access-Control-Allow-Methods:POST");
header("Access-Control-Max-Age: 3600");
header("Access-Control-Allow-Headers: Content-Type,Access-Control-Allow-Headers,Authorization,X-Requested-Width");


// on se connecte à la base
include_once '../config/database.php';

// l'objet product
include_once '../objects/product.php';

$database = new Database();
$db = $database->getConnection();

$product = new Product($db);

// on récupère les données du post
$data = json_decode(file_get_contents("php://input"));

// on s'assure que ce n'est pas vide
if (
        !empty($data->name) &&
        !empty($data->price) &&
        !empty($data->description) &&
        !empty($data->category_id)
) {
    $product->name = $data->name;
    $product->price = $data->price;
    $product->description = $data->description;
    $product->category_id = $data->category_id;
    $product->created = date('Y-m-d H:i:s');

    if ($product->create()) {

        //on envoie le code 201
        http_response_code(201);

        // on averti l'utilisateur
        echo json_encode(array("message" => "Produit Créé."));
    } else {
        // on envoie le code 503
        http_response_code(503);
        // on averti l'utilisateur
        echo json_encode(["message" => "Impossible de créer le produit!"]);
    }
} else {
    // on envoie le code 400 - bad request
    http_response_code(400);
    // on averti l'utilisateur
    echo json_encode(["message" => "Impossible de créer le produit! les données sont incomplètes!"]);
}
```

On va aussi ajouter la méthode `create()` à `product.php


```php
<?php

...

public function create() {
        // requête d'insertion
        $query = "INSERT INTO
                " . $this->table_name . "
            SET
                name=:name, price=:price, description=:description, category_id=:category_id, created=:created";
        // on prépare la requête
        $stmt = $this->conn->prepare($query);

        //nettoyage
        $this->name = htmlspecialchars(strip_tags($this->name));
        $this->price = htmlspecialchars(strip_tags($this->price));
        $this->description = htmlspecialchars(strip_tags($this->description));
        $this->category_id = htmlspecialchars(strip_tags($this->category_id));
        $this->created = htmlspecialchars(strip_tags($this->created));

        // on bind
        $stmt->bindParam(":name", $this->name);
        $stmt->bindParam(":price", $this->price);
        $stmt->bindParam(":description", $this->description);
        $stmt->bindParam(":category_id", $this->category_id);
        $stmt->bindParam(":created", $this->created);

        // on lance la requête
        if ($stmt->execute()) {
            return true;
        }
        return false;
    }
```

Verifiez que tout fonctionne avec postman ou insomnia à l'adresse `localhost/api/product/create.php` en ajoutant au body le Json:

```json
{
    "name" : "Mega oreiller 2.0",
    "price" : "199",
    "description" : "Le meilleur oreiller pour des programmeurs incroyables.",
    "category_id" : 2,
    "created" : "2018-06-01 00:35:07"
}
```

Et voilà !!!

![insomnia](/img/insomnia.png)

## Un seul objet (Partie 4)

Afin de lire un seul enregistrement, on va se donner un accé.
Créons le fichier `read_one.php` dans le dossier `product`.

```php
<?php



// les headers
header("Access-Control-Allow-Origin: *");
header("Access-Control-Allow-Headers: access");
header("Access-Control-Allow-Methods: GET");
header("Access-Control-Allow-Credentials: true");
header('Content-Type: application/json');

// les inclusions
include_once '../config/database.php';
include_once '../objects/product.php';

// on se connecte
$database = new Database();
$db = $database->getConnection();

$product = new Product($db);

// Récupérons l'id de l'objet à lire
$product->id = isset($_GET['id']) ? $_GET['id'] : die();

// on lit les détails du produit
$product->readOne();
if ($product->name != null) {
    // création d'un tableau
    $product_arr[] = ["id" => $product->id,
        "name" => $product->name,
        "description" => $product->description,
        "price" => $product->price,
        "category" => $product->category_id,
        "category_name" => $product->category_name];
    // requête ok 200
    http_response_code(200);

    //On traduit ça en Json
    echo json_encode($product_arr);
} else{
    http_response_code(404);
    echo json_encode(["message"=>"Ce produit n'éxiste pas!"]);

}
```

En résumé, on récupère l'id de l'objet dans l'URL puis on lance la requête avec la méthode `readOne()`. Si nous avons trouvé un produit, on met le infos dans un tableau et on renvoie en json.

Si pas d'éléments trouvés, on avertis l'utilisateur.

Il nous manque la méthode `readOne()` dans `product.php`

```php
<?php
...
public function readOne() {
  // requête pour lire 1 enregistrement
  $query = "SELECT
      c.name as category_name, p.id, p.name, p.description, p.price, p.category_id, p.created
      FROM
         " . $this->table_name . " p
      LEFT JOIN
         categories c
      ON p.category_id = c.id
      WHERE
         p.id = ?
      LIMIT
         0,1";
  // on prépare la requête
  $stmt = $this->conn->prepare($query);
  // on met l'id à sa place
  $stmt->bindParam(1, $this->id);
  // on execute
  $stmt->execute();
  // on récupère le résultat
  $row = $stmt->fetch(PDO::FETCH_ASSOC);

  // on renvoie ça dans l'objet
  $this->name = $row['name'];
  $this->price = $row['price'];
  $this->description = $row['description'];
  $this->category_id = $row['category_id'];
  $this->category_name = $row['category_name'];
    }
```

Voilà...On définie la requête préparée de recherche. On injecte l'id recherchée. On lance et on récupère les données dans l'objet.

Testez avec l'URL `http://localhost/api/product/read_one.php?id=60` par exemple dans **insomnia** ou **postman**.

<!-- ## L'UPDATE (Partie 5)

...

## Le DELETE (Partie 5)

...

## Le SEARCH (Partie 6)

...

## Une pagination (Partie 7)

...

## Lecture des catégories (Partie 8)

... -->

Ce cours n'est pas terminé. Revennez régulièrement pour la suite du CRUD.
