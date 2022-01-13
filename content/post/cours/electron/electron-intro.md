---
title: "Electron Intro"
date: 2021-04-29T09:41:07+02:00
draft: false
description: Création d'une app desktop avec electron
categories:
  - Electron
tags:
  - javascript
  - electron
thumbnail: img/electron/electronjs.gif
lead: Création d'une app desktop avec electron
comments: false
authorbox: true
toc: true
mathjax: true
---
# Electron

<iframe width="560" height="315" src="https://www.youtube.com/embed/videoseries?list=PLDgCz2YzJLyWK7uQvHBnciTrJ_Ed76Vhu" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## L'architecture d'electron

Electron utilise plusieurs processus pour gérer les états de l'application et l'interface utilisateur. Le processus principal `main` gère les états de l'application pendant que le `renderer` s'occupe de l'interface utilisateur.

Au démarrage d'une application Electron un processus `main` est créé. On ne peut en avoir qu'un seul. Ce `main` a accès aux api de **Node.js** mais pas celles de **chromium**. De ce fait, on peut utiliser les API node comme `fs.writeFile()`ou `require()` mais pas  `document` ou encore `window.addEventListener()`.

Dans l'idée, Electron peut être vu comme un navigateur. Le `main` est le navigateur et le `renderer` un onglet du navigateur. Du coup, le `main` peut ouvrir autant d'onglet qu'il a besoin. Ici on appellera, pour **Electron**, un onglet une fenêtre (`window`) puisque nous créons une application desktop.

![fen-base](/img/electron/fen-base.png)

A partir du `main.js` (processus `main`) on crée un fenêtre en instanciant [`BrowserWindow`](https://www.electronjs.org/docs/api/browser-window) accessible à partir du package `electron`.

Cette fenêtre `win` est vide et on y charge une page web (HTML) avec `win.loadFile(path)` ou `win.loadURL(url)` (distant). Avec cet appel **Electron** crée un processus de rendu `renderer`et affiche la page.

```js
//main.js
const { BrowserWindow } = require('electron')

//Création d'une fenêtre
const win = new BrowserWindow({
    width: 500,
    height: 200
  })

  win.loadFile('index.html')
```

Le `main` orchestre l'application mais ne peut pas créer d'interface utilisateur. Il doit créer un `renderer`à partir de l'interface `BrowserWindow`.

> `BrowserWidow` est une interface de haut-niveau pour rendre et controller la partie web mais ne peut pas rendre par elle même de page web. L'interface `webContents` est une api de bas niveau qui est responsable du rendu et du contrôle de la page en utilisant **Chromium**.

Le processus de rendu (`renderer`) est un processus système (OS) associé avec l'interface `webContent`. On peut accéder à cette interface avec l'interface `BrowserWindow` en utilisant la propriété `win.webContents`. Pour connaître l'adresse OS PID, on utilise `win.webContents.getOSProcessId()` et pour avoir le PID du renderer Chromium `win.webContents.getProcessId()`.

> On pourrai croire qu'il n'a qu'un processus de rendu par fenêtre, mais ce n'est pas toujours le cas. Le renderer est créé au chargement d'une page dans une fenêtre. Une fenêtre peut avoir d'autres renderer via un `<webview>`ou en ajoutant un `BrowserView` à une fenêtre existante.

## Main et Renderer

Le processus principal (`main`) s'occupe de l'application et le processus de rendu (`renderer`) contrôle l'UI.

Cependant le renderer n'a pas accès à beaucoup de choses. Le main a accès à l'api node par défaut. On peut, si on le souhaite, à la création d'une fenêtre avec `new BrowserWindow(options)` autoriser le renderer a utiliser l'API Node en ajoutant `nodeIntegration: true`. Sa valeur est `false` par défaut pour des raisons de sécurité. On pourrai par exemple passer ce paramètre à `true` si on a besoin de `fs.writeFile()` ou `require()` à partir du renderer.

> Le fait d'activer cet intégration de Node n'est pas recommandée car on peut donner accès au systèmes à des logiciels malicieux.

De même, on ne peut pas créer une nouvelle fenêtre à partir d'une fenêtre. Cela doit se faire à partir du `main`.

> `window.open()` peut quand même créer un affichage mais avec des fonctionnalités limités.

Autre chose, tout ce qui est relatif à l'API système est uniquement accessible depuis le `main` (ex: `dialog`).
Certains sont quand même accessibles aux deux comme vous pouvez le voir dans ce tableau récapitulatif.

|Main|Renderer|Commun|
|---|---|---|
|app|desktopCapturer|clipboard|
|autoUpdater|ipcRenderer|crashReporter|
|BrowserView|remote|nativeImage|
|BrowserWindow|webFrame|shell|
|contentTracing|  |  |
|dialog| | |
|globalShortcut| | |
|inAppPurchase| | |
|ipcMain| | |
|Menu| | |
|MenuItem| | |
|net| | |
|netLog| | |
|nativeTheme| | |
|Notification| | |
|powerMonitor| | |
|powerSaveBlocker| | |
|protocol| | |
|screen| | |
|session| | |
|systemPreferences| | |
|TouchBar| | |
|Tray| | |
|webContents| | |
|webFrameMain| | |

## Communication entre `main` et `renderer`

Alors, on fait comment? Par exemple, on voudrait ouvrir une nouvelle fenêtre avec une balise `<button>` avec une une image et un contrôle du zoom. C'est là que l'IPC va nous être util.
On peut faire communiquer le `renderer` et le `main` grace aux module IPC. Le processus `main` peut accéder à `ipcMain` et le `renderer` à `ipcRenderer`.

Le `ipcMain` peut écouter un évenement envoyé par le renderer en utilisant `ipcMain.on()` ou il peut gérer l'exécution de la fonction invoquée par le processus de rendu en utilisant `ipcMain.handle()`. De même, le module `ipcRenderer` peut envoyer des messages au processus `main` en utilisant la méthode `ipcRenderer.send()` ou appeler une procédure à l'intérieur du processus principal en utilisant la méthode `ipcRenderer.invoke()`.

Un bon exemple du processus principal dépendant du processus de rendu serait l'état de la connectivité réseau. Nous pouvons écouter les événements en ligne ou hors ligne dans un navigateur ([documentation](https://developer.mozilla.org/en-US/docs/Web/API/NavigatorOnLine/Online_and_offline_events)). Il est donc possible d’obtenir la connectivité réseau de l’appareil de l’utilisateur dans le processus de rendu, mais nous ne pouvons pas faire de même dans le processus principal. Ainsi, le seul moyen pour le processus principal d'obtenir l'état de la connectivité réseau est de demander au processus de rendu. Par conséquent, un processus de rendu peut envoyer un message au processus principal chaque fois que ce statut change (en utilisant les modules ipcMain et ipcRenderer).
Si vous souhaitez communiquer entre deux processus de rendu, vous devez utiliser le processus principal comme pont ou si vous avez accès à l'interface `webContents` d'un processus de rendu, vous pouvez utiliser la méthode `webContents.send()` pour envoyer un message à un autre processus de rendu et utilisez la méthode `ipcRenderer.on()` pour écouter ce message.

## Les Tâches de fond

Si vous souhaitez effectuer une tâche gourmande en ressources processeur, vous ne voudriez pas exécuter le code JavaScript de longue durée dans un processus de rendu qui bloquerait l'interface utilisateur et la laisserait morte pendant quelques secondes ou minutes. Cependant, si vous le faites, disons par accident, les autres fenêtres ne seront pas affectées car elles s'exécutent dans des processus de rendu distincts.

Mais encore, bloquer l’interface utilisateur n’est pas bon car c’est mauvais pour l’expérience utilisateur. À quoi sert votre application si l'utilisateur ne peut pas interagir avec l'interface utilisateur de l'application? Vous diriez, utilisons les processus principaux, car ils ne sont pas liés au processus de rendu, mais malheureusement, le blocage du processus principal laisserait votre application (et toutes ses fenêtres) morte également.

Pour exécuter des tâches en arrière-plan, nous n'avons pas besoin d'inventer la roue. Nous avons déjà quelques trucs dans nos manches. Nous pouvons utiliser l'API [WebWorker](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers) de JavaScript s'exécutant à l'intérieur du processus de rendu pour démarrer une tâche en arrière-plan. Nous pourrions également utiliser le module intégré [worker_threads](https://nodejs.org/api/worker_threads.html) de Node.js pour démarrer un thread de travail comme un thread `WebWorker`.
Certaines personnes peuvent suggérer d'utiliser une fenêtre transparente sans cadre que les utilisateurs ne peuvent pas voir pour effectuer des tâches en arrière-plan et gourmandes en ressources processeur. Voici un [article](https://www.geeksforgeeks.org/frameless-window-in-electronjs/) qui décrit exactement cela. Cependant, nous devrions éviter d'utiliser de telles astuces lorsque nous avons de meilleures façons de faire les choses.
Un `WebWorker` n'a pas automatiquement accès à l'API Node.js. Nous devons définir la propriété `options.webPreferences.nodeIntegrationInWorker` sur true lors de la création d'un objet `BrowswerWindow`. Cette propriété fonctionne quelle que soit la propriété `nodeIntegration`.

## Vu de plus haut

Ce dont nous avons discuté jusqu’à présent n’est qu’un aperçu de l’architecture d’Electron. Si vous voulez creuser plus profondément, vous voudrez peut-être lire ceci. Résumons ce dont nous avons discuté jusqu'à présent avec un diagramme simplifié.

![diag2](/img/electron/diag2-2.png)

<!-- TODO: image à refaire -->

L'ensemble du processus et de l'architecture de communication d'Electron est basé sur l'architecture de Chromium. Par exemple, le concept de processus principal et de rendu, ainsi que le mécanisme de communication IPC sont dérivés de l’architecture de Chromium. Pour en savoir plus sur le fonctionnement de Chromium sous le capot, lisez cette [documentation](https://www.chromium.org/developers/design-documents/multi-process-architecture) sur chromium.org.

## Créons une application Electron

Créons une application desktop simple qui affiche "Je débute avec Electron!" dans une fenêtre et un bouton qui ouvre une fenêtre avec une image aléatoire d'Internet.

Créons un répertoire de projet. Pour moi, le nom du répertoire du projet est `electron-un`. La structure du projet ressemblera à celle ci-dessous, mais nous passerons en revue chacun de ces fichiers un par un.

```zsh
electron-un/
├── main.js
├── hello.html
├── image.html
├── img.jpeg
├── package-lock.json
└── package.json
```

Désormais, pour créer des applications desktop à l'aide d'Electron, nous n'avons pas besoin d'une configuration sophistiquée ni d'installer une application. Nous avons juste besoin du package electron que nous pouvons installer à l'aide de la commande `npm install` ou `yarn add`. Alors initialisons notre projet avec `npm init`. Et ensuite ajoutons electron.

 ```zsh
npm init -y
npm install --save-dev electron
npx electron --version
  v11.1.1
 ```

 > le `-y`  oblige npm à répondre aux questions par lui même.

 Voici maintenant un point important que vous ne devez jamais oublier. Le point d'entrée d'une application Electron est un fichier JavaScript. Ce fichier JavaScript s'exécute dans le processus principal et ouvrira les fenêtres d'application comme indiqué précédemment. Créons donc le fichier `main.js` dans le projet.

```js
//main.js
const { app, BrowserWindow, ipcMain } = require( 'electron' );

// ouvrir une fenêtre
const openWindow = ( type ) => {
    const win = new BrowserWindow( {
        width: 400,
        height: 300,
    } );

    if( type === 'image' ) {
        win.loadFile( './image.html' ); // fenêtre image
    } else {
        win.loadFile( './hello.html' ); // fenêtre par défaut
    }
}

// quand l'application est prête on crée la fenêtre
app.on( 'ready', () => {
    openWindow();
} );

// si toutes le fenêtre sont fermées on quite
app.on( 'window-all-closed', () => {
    if( process.platform !== 'darwin' ) {
        app.quit(); // exit
    }
} );

// quand l'appli est active on ouvre une fenêtre
app.on( 'activate', () => {
    if( BrowserWindow.getAllWindows().length === 0 ) {
        openWindow();
    }
} );

// à l'écoute du message de l'appli
ipcMain.on( 'app:display-image', () => {
    console.log( '[message received]', 'app:display-image' );
    openWindow( 'image' );
} )
```

Voyons ce que nous faisons dans ce fichier. Tout d'abord, nous importons les modules `app`, `BrowserWindow` et `ipcMain` à partir du package `electron`. Nous avons déjà discuté des modules `BrowserWindow` et `ipcMain`, alors laissons cela de côté un instant et concentrons-nous sur le module `app`.

Le module `app` est une interface pour contrôler le cycle de vie de l'application. Si nous voyons la table d'accessibilité du module, ce module n'est disponible que pour le processus principal. Ce module nous donne la possibilité d'écouter les événements du cycle de vie de l'application, d'appeler des méthodes pour modifier l'état de l'application et de lire l'état de l'application via des propriétés statiques.
Dans le fichier `main.js` ci-dessus, nous écoutons l'événement `ready` de l'application à l'aide de la méthode `app.on(event, handler)`. Cet événement est déclenché une seule fois lorsque Electron a terminé l'initialisation de l'application et que les fenêtres peuvent être créées en toute sécurité.

>💡 Au lieu de l'approche `app.on('ready', handler)`, vous pouvez également utiliser la méthode `app.whenReady()` qui renvoie une promesse. Dans le gestionnaire `then()` de cette promesse, vous pouvez faire ce que nous faisons dans le fichier `main.js` ci-dessus.

L'événement `window-all-closed` est émis lorsque la dernière fenêtre ouverte de l'application est fermée. Dans le gestionnaire de cet événement, nous devons quitter l'application en appelant la méthode `app.quit()` sauf lorsque le système d'exploitation est macOS car il contredit le comportement par défaut de macOS.
>💡 La variable de processus provient de Node.js puisque le processus principal a toujours accès aux API Node.js. Par conséquent, `process.platform` donne le nom de la plate-forme sous-jacente (noyau ou OS) sur laquelle Node.js s'exécute.

L'événement d'activation est spécifique à macOS et il est déclenché lorsque l'utilisateur clique sur l'icône de l'application du dock (et à d'autres endroits). Étant donné que la fermeture de toutes les fenêtres dans macOS ne ferme pas l'application (processus principal), nous aurions besoin d'ouvrir une fenêtre (si aucune n'est ouverte) lorsque l'application est à nouveau activée.
Lorsque l'application est prête, nous appelons la fonction `openWindow()` qui crée une fenêtre de taille 600x300 et ouvre le fichier `hello.html` par défaut qui se trouve dans le même répertoire que le `main.js`. Si la valeur de l'argument de type est image, cela créera une autre fenêtre et ouvrira le fichier `image.html` dans cette fenêtre.

Puisque nous voulons ouvrir la fenêtre d'image lorsque l'utilisateur appuie sur un bouton dans la fenêtre par défaut, nous devons envoyer un message de la fenêtre par défaut (processus de rendu) au `main.js` (processus principal). Nous écoutons ce message dans le `main.js` en utilisant la méthode `ipcMain.on()`. Lorsque `main.js` reçoit l'événement `app: display-image`, il ouvrira la fenêtre d'image.
>💡 J'utilise le format app: `<event-name>` pour les événements personnalisés mais il n'y a pas de convention pour nommer les noms d'événements. Ce devrait être une chaîne, c’est tout.

```html
<!DOCTYPE html>
<html lang="fr">
<head>
	<meta charset="UTF-8">
	<meta http-equiv="X-UA-Compatible" content="IE=edge">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline';" />
	<link href="https://unpkg.com/tailwindcss@^2/dist/tailwind.min.css" rel="stylesheet">
	<title>Ma première appli Electron</title>
</head>

</head>
<body class="flex items-center justify-center h-screen">
	<div class="text-center">
		<h1 class="text-4xl font-semibold">Je suis <span class="font-bold text-green-400">Electron</span>!</h1>
		<button class="rounded bg-green-400 hover:bg-green-500 hover:shadow text-white mt-5 p-1" id='btn'>Cliquez pour voir une image</button>
	</div>


	<script>
		const { ipcRenderer } = require( 'electron' );

		document.getElementById( 'btn' ).onclick = function() {
			ipcRenderer.send( 'app:display-image' );
			console.log( '[message sent]', 'app:display-image' );
		}
	</script>
</body>
</html>
```

>Le style est fait avec tailwindcss et son CDN. Juste un coquetterie de ma part.

Notre fichier hello.html n'est qu'une simple page Web HTML. Electron recommande d'ajouter un en-tête de réponse CSP (Content Security Policy) lors du chargement d'une page Web dans une fenêtre. Lors du chargement d'un fichier HTML local, l'en-tête CSP peut être ajouté via une balise Meta. Cette étape est nécessaire pour ajouter une couche de sécurité supplémentaire aux attaques XSS comme décrit [ici](https://www.electronjs.org/docs/tutorial/security#6-define-a-content-security-policy)

L'élément `<title>` spécifie le titre du document et Electron utilise cette valeur pour le titre de la fenêtre que nous verrons dans une minute. Vous pouvez également fournir le titre de la fenêtre via la propriété title lors de la création d'une instance de `BrowserWindow`.
Dans notre page, nous avons un "Je suis Electron!" enveloppé dans un élément `<h1>` et un bouton avec l'ID `#btn`. L'élément `<script>` a du JavaScript qui semble un peu différent du JavaScript normal que nous utilisons dans le navigateur.

Comme indiqué précédemment, le processus de rendu a également accès aux API Node.js, ce qui signifie que tout JavaScript s'exécutant dans une fenêtre a également accès à ces API. C'est pourquoi nous pouvons utiliser la fonction require de Node dans l'élément `<script>`. La même règle s'applique si nous importons un fichier JavaScript externe à l'aide de l'élément `<script src = "path-to-file.js">`.
Dans ce JavaScript, nous importons le module `ipcRenderer` du package electron pour envoyer le message `app: display-image` au processus principal lorsque l'utilisateur appuie sur le bouton **Cliquez pour voir une image**. Vous pouvez également accéder aux modules Node intégrés tels que `fs` ou `path` ainsi qu'à des variables globales telles que `process` et autres.

Démarrons notre application Electron. Pour démarrer l'application Electron, nous devons utiliser la commande `electron [options] <path>`. Cette commande est fournie par le package `electron`. Ici path est le chemin de `main.js` ou un répertoire contenant `index.js` (qui serait le point d'entrée) ou un répertoire contenant `package.json`. Ce `package.json` doit avoir le chemin du point d'entrée (fichier JavaScript) spécifié par le champ `main`.
Dans notre cas, nous exécuterons la commande `electron .` à partir du répertoire du projet. Puisque nous avons le `package.json` dans ce répertoire, nous devons spécifier le chemin de `main.js` qui est le point d'entrée de notre application via le champ `main` du `package.json`.

```json
{
  "name": "exemple",
  "version": "1.0.0",
  "description": "un simple exemple",
  "main": "main.js", 
  "author": "Germain SIPIERE",
  "license": "MIT",
  "private": false,
  "scripts": {
    "start": "electron ."
  },
  "devDependencies": {
    "electron": "^12.0.5"
  }
}
```

Puisque electron est un package local, nous ne pouvons pas utiliser `electron .` depuis le terminal. Vous pouvez exécuter `npx electron .` mais il est plus pratique d'ajouter un script dans `package.json` comme nous l'avons fait ci-dessus et d'utiliser la commande `npm run start` pour démarrer l'application.

![fen2](/img/electron/electron2.png)

Lorsque nous utilisons `npm run start`, Electron exécute le fichier `main.js` et l'événement `ready` est déclenché, ce qui ouvre une fenêtre avec le fichier `hello.html` similaire à celui que vous voyez ci-dessus. Notez le titre de la fenêtre. Cette commande bloquera le terminal à partir duquel cette commande a été exécutée et toutes les instructions `console.log` exécutées dans le processus principal (`main.js`) seront enregistrées ici.
Comme j'utilise macOS, je vais ouvrir le moniteur d'activité pour voir quels processus cette application a démarré.

![moniteur](/img/electron/moniteur.png)

Le processus avec le nom Electron est le processus principal. Le processus nommé Electron Helper (Renderer) est le processus de rendu. Comme nous n'avons qu'une seule fenêtre ouverte, nous n'avons qu'un seul processus de rendu. Les autres processus ne sont que des processus auxiliaires du processus principal.
Cliquons sur le bouton Cliquer pour ouvrir une photo rendue dans la fenêtre et voyons ce qui se passe. Eh bien, rien ne se passera. Pour voir le problème, nous devons ouvrir DevTools.

Pour attacher un DevTools à la fenêtre actuelle, appuyez sur Cmd + Option + I (macOS) et cela ouvrira le panneau DevTools dans la fenêtre.

>💡 Le panneau DevTools est techniquement une autre fenêtre, il lancera donc un autre processus de rendu que vous pouvez voir dans le moniteur d'activité. Si vous souhaitez ouvrir DevTools par programmation, utilisez `win.openDevTools()`.

![devtool](/img/electron/devtool.png)

Le JavaScript s'exécutant dans cette fenêtre a levé une exception. Il dit que la variable require n'existe pas à la ligne no. 21 du `hello.html`. Mais la fonction `require()` aurait dû être là puisqu'elle est fournie par les API Node.js. D'une manière ou d'une autre, le JavaScript exécuté dans le processus de rendu n'a pas accès aux API Node.js. C'est parce que nous n'avons pas activé l'intégration node ni désactivé l'isolation de context lors de la création de la fenêtre.

```js
const openWindow = ( type ) => {
    const win = new BrowserWindow( {
        width: 400,
        height: 300,
        webPreferences: {
            nodeIntegration: true, //ici
            contextIsolation: false //et là
        },
    } );
    //...
}
```

Maintenant vous ne devriez plus avoir d'erreur en console (mais nous y reviendrons). Voyons à quoi ressemble le fichier `image.html` avant de cliquer sur le bouton et de créer un désordre.

```html
<!DOCTYPE html>
<html lang='fr'>
    <head>
        <meta charset='UTF-8'>
        <meta name='viewport' content='width=device-width, initial-scale=1.0'>
        <meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline';" />
        <link href="https://unpkg.com/tailwindcss@^2/dist/tailwind.min.css" rel="stylesheet">
        <title>Image au hasard</title>

        <style>
            body {
                font-size: 0;
                background-color: #eee;
                font-family: sans-serif;
                text-align: center;
                vertical-align: middle;
                padding: 0;
                margin: 0;
            }
        </style>
    </head>
    <body>
        <!-- random image -->
        <h3 id='loading' style="font-size: 14px;">Chargement...</h3>
        <img id='img' src='' style="display: none;">

        <script>
            const path = require( 'path' );
            const axios = require( 'axios' );
            const sharp = require( 'sharp' );

            // récupération d'une image
            axios.get( 'https://source.unsplash.com/random', {
                responseType: 'arraybuffer',
            } )
            .then( ( response ) => {

                // creation d'on objet Buffer
                const buffer = Buffer.from( response.data, 'binary' );

                // on déclare le chemin pour la sauvegarde de l'image
                const outPath = path.resolve( __dirname, 'img.jpeg' );

                // redimensionnement image avec sharp
                sharp( buffer )
                .resize( 600, 300 )
                .toFile( outPath ) // on sauve l'image
                .then( () => {

                    // affichage de l'image dans le tag `img`
                    document.getElementById( 'img' ).setAttribute( 'src', `file://${ outPath }` );
                    document.getElementById( 'img' ).setAttribute( 'style', '' );
                    document.getElementById( 'loading' ).setAttribute( 'style', 'display:none;' );
                } );
            } );
        </script>
    </body>
</html>
```

Dans `image.html`, nous récupérons une image aléatoire (jpeg) à partir de l'URL `https://source.unsplash.com/random` à l'aide du package `axios`. Ensuite, nous convertissons cette image en un objet Buffer. L'interface Buffer est fournie par Node.js. Ensuite, nous redimensionnons cette image en utilisant un package `sharp`. Nous devons donc installer ces packages.

```zsh
npm install --save axios sharp
```

Comme la variable `__dirname` fournie par Node.js pointe vers le chemin absolu de `image.html` sur le système de fichiers, c'est pourquoi le chemin outPath est le `<project-path>/img.jpeg`. Lorsque nous enregistrons cette image en utilisant `.toFile(outPath)`, elle est enregistrée dans le répertoire du projet.

Le protocole `file://` est utilisé pour charger un fichier à partir du système de fichiers local. L'URL de l'attribut src est `file:// <chemin-absolu-vers-img>.jpeg`, ce qui signifie que l'élément `<img/>` affichera le fichier récemment enregistré. Ainsi, lorsque nous cliquons sur le bouton **Cliquez pour voir une image**, une nouvelle fenêtre s'ouvrira et rendra le fichier téléchargé. Vous devriez également voir apparaître le fichier `img.jpeg` dans le répertoire du projet.

![image](/img/electron/image.png)

## Ce n'est pas fini

Je ne vous ai pas tout dit. Dans la version 12 d'Electron, l'intégration de node et l'isolation ont des paramètre par défaut pour une bonne raison. Ce sont des portes ouvertes aux logiciels malicieux. Un script peut avoir accès à votre système en passant par le renderer directement. CE N'EST PAS BON !

### Corrigeons ça

On va désactiver les deux options dans le `main.js` au niveau du `BrowserWindow`:

```js
const openWindow = ( type ) => {
    const win = new BrowserWindow( {
        width: 600,
        height: 300,
        webPreferences: {
            preload: path.join(__dirname,'preload.js')
        },
    } );
```

Comme vous le voyez on ajoute également un préloader. Ce fichier sera chargé à partir du main et pas de la fenêtre.

> Si vous relancez maintenant, plus rien ne fonctionne évidemment.

Ajoutez le fichier `preload.js` dans le projet.

```js
const { ipcRenderer } = require( 'electron' );

window.addEventListener('DOMContentLoaded', () => {
	const btn1 = document.getElementById('btn')
	if (btn !== null){
		btn1.onclick = function() {
			ipcRenderer.send( 'app:display-image' );
			console.log( '[message sent]', 'app:display-image' );
		}
	}
  })
```

et on enlève le script de `hello.html`. Dans le détail, que ce passe-t-il? On ajoute un écouteur à la fenêtre qui déclenche une méthode quand la page est chargée.
On récupère alors l'élément portant l'id `btn`. Je triche un peu pour la suite en vérifiant que l'objet n'est pas null car je vais utiliser le même préloader pour plusieurs pages.
Si cet objet n'est pas null on fait ce que l'on faisait avant directement dans le html dans la balise `<script>...</script>`.

Le même travail est à faire pour `image.html` donc toujours dans `preloader.js` nous ajoutons ce qu'il manque et nous le supprimons de `image.html`

```js
const { ipcRenderer } = require( 'electron' );
const path = require( 'path' );
const axios = require( 'axios' );
const sharp = require( 'sharp' );


window.addEventListener('DOMContentLoaded', () => {
	const btn1 = document.getElementById('btn')
	const btn2 = document.getElementById('btn2')
	const img = document.getElementById('img')
	const loading = document.getElementById('loading')
	if (btn !== null){
		btn1.onclick = function() {
			ipcRenderer.send( 'app:display-image' );
			console.log( '[message sent]', 'app:display-image' );
		}
	}
	if (img !== null && loading !== null){
		axios.get( 'https://source.unsplash.com/random', {
		responseType: 'arraybuffer',
	} )
	.then( ( response ) => {

		const buffer = Buffer.from( response.data, 'binary' );
		const outPath = path.resolve( __dirname, 'img.jpeg' );
		sharp( buffer )
		.resize( 600, 300 )
		.toFile( outPath ) 
		.then( () => {
			img.setAttribute( 'src', `file://${ outPath }` );
			img.setAttribute( 'style', '' );
			loading.setAttribute( 'style', 'display:none;' );
		} );
	} );
	}
  })
```

Dernière remarque, nous utilisons le même `BrowserWindow` et de ce fait le même preloader. Il faut donc toujours s'assurer que nos objets sont bien récupérés.

Voilà l'application refonctionne et nous respectons les consignes de sécurité.

