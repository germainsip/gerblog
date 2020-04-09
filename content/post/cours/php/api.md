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

USE api_db
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

Vous pouvez aussi télécharger le script de création [ici](download/ma_base.sql).

## Connectons nous à la base donnée

Dans le dossier `api` que vous avez créé dans le dossier de votre serveur. Créons un dossier `config`.
Puis, créons le fichier `database.php` qui va nous permettre de nous connecter.

**database.php**


```php

```
