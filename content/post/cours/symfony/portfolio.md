---
title: "Portfolio"
date: 2021-02-20T20:54:03+01:00
draft: true
description: Créons un portfolio
categories:
  - Projet
tags:
  - php
  - déploiement
  - symfony
  - twig
thumbnail: img/portfolio.png
lead: Créons un portfolio
comments: false
authorbox: true
toc: true
---

## Un portfolio automatisé

Nous allons ici réfléchir à comment réaliser un portfolio dynamique. Mon idée est de pouvoir montrer aux autres mes réalisations sans avoir à réaliser des screenshots, et surtout je ne veux avoir un site statique que je serais obliger de modifier continuellement si je créé un nouveau projet ou une nouvelle vidéo.

## Comment procéder?

Déjà, j'ai choisi Symfony pour plus de simplicité dans le développement. Une grande partie des composants sont déjà codés.
<!-- FIXME: utiliser sqlite peut être?-->
Pour une intégration simple avec **Heroku**, nous allons utiliser **postgresql** même si la quantité d'enregistrements ne nécessite pas un tel outil. Comme nous allons héberger notre projet sur Heroku postrgre est complètement intégré.

## Construisons notre environnement de dev

Un petit `symfony new portfolio --full` et `cd portfolio`. Après c'est à vous de voire `code .` ou vous ouvrez le projet avec votre IDE.

A la racine on va poser un `docker-compose.yml` avec comme contenu:

```yaml
version: '3.8'

services:
  database:
      image: postgres:13-alpine
      environment:
          POSTGRES_USER: main
          POSTGRES_PASSWORD: main
          POSTGRES_DB: main
      ports: [5432]
```

> Remarque: si vous n'avez pas docker sur votre machine, vous pouvez utiliser une autre base de donnée.

Maintenant `docker-compose up -d` dans le terminal et notre base de **postgre** de dev est en route.
On peut lancer `symfony server:start -d` et symfony reconnaît automatiquement le container et crée la base du projet.

## Nos Entités

Je vais partir du principe que je veux pouvoir afficher des lien vers mes vidéos avec, pourquoi pas, les cours associés.
Je veux aussi pouvoir afficher les dépots ou réalisations dont je suis fier.

Donc mes entités vont ressembler à ça:

![entités](/img/entityPortfolio.png)

## Un filtre pour les screenshots

Nous allons mettre en place un filtre twig pour rafraîchir automatiquement les screenshots des dépots.

Tout d'abord, on ne veut pas faire nous même un screen du dépot et on voudrait que les vignettes reflètent l'actualité du dépot.

Pour le premier problème, on va utiliser la librairie [spatie/browsershot](https://github.com/spatie/browsershot).
Pour le second, on va mettre en place un système qui actualise le screenshot tous les deux jours.

C'est partie. Intégrons Browsershot au projet:

```zsh
npm install puppeteer
```

et

```zsh
composer require spatie/browsershot
```

<!-- FIXME: il faudra intégrer ça https://medium.com/@timleland/headless-chrome-on-heroku-f5c3642bae61 -->


Créons l'extension Twig `symfony console make:twig-extension BrowserExtension` et modifions la comme ceci:

```php
<?php

namespace App\Twig;

use Twig\Extension\AbstractExtension;
use Twig\TwigFilter;
use Twig\TwigFunction;
use Spatie\Browsershot\Browsershot; //n'oubliez pas ce use
use Spatie\Image\Manipulations; // et celui ci
use App\Entity\Dep; // celui là sert à l'injection

class BrowserExtension extends AbstractExtension
{
    public function getFilters(): array
    {
        return [
            // If your filter generates SAFE HTML, you should add a third
            // parameter: ['is_safe' => ['html']]
            // Reference: https://twig.symfony.com/doc/2.x/advanced.html#automatic-escaping
            new TwigFilter('shot', [$this, 'urlThumbnail']),
        ];
    }

    public function getFunctions(): array
    {
        return [
            new TwigFunction('function_name', [$this, 'doSomething']),
        ];
    }

    public function urlThumbnail(Dep $value)
    {
        // on vérifie que le fichier existe
        if (file_exists("img/".$value->getName().".png")){
            // s'il existe on regarde s'il a moins de 2 jours sinon on le rafraîchie
            return (time()-filemtime("img/".$value->getName().".png") <= 2 * 3600)?"img/".$value->getName().".png":createImg($value);
        } else {

            return createImg($value);
        }

    }

}

 function createImg(Dep $value){
    $image = Browsershot::url($value->getUrl())
        ->windowSize(1920, 1080)
        ->fit(Manipulations::FIT_CONTAIN, 300, 300)
        ->save("img/".$value->getName().".png");
        return "img/".$value->getName().".png";
}

```

Tout d'abord la méthode

```php
public function getFilters(): array
    {
        return [

            new TwigFilter('shot', [$this, 'urlThumbnail']),
        ];
    }
```

introduit le pipe `shot`dans twig, nous verrons comment l'utiliser tout à l'heure, et utilise le méthode `urlThumbnail()` que nous venons de créer.

Que fait cette méthode? Comme vous pouvez le lire dans les commentaires, elle va d'abord vérifier que l'image n'a pas en core été créé. Si elle n'éxiste pas nous la créons avec la librairie importée plus tôt.

```php
$image = Browsershot::url($value->getUrl())
        ->windowSize(1920, 1080)
        ->fit(Manipulations::FIT_CONTAIN, 300, 300)
        ->save("img/".$value->getName().".png");
```

la classe `Brothershot` permet de faire un screenshot d'un site avec une taille donnée et le sauvegarder où l'on veut.
Remarquez qu'ici on utilise l'injection de dépendance en recevant un objet `Depo` dont nous allons utiliser le champ url pour générer et nommer l'image.

...

## Intégration de Tailwindcss

On intègre webpack encore à notre projet:

```zsh
composer require symfony/webpack-encore-bundle
```

On install les dépendances de webpack avec npm

```zsh
npm install
```

Conformément à la doc de Tailwind on install Tailwind et les composants dont il aura besoin

```zsh
npm install -D tailwindcss postcss-loader purgecss-webpack-plugin glob-all path autoprefixer
```

Pour les réglages, nous ajoutons un fichier `postcss.config.js`

```javascript
module.exports = {
    plugins: [
        require('tailwindcss'),
    ],
};
```

Au début de `webpack.config.js` ajoutez

```javascript
var Encore = require('@symfony/webpack-encore');
const PurgeCssPlugin = require('purgecss-webpack-plugin');
const glob = require('glob-all');
const path = require('path');
```

et

```javascript
.enablePostCssLoader()
```

Enfin, ajoutons les lignes suivantes à `assets/styles/app.css`

```css
@import "tailwindcss/base";

@import "tailwindcss/components";

@import "tailwindcss/utilities";
```

Pour que toutes les mofifications soient compilées et pise en compte on fait `npm run build` dans le terminal.

ajout de purgecss

dans webpack.config.js

```javascript
if (Encore.isProduction()) {
    Encore.addPlugin(new PurgeCssPlugin({
          paths: glob.sync([
              path.join(__dirname, 'templates/**/*.html.twig')
          ]),
          defaultExtractor: (content) => {
              return content.match(/[\w-/:]+(?<!:)/g) || [];
          }
      }));
  }
;
```

...

## Déployons sur heroku



> verifier que le build n'est pas dans le gitignore

