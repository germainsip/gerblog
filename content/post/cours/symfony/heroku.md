---
title: "Heroku"
date: 2021-02-17T09:04:31+01:00
draft: true
description: Déployons une appli symfony sur heroku
categories:
  - Déploiement
tags:
  - php
  - déploiement
thumbnail: img/herosymfdep.png
lead: Déployons une appli symfony sur heroku
comments: false
authorbox: true
toc: true
---

> ce cours et une **révision** du tuto de yoandev  [ici](https://yoandev.co/mettre-en-production-une-application-symfony-5-avec-heroku), il sera modifié en profondeur par la suite.


## Qu'allons nous faire ici?

Je vais vous montrer comment déployer une application symfony sur Heroku.
Cette technique va nous permettre de tester hors de notre machine notre application.
L'application va permettre de lister et de revoir les vidéos Youtube que l'on a faites. On en profitera pour éditer des filtre twig personnalisés.

## Préparatifs

Je vais vous demander d'avoir installé docker sur votre poste (on s'en servira pour émuler la base de donnée). Nous aurons aussi besoin que vous ayez un compte sur heroku et installé le CLI d'heroku. Enfin, mais ça vous devez déjà l'avoir, j'utilise aussi le CLI de symfony.

## Création du projet

dans votre dossier de développement créons le nouveau projet symfony.

```zsh
symfony new deploy-heroku --full
cd deploy-heroku
```

> Vous pouvez choisir le nom que vous voulez pour le projet.

## Créons notre container PostGreSql

Pour travailler avec Heroku, nous allons utiliser une base de donnée postgresql. Effectivement, Heroku supporte nativement postgre et comme nous n'avons pas ce moteur sur notre machine nous allons simplement créer un conteneur avec docker.

Pour docker, il nous faut un fichier docker-compose.yml à la racine de notre machine avec ce code:

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

Rien de très particulier. On déclare juste que nous allons utiliser l'image `postgres`et qu'on utilisera le port 5432.

## Première mise en route

Le serveur de dev de symfony est en mesure de gérer directement Postgre sans aucunes modifications de notre part. (ça c'est cool...)

Pour lancer tout ça on va faire dans le terminal

```zsh
docker-compose up -d
```

pour lancer la le conteneur

puis

```zsh
symfony server:start -d
```

pour lancer symfony.

## On commence

Créons notre entité

```zsh
symfony console make:entity Youtube
```

On a deux champs:

- name, string, 255, non nul
- url, string, 255, non nul

Cela va nous permettre de stocker les nom et url des vidéos que nous voulons mettre en avant.

On fait le migration: `symfony console make:migration`
Et on effectue les changements `symfony console doctrine:migrations:migrate`

Si tout s'est bien passé, nous avons maintenant un base avec une table `youtube`.

## Un formulaire d'ajout

De la même façon avec le cli de symfony nous créons le formulaire dont nous aurons besoin.

```zsh
symfony console make:form YoutubeType
```

Il faut modifier le fichier généré dans `src/Form/YoutubeType.php`

```php
<?php

namespace App\Form;

use App\Entity\Youtube;
use PhpParser\Node\Stmt\Label;
use Symfony\Component\Form\AbstractType;
use Symfony\Component\Form\Extension\Core\Type\SubmitType;
use Symfony\Component\Form\Extension\Core\Type\TextType;
use Symfony\Component\Form\Extension\Core\Type\UrlType;
use Symfony\Component\Form\FormBuilderInterface;
use Symfony\Component\OptionsResolver\OptionsResolver;

class YoutubeType extends AbstractType
{
    public function buildForm(FormBuilderInterface $builder, array $options)
    {
        $builder
            ->add('url', UrlType::class)
            ->add('name', TextType::class, ['label' => 'Nom'])
            ->add('Submit', SubmitType::class, ['label' => 'Enregistrer'])
        ;
    }

    public function configureOptions(OptionsResolver $resolver)
    {
        $resolver->setDefaults([
            'data_class' => Youtube::class,
        ]);
    }
}

```

Afin que ce soit **Nom** et **Enregistrer** qui s'affiche vous remarquerez que j'ai ajouté des options à mes champs.

## Un controller

Encore une fois un peu de console:

```zsh
symfony console make:controller Youtube
```

Changeons quelques petites choses sur ce controller: `src/Controller/YoutubeController.php`

```php
    /**
     * @Route("/", name="app_home")
     */
    public function index(Request $request,EntityManagerInterface $em, YoutubeRepository $youtubeRepository): Response
    {
        $youtube = new Youtube();

        $form = $this->createForm(YoutubeType::class,$youtube);

        $form->handleRequest($request);

        if ($form->isSubmitted() && $form->isValid()){
            $youtube = $form->getData();

            $em->persist($youtube);
            $em->flush();

            return $this->redirectToRoute('app_home');
        }


        return $this->render('youtube/index.html.twig', [
            'form'=>$form->createView(),
            'youtubes' => $youtubeRepository->findAll()
        ]);

```

On modifie la méthode index ainsi que sa route. On injecte `Request`,l'`EntityManager`et le Repository `YoutubeRepository`.

Ainsi on peut aller chercher tous les enregistrements de la base, les afficher et en enregistre d'autres.
Remarquez aussi que l'on renvoie toujours vers cette page après validation du formulaire:

`return $this->redirectToRoute('app_home');`

Notre template `/templates/youtube/index.html.twig` est à modifier maintenant.

```twig
{% extends 'base.html.twig' %}

{% block title %}Best Youtube Video !{% endblock %}

{% block body %}

    {{ form(form) }}

    {% for youtube in youtubes %}

        <p>{{ youtube.url }} - {{ youtube.name }}</p>

    {% endfor %}

{% endblock %}
```

Si vous testez dans le navigateur, le résultat n'est pas très jolie mais fonctionnel.




<!-- TODO: à compléter -->

## Ajout de filtre twig

ajout de `https://packagist.org/packages/copadia/php-video-url-parser`

librairie qui donne le thumbnail d'une video youtube.

`symfony console make:twig-extension`

nommer l'extension

et modifier le fichier en:

```php
<?php

namespace App\Twig;

use Twig\Extension\AbstractExtension;
use Twig\TwigFilter;
use Twig\TwigFunction;
use RicardoFiorani\Matcher\VideoServiceMatcher;

class YoutubeExtension extends AbstractExtension
{
    private $youtubeParser;

    public function __construct()
    {
        $this->youtubeParser = new VideoServiceMatcher();
    }

    public function getFilters(): array
    {
        return [
            // If your filter generates SAFE HTML, you should add a third
            // parameter: ['is_safe' => ['html']]
            // Reference: https://twig.symfony.com/doc/2.x/advanced.html#automatic-escaping
            new TwigFilter('youtube_thumbnail', [$this, 'youtubeThumbnail']),
        ];
    }

    public function getFunctions(): array
    {
        return [
            new TwigFunction('function_name', [$this, 'doSomething']),
        ];
    }

    public function youtubeThumbnail($value)
    {
        $video = $this->youtubeParser->parse($value);
        return $video->getLargestThumbnail();
    }
}

```

