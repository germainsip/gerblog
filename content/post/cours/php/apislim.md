---
title: "Apislim"
date: 2020-06-23T19:01:37+02:00
draft: true
description: Création d'API avec Slim
categories:
  - API
  - Frameworks
tags:
  - php
  - API
  - micro framework
thumbnail: img/slimapi.png
lead: Création d'API avec Slim
comments: false
authorbox: true
toc: true
---

## Slim framework pour une API REST

**Slim** est un micro framework. Vous pouvez trouver son site [ici](http://www.slimframework.com/). Un **micro framework** est un framework allégé qui n'embarquera que le nécessaire à votre application contrairement à symphonie ou Laravel qui sont vite très volumineux.

L'avantage d'utiliser un framework pour construire une API REST est que nous avons à disposition plusieurs mécanisme déjà programmés pour nous comme par exemple le système de routage dont nous reparlerons par la suite.

## Préparation du projet

> **prérequis**: veuillez à avoir installé php et [composer](https://getcomposer.org/) sur votre machine.

Créez un dossier dans lequel vous allez travailler. Donnez lui le nom de votre projet (exemple: tutoslim).

Dans le dossier, nous allons déjà installer **Slim**

```zsh
cd tutoslim
composer require slim/slim:"4.*"
```

On va ajouter l'implémentation du [PSR-7](https://www.php-fig.org/psr/psr-7/) pour respecter les règles du php concernant le http au projet:

```zsh
composer require slim/psr7
```

Nous allons aussi utiliser l'injection de dépendances et l'"autowiring" du [PSR-11](https://www.php-fig.org/psr/psr-11/)

```zsh
composer require php-di/php-di
```

Vous pouvez aussi ajouter phpunit en tant que dépendance

```zsh
composer require phpunit/phpunit --dev
```

C'est un bon début pour commencer.

> remarque: Si vous utilisez git, veuillez à ajouter le dossier `vendor/` au gitignore

**Structure du projet:**

Le projet va suivre cette arborescence pour être lisible et organisée.

```zsh
.
├── config
├── public
│   ├── .htaccess
│   └── index.php
├── src
├── templates
├── tmp
├── vendor
├── .gitignore
├── .htaccess
├── composer.json
└── composer.lock
```

**Mise en place du** [PSR-4 autoloader](https://www.php-fig.org/psr/psr-4/):

On va définir le dossier src comme racine de App

``` json
"autoload": {
    "psr-4": {
        "App\\": "src/"
    }
},
"autoload-dev": {
    "psr-4": {
        "App\\Test\\": "tests/"
    }
}
```

Notre `composer.json` doit maintenant ressembler à ça

```json
{
    "require": {
        "slim/slim": "4.*",
        "slim/psr7": "^1.1",
        "php-di/php-di": "^6.2"
    },
    "require-dev": {
        "phpunit/phpunit": "^9.2"
    },
    "autoload": {
        "psr-4": {
            "App\\": "src/"
        }
    },
    "autoload-dev": {
        "psr-4": {
            "App\\Test\\": "tests/"
        }
    },
    "config": {
        "process-timeout": 0,
        "sort-packages": true
    }
}
```

Pour effectuer les modifications sur le projet, on lance

```zsh
composer upgrade
```

**Réécriture d'URL sur Apache**:

Pour que **Slim** fonctionne on fait appel à un "front controller". Ici le `index.php` dans `public/`. Nous allons devoir réécrire l'url pour ne pas avoir à taper ce `index.php`.

Créez un dossier `public/` si ce n'est pas déjà fait et créez y le fichier `.htacces` qui sera lu par Apache

```txt
# Redirection vers le front controller
RewriteEngine On
# RewriteBase /
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^ index.php [QSA,L]
```

Créez en un deuxième contenant le code suivant à la racine du projet

```txt
RewriteEngine on
RewriteRule ^$ public/ [L]
RewriteRule (.*) public/$1 [L]
```

Celui-ci permet de démarrer de la racine du domaine et de ne donner accès qu'au dossier `public/`.

Créez maintenant le fichier `index.php` dans le dossier `public`

```php
<?php

(require __DIR__ . '/../config/bootstrap.php')->run();
```

`index.php`est le ponit d'entrée.

## Configuration

Pour la config nous allons aller dans le dossier `config/` et c réer un fichier `settings.php`


```php
<?php

// Error reporting for production
error_reporting(0);
ini_set('display_errors', '0');

// Timezone
date_default_timezone_set('Europe/Berlin');

// Settings
$settings = [];

// Path settings
$settings['root'] = dirname(__DIR__);
$settings['temp'] = $settings['root'] . '/tmp';
$settings['public'] = $settings['root'] . '/public';

// Error Handling Middleware settings
$settings['error'] = [

    // Should be set to false in production
    'display_error_details' => true,

    // Parameter is passed to the default ErrorHandler
    // View in rendered output by enabling the "displayErrorDetails" setting.
    // For the console and unit tests we also disable it
    'log_errors' => true,

    // Display error details in error log
    'log_error_details' => true,
];

return $settings;
```

## Le point de départ

L'application démarre en allant chercher `bootstrap.php` (amorceur). C'est lui qui va charger l'autoload, construire le conteneur, créer l'application et enregistrer les _routes_ et le _middleware_

Créez le fichier `bootstrap.php` dans `config/`

```php
<?php

use DI\ContainerBuilder;
use Slim\App;

require_once __DIR__ . '/../vendor/autoload.php';

$containerBuilder = new ContainerBuilder();

// Set up settings
$containerBuilder->addDefinitions(__DIR__ . '/container.php');

// Build PHP-DI Container instance
$container = $containerBuilder->build();

// Create App instance
$app = $container->get(App::class);

// Register routes
(require __DIR__ . '/routes.php')($app);

// Register middleware
(require __DIR__ . '/middleware.php')($app);

return $app;
```

## Réglage des routes

Les _Routes_ sont les chemins url que nous allons utiliser.

Créons le fichier `config/routes.php`


```php
<?php

use Slim\App;

return function (App $app) {
    // empty
};
```

## Le Middleware

Qu'est-ce qu'un middleware?
Un middleware peut être exécuté avant et après votre application Slim pour manipuler l'objet de requête et de réponse selon vos besoins.

[Plus de détails](https://www.slimframework.com/docs/v4/concepts/middleware.html)

On va utiliser des Middleware déjà écrit dans slim et les inscrire dans le fichier `config/middleware.php`


```php
<?php

use Slim\App;
use Slim\Middleware\ErrorMiddleware;

return function (App $app) {
    // Parse json, form data and xml
    $app->addBodyParsingMiddleware();

    // Add the Slim built-in routing middleware
    $app->addRoutingMiddleware();

    // Catch exceptions and errors
    $app->add(ErrorMiddleware::class);
};
```

<!-- TODO: rédiger l'injection de dépendances et conteneurs -->

## Définition du conteneur

Slim 4 utilise un conteneur d'injection de dépendances pour préparer, gérer et injecter les dépendances des applications.

Vous pouvez ajouter n'importe quelle bibliothèque de conteneurs qui implémente l' interface [PSR-11](https://www.php-fig.org/psr/psr-11/) .

Créez un nouveau fichier pour les entrées du conteneur `config/container.php` et copiez / collez ce contenu:

```php
<?php

use Psr\Container\ContainerInterface;
use Slim\App;
use Slim\Factory\AppFactory;
use Slim\Middleware\ErrorMiddleware;

return [
    'settings' => function () {
        return require __DIR__ . '/settings.php';
    },

    App::class => function (ContainerInterface $container) {
        AppFactory::setContainer($container);

        return AppFactory::create();
    },

    ErrorMiddleware::class => function (ContainerInterface $container) {
        $app = $container->get(App::class);
        $settings = $container->get('settings')['error'];

        return new ErrorMiddleware(
            $app->getCallableResolver(),
            $app->getResponseFactory(),
            (bool)$settings['display_error_details'],
            (bool)$settings['log_errors'],
            (bool)$settings['log_error_details']
        );
    },

];
```

## Assurons nous que notre app fonctionne

<!--  php -S localhost:8888 -t public/ -->
Deux options, si vous hébergez votre application à la racine du serveur, elle devrait fonctionner telle que. Mais souvent on la place dans un sous dossier et nous devons régler le chemin racine de l'application.

Pour ça nous avons un middleware.

```zsh
composer require selective/basepath
```

Ajoutez la définition de conteneur suivante dans `config/container.php`:

```php
use Selective\BasePath\BasePathMiddleware;
// ...

return [
    // ...

    BasePathMiddleware::class => function (ContainerInterface $container) {
        return new BasePathMiddleware($container->get(App::class));
    },
];
```

Ajoutez ensuite le `BasePathMiddleware::class` à la pile du middleware dans `config/middleware.php`:

```php
<?php

use Selective\BasePath\BasePathMiddleware;
use Slim\App;
use Slim\Middleware\ErrorMiddleware;

return function (App $app) {
    // Parse json, form data and xml
    $app->addBodyParsingMiddleware();

    // Add the Slim built-in routing middleware
    $app->addRoutingMiddleware();

    $app->add(BasePathMiddleware::class); // <--- ici

    // Catch exceptions and errors
    $app->add(ErrorMiddleware::class);
};
```

## Enfin un peu d'affichage avec notre première route

Nous pouvons maintenant définir une première route (chemin) dans `config/routes.php`


```php
<?php
use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;
use Slim\App;

    return function (App $app) {
        // la route
        $app->get('/', function (
            ServerRequestInterface $request,
            ResponseInterface $response
        ) {
            // le message
            $response->getBody()->write('<h1>Salut les gars !!!</h1>');

            return $response;
        });
    };

```

Si vous voulez essayer:

soit directement à partir du dossier

```zsh
php -S localhost:8888 -t public/
```

et `localhost:8888` dans votre navigateur.

Ou vous pouvez déposer le projet sur votre serveur local et aller à

`localhost/"nom du projet"`

vous devriez obtenir ceci

![firstroute](/img/firstroute.png)

## Les Actions

Chaque contrôleur à action unique est représenté par une classe individuelle ou une fermeture.

L' action ne fait que ces choses:

collecte les entrées de la requête HTTP (si nécessaire)
invoque le domaine avec ces entrées (si nécessaire) et conserve le résultat
crée une réponse HTTP (généralement avec les résultats de l'appel de domaine).
Toutes les autres logiques, y compris toutes les formes de validation d'entrée, de gestion des erreurs, etc., sont donc poussées hors de l'action et dans le domaine (pour les problèmes de logique du domaine) ou le moteur de rendu des réponses (pour les problèmes de logique de présentation).

Une réponse pourrait être rendue au format HTML (par exemple avec Twig) pour une demande Web standard; ou cela pourrait être quelque chose comme JSON pour les demandes d'API RESTful.

Créez un sous-répertoire: src/Action
Créez cette classe d'action dans: `src/Action/HomeAction.php`

```php
<?php

namespace App\Action;

use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;

final class HomeAction
{
    public function __invoke(
        ServerRequestInterface $request,
        ResponseInterface $response
    ): ResponseInterface {
        $response->getBody()->write("<h1>Salut d'action!</h1>");

        return $response;
    }
}
```

Ensuite, ouvrez `config/routes.php` et remplacez la fermeture de l'itinéraire pour `/` avec cette ligne:

```php
<?php

use Slim\App;

return function (App $app) {
    $app->get('/', \App\Action\HomeAction::class);
};
```

Essayez, ça affiche maintenant "Salut d'action!"

## Retour à notre objectif

On veut faire une api donc on doit écrire du Json

Pour créer une réponse JSON valide, vous pouvez écrire la chaîne codée json dans le corps de la réponse et définir l'en- Content-Typetête sur application/json:

```php
<?php

namespace App\Action;

use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;

final class HomeAction
{
    public function __invoke(
        ServerRequestInterface $request,
        ResponseInterface $response
    ): ResponseInterface {
        $response->getBody()->write(json_encode(['success' => true,
        'message' => "c'est gagné !"]));

        return $response->withHeader('Content-Type', 'application/json');
    }
}
```

Si vous ouvrez ça dans le navigateur, il devrait vous renvoyer

```json
{
"success": true,
"message": "c'est gagné !"
}
```

> l'ide devrait vous proposer d'ajouter `ext-json` dans `composer.json` sinon faites le.

> Pour lire de façon plus agréable le json sur Chrome, installez l'extension [JSON Viewer Awesome](https://chrome.google.com/webstore/detail/json-viewer-awesome/iemadiahhbebdklepanmkjenfdebfpfe)

<!-- >Relarque: si l'on a besoin de passer le code d'état HTTP on ajoute `$response->withStatus(x)`
```php
$result = ['error' => ['message' => 'Validation failed']];

$response->getBody()->write(json_encode($result));

return $response
    ->withHeader('Content-Type', 'application/json')
    ->withStatus(422);
``` -->

## Le domaine

Oubliez CRUD! Votre API doit refléter les cas d'utilisation commerciale et non les «opérations de base de données» techniques (CRUD) . Ne mettez pas la logique métier dans les actions. L'action appelle la couche domaine, resp. le service d'application. Si vous souhaitez réutiliser la même logique dans une autre action, il vous suffit d'appeler le service d'application dont vous avez besoin dans votre action.

**Les services**:
On va découper notre logique en "services" pour les rendre réutilisables.
Cela se passe dans le dossier `Domain` et dans dans des sous dossiers spécialisés.

Créons donc dans `src/Domain/User/Service/UserCreator.php` nos services


```php
<?php

namespace App\Domain\User\Service;

use App\Domain\User\Repository\UserCreatorRepository;
use App\Exception\ValidationException;

/**
 * Service.
 */
final class UserCreator
{
    /**
     * @var UserCreatorRepository
     */
    private $repository;

    /**
     * Le constructeur.
     *
     * @param UserCreatorRepository $repository Le dépot
     */
    public function __construct(UserCreatorRepository $repository)
    {
        $this->repository = $repository;
    }

    /**
     * Créer un nouvel utilisateur.
     *
     * @param array $data Le form data
     *
     * @return int L' ID du nouveau user
     */
    public function createUser(array $data): int
    {
        // Validation des données
        $this->validateNewUser($data);

        // Insertion du user
        $userId = $this->repository->insertUser($data);

        // Logging here: User created successfully
        //$this->logger->info(sprintf('User created successfully: %s', $userId));

        return $userId;
    }

    /**
     * Input validation.
     *
     * @param array $data The form data
     *
     * @throws ValidationException
     *
     * @return void
     */
    private function validateNewUser(array $data): void
    {
        $errors = [];

        // Here you can also use your preferred validation library

        if (empty($data['username'])) {
            $errors['username'] = 'Input required';
        }

        if (empty($data['email'])) {
            $errors['email'] = 'Input required';
        } elseif (filter_var($data['email'], FILTER_VALIDATE_EMAIL) === false) {
            $errors['email'] = 'Invalid email address';
        }

        if ($errors) {
            throw new ValidationException('Please check your input', $errors);
        }
    }
}
```
