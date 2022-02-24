---
title: "Les bases de Vue"
date: 2022-02-24T10:33:02+01:00
draft: true
description: Pour bien commencer avec vue 3
categories:
  - Vue
tags:
  - javascript
  - Vue
thumbnail: img/vue/vueintro.png
lead: Pour bien commencer avec vue 3
comments: false
authorbox: true
toc: true
mathjax: true
---

Vue est un framework Javascript destiné à la création d'interface utilisateur.
Vue est conçu pour être "progressif". C'est à dire qu'il conviendra à plusieurs conceptions ou besoins différents comme vous pourrez le lire dans la [documentation](https://vuejs.org/guide/extras/ways-of-using-vue.html)

## Commencer avec Vue

### Le CDN

En allant sur la doc de Vue vous trouverez le [CDN](https://vuejs.org/guide/quick-start.html#without-build-tools) permettant d'intégrer directement vue à une page html.

Il suffit d'ajouter ce script à la page.

Par exemple:

```html
<!DOCTYPE html>
<html lang="fr">
  <head>
    <meta charset="UTF-8" />
    <title>Vue avec le CDN</title>
    <!-- Import Styles -->
    <link rel="stylesheet" href="./assets/styles.css" />
    <!-- Import Vue.js -->
    <script src="https://unpkg.com/vue@3"></script>
  </head>
  <body>
    <div id="app">
      <h1>Les produits seront ici</h1>
    </div>

    <!-- Import Js -->
    <script src="./main.js"></script>
  </body>
</html>
```

vous pouvez déjà cloner ce dépot pour partir de ce niveau.

`git clone https://github.com/germainsip/vuecdn.git && cd vuecdn.git`

puis `git checkout 1-intro-debut`

Nous avons déjà un style.css un main.js et des images dans notre projet:

```zsh
vuecdn
├── assets
│   ├── images
│   │   ├── socks_blue.jpg
│   │   └── socks_green.jpg
│   └── styles.css
├── index.html
└── main.js
```

Ouvrons le `main.js` et modifions le pour créer une application vue:

```js
const app = Vue.createApp({
    data() {
        return {
            product: 'Chaussettes'

        }
    }
})
```

Nous avons ici à l'aide de la méthode de vue `createApp()` créé une application vue. Cette méthode prend en paramètre une fonction `data()`
qui renvoie un objet `{product: 'Chaussettes'}`.

Pour le moment, notre index.html ne connait pas l'appli. Il faut a monter à l'emplacement de notre choix:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Vue avec le CDN</title>
    <!-- Import Styles -->
    <link rel="stylesheet" href="./assets/styles.css" />
    <!-- Import Vue.js -->
    <script src="https://unpkg.com/vue@3.0.11/dist/vue.global.js"></script>
  </head>
  <body>
    <div id="app">
      <h1>Les {{product}} seront ici</h1>
    </div>

    <!-- Import App -->
    <script src="./main.js"></script>

    <!--Mount App-->
    <script>
        const mountedApp = app.mount('#app')
    </script>
  </body>
</html>
```

A ce stade, si vous ouvrez le fichier `index.html` (_via live server_) vous devriez avoir:

![firstvue](/img/vue/firstvue.png)

Vous voyez que les "moustaches" `{{product}}` ont été remplacées par la valeur contenue dans l'objet. Vous pouvez également mettre des expressions javascript dans ces "moustaches".

Par exemple, `{{product.toUpperCase()}}` mettra Chaussettes en majuscule.

En guise d'application, ajoutez sous titre (`<h2>`) avec une description dans l'application vue.

![secvue](/img/vue/secvue.png)

Vous aurez la solution en accédant à la branche `1-intro-fin`.

## Le binding

Pour ceux qui voudraient commencer à partir du même point. Passez sur la branche `2-binding-debut`.

J'ai ajouté une balise dans notre html:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Vue avec le CDN</title>
    <!-- Import Styles -->
    <link rel="stylesheet" href="./assets/styles.css" />
    <!-- Import Vue.js -->
    <script src="https://unpkg.com/vue@next"></script>
  </head>
  <body>
    <div id="app">
        <div class="nav-bar"></div>
        
        <div class="product-display">
          <div class="product-container">
            <div class="product-image">
              <!-- image goes here -->
            </div>
            <div class="product-info">
              <h1>{{ product }}</h1>
            </div>
          </div>
        </div>
      </div>

    <!-- Import App -->
    <script src="./main.js"></script>

    <!--Mount App-->
    <script>
        const mountedApp = app.mount('#app')
    </script>
  </body>
</html>
```

Nous avons déjà des images dans le dossier `assets` ajoutons cet attribut dans notre `app`

```js
const app = Vue.createApp({
    data() {
        return {
            product: 'Chaussettes',
            image: './assets/images/socks_blue.jpg'
        }
    }
})
```

Afin d'ajouter cette valeur à une balise `<img>` nous allons utiliser l'attribut `v-bind:` dans `index.html`:

```html
<!--...-->
 <div id="app">
        <div class="nav-bar"></div>
        
        <div class="product-display">
          <div class="product-container">
            <div class="product-image">
              <img  v-bind:src="image" alt="chaussettes">
            </div>
            <div class="product-info">
              <h1>{{ product }}</h1>
            </div>
          </div>
        </div>
 </div>
<!--...-->
```

Ce qui nous donne ça:

![img-bind](/img/vue/img-bind.png)

Nous avons ici connecté l'attribut `src` de `<img>` à la propriété image de notre application.

On peut raccourcir l'écriture en écrivant uniquement `:src`.

En guise d'exercice, ajoutez une propriété url dans l'application et bindez la dans un lien (`<a href>`).

solution à la branche `2-binding-fin`.

## Le rendu conditionnel

Avec Vue, nous pouvons faire du "rendue conditionnel". C'est à dire afficher quelque chose en fonction d'une condition.

Par exmple, nous pourrions afficher si la chaussette choisie est en stock ou non.

Ajoutons les deux propositions dans l'html:

```html
 <div class="product-display">
          <div class="product-container">
            <div class="product-image">
              <img  v-bind:src="image" alt="chaussettes">
            </div>
            <div class="product-info">
              <h1>{{ product }}</h1>
              <p>En stock</p>
              <p>Rupture</p>
            </div>
          </div>
        </div>
```

Côté javascript, ajoutons un booléen:

```js
const app = Vue.createApp({
    data() {
        return {
            product: 'Chaussettes',
            image: './assets/images/socks_blue.jpg',
            inStock: true
        }
    }
})
```

Pour Vue, nous avons une directive qui permet de faire ce que nous avons envie de faire. Il s'agit de `v-if` et `v-else`. Modifions donc `index.html`.

```html
<div class="product-info">
  <h1>{{ product }}</h1>
  <p v-if="inStock">En stock</p>
  <p v-else>Rupture</p>
</div>
```

Dès l'ors si vous changez la valeur de `inStock` l'affichage sera mis à jour avec respectivement "En stock" ou "Rupture".

> Rq: `v-else` n'est pas obligatoire si vous n'avez pas de valeur alternative à afficher.

Dans le cas où il ne s'agit que de faire apparaitre ou disparaitre un élément nous pouvons utiliser `v-show` qui agira sur la visibilité d'un élément (`style= "display: none"`).

Nous pouvons faire plus élaboré. Notre texte pourrait changer selon certaines valeurs.

Changeons la propriété dans `mains.js`

```js
const app = Vue.createApp({
  data() {
    return {
      product: 'Chaussettes',
      image: './assets/images/socks_blue.jpg',
      inventory : 100
    }
  }
})
```

Et dans `index.html` nous pouvons ajouter une étape:

```js
<div class="product-info">
  <h1>{{ product }}</h1>
  <p v-if="inventory > 10">En stock</p>
  <p v-else-if="inventory <= 10 && inventory > 0">Petites quantités</p>
  <p v-else>Rupture</p>
</div>
```

## Afficher une liste d'éléments

> Nous en sommes à la branche `4-list-debut`

Dans `main.js` nous avons la propriété `details: ['50% coton', '30% laine', '20% polyester']` qui est une liste de valeurs.

Afin de les afficher, nous allons utiliser une autre directive `v-for`

```html
<div class="product-info">
    <h1>{{ product }}</h1>
    <p v-if="inStock">En stock</p>
    <p v-else>Rupture</p>
    <p>Détail:</p>
    <ul>
      <li v-for="detail in details">{{detail}}</li>
    </ul>
</div>
```

Nous pouvons également utiliser une liste d'objets:

```js
//main.js
const app = Vue.createApp({
    data() {
        return {
            product: 'Chaussette',
            image: './assets/images/socks_blue.jpg',
            inStock: true,
            details: ['50% coton', '30% laine', '20% polyester'],
            variants: [
                {id: 1234 , color: 'bleu'},
                {id: 3245 , color: 'vert'}
            ]
        }
    }
})
```

```html
<!--index.html-->
<div class="product-info">
  <h1>{{ product }}</h1>
  <p v-if="inStock">En stock</p>
  <p v-else>Rupture</p>
  <p>Détail:</p>
  <ul>
    <li v-for="detail in details">{{detail}}</li>
  </ul>
  <p>Existe en:</p>
  <div v-for="variant in variants" :key="variant.id">{{variant.color}}</div>
</div>
```

![list](/img/vue/list.png)

>Rq: `:key="variant.id"` est fortement conseillé car il donne une clé unique à chaque élément du DOM.

## Les évènements

Partons de la branche `5-action-debut` [ici](https://github.com/germainsip/vuecdn/tree/5-action-debut)

Comme vous pouvez le constater, j'ai ajouté un bouton et un compteur.

![action](/img/vue/action.png)

Afin d'écouter l'évènement `click` et d'ajouter une paire de chaussette au panier nous allons utiliser la directive `v-on` et lui affecter une action.

```html
<div id="app">
        <div class="nav-bar"></div>
        
        <div class="cart">Panier({{ cart }})</div>

        <div class="product-display">
          <div class="product-container">
            <div class="product-image">
              <img  v-bind:src="image" alt="chaussettes">
            </div>
            <div class="product-info">
              <h1>{{ product }}</h1>
              <p v-if="inStock">En stock</p>
              <p v-else>Rupture</p>
              <p>Détail:</p>
              <ul>
                <li v-for="detail in details">{{detail}}</li>
              </ul>
              <p>Existe en:</p>
              <div v-for="variant in variants" :key="variant.id">{{variant.color}}</div>
              <!-- ICI -->
              <button v-on:click="cart += 1" class="button">Ajouter au panier</button>
            </div>
          </div>
        </div>
      </div>
```

Maintenant, à chaque clique, la propriété `cart` et augmenté de 1. Vue ne va alors rafraichir que la balise `<div class="cart">Panier({{ cart }})</div>`.

Ca fonctionne mais ce serait mieux d'avoir un fonction (méthode) pour ça.

Ajoutons la méthode `addToCart()` dans `main.js`:

```js
const app = Vue.createApp({
    data() {
        return {
            cart:0,
            product: 'Chaussette',
            image: './assets/images/socks_blue.jpg',
            inStock: true,
            details: ['50% coton', '30% laine', '20% polyester'],
            variants: [
                {id: 1234 , color: 'bleu'},
                {id: 3245 , color: 'vert'}
            ]
        }
    },
    methods: {
        addToCart() {
            this.cart += 1
        }
    }
})
```

et remplaçons dans `index.html`

```html
<button v-on:click="addToCart" class="button">Ajouter au panier<button>
```

De même que pour le `v-bind` nous avons un raccourcit. Ici `v-on:click` peut être remplacé par `@click`.

Nous avons vu un évènement, mais il y en a d'autres. Par exemple, `on hover` quand on survole un éléments. Mettons en place le changement d'image au survole de la couleur.

Commençons par ajouter les urls dans la liste `variants`:

```js
variants: [
            {id: 1234 , color: 'bleu', image: './assets/images/socks_blue.jpg'},
            {id: 3245 , color: 'vert', image: './assets/images/socks_green.jpg'}
            ]
```

Ajoutons également la méthode `changeImg()`:

```js
changeImg(variantImage) {
            this.image = variantImage
        }
```

et enfin ajoutons l'évènement à nos balises:

```html
<div v-for="variant in variants" :key="variant.id" @mouseOver="changeImg(variant.image)">{{variant.color}}</div>
```

Et voilà:

![over](/img/vue/over.gif)

Pour améliorer notre panier, vous pouvez ajouter un bouton pour enlever un élément du panier. (solution à `5-action-fin`)

## Le binding de classe et de style

Plutôt que d'avoir bleu et vert, nous allons mettre des pastilles de couleurs.
Commençons par ajouter une classe à notre balise:

```html
<div class="color-circle" v-for="variant in variants" :key="variant.id" @mouseOver="changeImg(variant.image)">{{variant.color}}</div>
```

cette classe est déjà écrite dans le `style.css`:

```css
.color-circle {
  width: 50px;
  height: 50px;
  margin-top: 8px;
  border: 2px solid #d8d8d8;
  border-radius: 50%;
}
```

Remplaçons, maintenant, le texte par un background grâce à la directive `:style`.

```html
<div 
class="color-circle" 
:style="{backgroundColor: variant.back}" 
v-for="variant in variants" :key="variant.id" 
@mouseOver="changeImg(variant.image)"></div>
```

Et dans `main.js` ajoutons nos valeurs dans `variants`

```js
variants: [
            {id: 1234 , color: 'bleu', back: '#38475F', image: './assets/images/socks_blue.jpg'},
            {id: 3245 , color: 'vert', back: '#4F9869', image: './assets/images/socks_green.jpg'}
            ]
```

Dorénavant nos couleurs apparaissent sous forme de pastilles.

Nous pouvons aussi agir sur d'autres attributs. Par exemple, désactivons les boutons si le stock est en rupture.
Pour nos deux boutons cela donnerait:

```html
<button 
 class="button"
 :class="{disabledButton: !inStock}"
 :disabled="!inStock" 
 @click="addToCart" >
  Ajouter au panier
</button>
<button 
 class="button"
 :class="{disabledButton: !inStock}"
 :disabled="!inStock" 
 @click="remFromCart" >
  Enlever du panier
</button>
```

Ici nous faisons deux choses. D'une part, si `inStock=false` les boutons passent à `disabled` et la classe passe à `disabledButton` décrite dans le css par:

```css
.disabledButton {
  background-color: #d8d8d8;
  cursor: not-allowed;
}
```

## Propriétés calculées

Nous pouvons avoir des propriétés calculées (compilé). C'est à dire, qu'elles nécessites un traitement. Par exemple, ajoutons des quantités dans notre liste et gérons le stock ainsi que l'image par calcul.

Modifions déjà l'html:

```html
<div id="app">
      <div class="nav-bar"></div>

      <div class="cart">Panier({{ cart }})</div>

      <div class="product-display">
        <div class="product-container">
          <div class="product-image">
            <img v-bind:src="image" alt="chaussettes" />
          </div>
          <div class="product-info">
            <h1>{{ product }}</h1>
            <p v-if="inStock">En stock</p>
            <p v-else>Rupture</p>
            <p>Détail:</p>
            <ul>
              <li v-for="detail in details">{{detail}}</li>
            </ul>
            <p>Existe en:</p>
            <div 
              v-for="(variant, index) in variants" 
              :key="variant.id" 
              @mouseover="updateVariant(index)" 
              class="color-circle" 
              :style="{ backgroundColor: variant.back }">
            </div>
            <button
              class="button"
              :class="{disabledButton: !inStock}"
              :disabled="!inStock"
              @click="addToCart"
            >
              Ajouter au panier
            </button>
            <button
              class="button"
              :class="{disabledButton: !inStock}"
              :disabled="!inStock"
              @click="remFromCart"
            >
              Enlever du panier
            </button>
          </div>
        </div>
      </div>
    </div>
```

La méthode `changeImg` est remplacée par `updateVariant` et le `v-for` prend `(variant,index)` afin d'avoir l'index de l'élément dans le tableau.

Côté javascript, on va faire plus de modifications. D'abords, ajoutons la méthode `updateVariant` et modifions les propriétés pour avoir `selectedVariant` mais pas `image` et `inStock` qui vont être calculées.

```js
const app = Vue.createApp({
    data() {
        return {
            cart:0,
            product: 'Chaussette',
            selectedVariant: 0,
            details: ['50% coton', '30% laine', '20% polyester'],
            variants: [
                {id: 1234 , color: 'bleu', back: '#38475F', image: './assets/images/socks_blue.jpg',quantity: 50},
                {id: 3245 , color: 'vert', back: '#4F9869', image: './assets/images/socks_green.jpg',quantity:0}
            ]
        }
    },
    methods: {
        addToCart() {
            this.cart += 1
        },
        remFromCart() {
            if(this.cart > 0) {
                this.cart -= 1
            }
            
        }, 
        updateVariant(index) {
            this.selectedVariant = index
        }
    }
})

```

Retrouvons nos propriétés dans `computed`

```js
const app = Vue.createApp({
    data() {
        return {
            cart:0,
            product: 'Chaussette',
            selectedVariant: 0,
            details: ['50% coton', '30% laine', '20% polyester'],
            variants: [
                {id: 1234 , color: 'bleu', back: '#38475F', image: './assets/images/socks_blue.jpg',quantity: 50},
                {id: 3245 , color: 'vert', back: '#4F9869', image: './assets/images/socks_green.jpg',quantity:0}
            ]
        }
    },
    methods: {
        addToCart() {
            this.cart += 1
        },
        remFromCart() {
            if(this.cart > 0) {
                this.cart -= 1
            }
            
        }, 
        updateVariant(index) {
            this.selectedVariant = index
        }
    },
    computed: {
        image() {
            return this.variants[this.selectedVariant].image
        },
        inStock() {
            return this.variants[this.selectedVariant].quantity
        }
    }
})
```

Cette fois, quand on survole les pastilles, l'image, le stock et les boutons sont mis à jour.

## Les composants et les props

Les frameworks comme vue, react, angular... sont conçu pour fonctionner par composants. L'idée est de séparer la page en éléments indépendants et réutilisables.

Nous allons découper notre application en composants.
Tout d'abord, créez un dossier `components` et ajoutez y un fichier `ProductDisplay.js`

Dans ce fichier nous allons créer la partie qui affiche le produit. Déplaçons toute la partie display:

```js
// ProductDisplay.js
app.component('product-display', {
    template:
    /*html*/
    `<div class="product-display">
        <div class="product-container">
          <div class="product-image">
            <img v-bind:src="image" alt="chaussettes" />
          </div>
          <div class="product-info">
            <h1>{{ product }}</h1>
            <p v-if="inStock">En stock</p>
            <p v-else>Rupture</p>
            <p>Détail:</p>
            <ul>
              <li v-for="detail in details">{{detail}}</li>
            </ul>
            <p>Existe en:</p>
            <div 
              v-for="(variant, index) in variants" 
              :key="variant.id" 
              @mouseover="updateVariant(index)" 
              class="color-circle" 
              :style="{ backgroundColor: variant.back }">
            </div>
            <button
              class="button"
              :class="{disabledButton: !inStock}"
              :disabled="!inStock"
              @click="addToCart"
            >
              Ajouter au panier
            </button>
            <button
              class="button"
              :class="{disabledButton: !inStock}"
              :disabled="!inStock"
              @click="remFromCart"
            >
              Enlever du panier
            </button>
          </div>
        </div>
      </div>`,
      data() {
        return {
            product: 'Chaussette',
            selectedVariant: 0,
            details: ['50% coton', '30% laine', '20% polyester'],
            variants: [
                {id: 1234 , color: 'bleu', back: '#38475F', image: './assets/images/socks_blue.jpg',quantity: 50},
                {id: 3245 , color: 'vert', back: '#4F9869', image: './assets/images/socks_green.jpg',quantity:0}
            ]
        }
    },
    methods: {
        addToCart() {
            this.cart += 1
        },
        remFromCart() {
            if(this.cart > 0) {
                this.cart -= 1
            }
            
        }, 
        updateVariant(index) {
            this.selectedVariant = index
        }
    },
    computed: {
        image() {
            return this.variants[this.selectedVariant].image
        },
        inStock() {
            return this.variants[this.selectedVariant].quantity
        }
    }
})
```

Dans `index.html` nous pouvons maintenant importer notre composant et utiliser la balise qui porte sont nom.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Vue avec le CDN</title>
    <!-- Import Styles -->
    <link rel="stylesheet" href="./assets/styles.css" />
    <!-- Import Vue.js -->
    <script src="https://unpkg.com/vue@next"></script>
  </head>
  <body>
    <div id="app">
      <div class="nav-bar"></div>
      <div class="cart">Panier({{ cart }})</div>
      <product-display></product-display>
    </div>
    <!-- Import App -->
    <script src="./main.js"></script>

    <!-- Import ProductDisplay -->
    <script src="./components/ProductDisplay.js"></script>

    <!--Mount App-->
    <script>
      const mountedApp = app.mount("#app");
    </script>
  </body>
</html>
```

Nous pourrions ajouter autant de versions indépendantes de ce composant.

### Les props

Notre composant doit pouvoir communiquer avec le reste de l'application. Les `props` sont là pour ça.

Pour illustrer cette `prop` ajoutons une propriété `premium` au `main.js`. Cette propriété dit si l'usager a un compte premium ou non. Dans le composant nous afficherons les frais de port (gratuit pour premium, 5 € pour les autres).

Dans `main.js` ça donne:

```js
const app = Vue.createApp({
    data() {
        return {
            cart:0,
            premium: true
        }
    },
    methods: {}
})
```

Et dans le composant `ProductDisplay.js`:

- On ajoute la prop en entré en spécifiant son type.
- On ajoute une balise `<p>` avec les frais de ports qui dépendent de la méthode `shipping`.
- Que nous ajoutons également.

```js
app.component("product-display", {
  props: {
    premium: {
      type: Boolean,
      required: true,
    },
  },
  template:
    /*html*/
    `<div class="product-display">
        <div class="product-container">
          <div class="product-image">
            <img v-bind:src="image" alt="chaussettes" />
          </div>
          <div class="product-info">
            <h1>{{ product }}</h1>
            <p v-if="inStock">En stock</p>
            <p v-else>Rupture</p>
            <p>Frais de port: {{shipping}}</p>
            <p>Détail:</p>
            <ul>
              <li v-for="detail in details">{{detail}}</li>
            </ul>
            <p>Existe en:</p>
            <div 
              v-for="(variant, index) in variants" 
              :key="variant.id" 
              @mouseover="updateVariant(index)" 
              class="color-circle" 
              :style="{ backgroundColor: variant.back }">
            </div>
            <button
              class="button"
              :class="{disabledButton: !inStock}"
              :disabled="!inStock"
              @click="addToCart"
            >
              Ajouter au panier
            </button>
            <button
              class="button"
              :class="{disabledButton: !inStock}"
              :disabled="!inStock"
              @click="remFromCart"
            >
              Enlever du panier
            </button>
          </div>
        </div>
      </div>`,
  data() {
    return {
      product: "Chaussette",
      selectedVariant: 0,
      details: ["50% coton", "30% laine", "20% polyester"],
      variants: [
        {
          id: 1234,
          color: "bleu",
          back: "#38475F",
          image: "./assets/images/socks_blue.jpg",
          quantity: 50,
        },
        {
          id: 3245,
          color: "vert",
          back: "#4F9869",
          image: "./assets/images/socks_green.jpg",
          quantity: 0,
        },
      ],
    };
  },
  methods: {
    addToCart() {
      this.cart += 1;
    },
    remFromCart() {
      if (this.cart > 0) {
        this.cart -= 1;
      }
    },
    updateVariant(index) {
      this.selectedVariant = index;
    },
  },
  computed: {
    image() {
      return this.variants[this.selectedVariant].image;
    },
    inStock() {
      return this.variants[this.selectedVariant].quantity;
    },
    shipping() {
      if (this.premium) {
        return "Gratuit";
      }
      return "5 €";
    },
  },
});
```

## Communiquer en dehors du composant.

Nous avons factorisé notre code en composant, mais les boutons ne fonctionnent plus.
Comment réparer ça?

Nous devons faire sortir l'évènement du composant avec un `$emit`

```js
//ProductDisplay.js
...
methods: {
    addToCart() {
      this.$emit("add-to-cart")
    },
    remFromCart() {
      this.$emit("rem-from-cart")
    },
    updateVariant(index) {
      this.selectedVariant = index;
    },
  },
...
```

Quand les boutons seront cliqués, ils enverront un évènement (resp `add-to-cart` et `rem-from-cart`). Il faut les écouter dans `index.html`

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Vue avec le CDN</title>
    <!-- Import Styles -->
    <link rel="stylesheet" href="./assets/styles.css" />
    <!-- Import Vue.js -->
    <script src="https://unpkg.com/vue@next"></script>
  </head>
  <body>
    <div id="app">
      <div class="nav-bar"></div>

      <div class="cart">Panier({{ cart }})</div>
      <product-display :premium="premium" @add-to-cart="addToCart" @rem-from-cart="remFromCart"></product-display>
    </div>

    <!-- Import App -->
    <script src="./main.js"></script>

    <!-- Import Import ProductDisplay -->
    <script src="./components/ProductDisplay.js"></script>

    <!--Mount App-->
    <script>
      const mountedApp = app.mount("#app");
    </script>
  </body>
</html>
```

Et mettons à jour les méthodes dans `main.js`

```js
const app = Vue.createApp({
    data() {
        return {
            cart: 0,
            premium: true
        }
    },
    methods: {
        addToCart(){
            this.cart++
        },
        remFromCart(){
            if(this.cart>0){
                this.cart--
            }
        }
    }
})
```

<!-- aller plus loin avec https://vuejs.org/guide/components/events.html -->

## Petit clean-up

En réalité, ce serait beaucoup mieux pour le panier d'avoir un tableau qui liste les éléments achetés.

```js
//main.js
const app = Vue.createApp({
    data() {
        return {
            cart: [],
            premium: true
        }
    },
    methods: {
        addToCart(id){
            this.cart.push(id)
        },
        remFromCart(id){
            // TODO
        }
    }
})
```

Modifions également `ProductDisplay.js` 

```js
...
methods: {
    addToCart() {
      this.$emit("add-to-cart",this.variants[this.selectedVariant].id)
    },
    remFromCart() {
      this.$emit("rem-from-cart")
    },
    updateVariant(index) {
      this.selectedVariant = index;
    },
  },
...
```

Pensez à passer la quantité de chaussettes vertes à 20 et testez.

Le panier devrait afficher la liste des `id`.

Corrigez en modifiant `<div class="cart">Panier({{ cart }})</div>` en `<div class="cart">Panier({{ cart.length }})</div>`

**Exercice:** complétez `remFromCart(id)` dans `main.js` pour que le bouton "Enlever du panier" refonctionne. Et faites en sorte que le stock soit actualisé.

**Correction:** branche `9-emit-fin`

## Les formulaires

Depuis le début, nous avons parlé de "binder" (lier) dans le sens donné vers la vue.
Pour un formulaire, nous allons lier "binder" dans les deux sens.
Et pour ça nous utiliserons `v-model`.

Créons un nouveau composant `components/ReviewForm.js`:

```js
app.component("review-form", {
  template:
    /*html*/
    `<form class="review-form">
    <h3>Laissez un avis</h3>
    <label for="name">Nom:</label>
    <input id="name" type="text" />

    <label for="review">Avis:</label>
    <textarea id="review"></textarea>

    <label for="rating">Note:</label>
    <select id="rating">
    <option >5</option>
    <option >4</option>
    <option >3</option>
    <option >2</option>
    <option >1</option>
    </select>

    <input type="submit" class="button" value="Envoyer"/>
    </form>`,
  data() {
    return {
      name: "",
      review: "",
      rating: null,
    };
  },
});

```

Un simple formulaire avec 3 valeurs. Nous les retrouvons dans les `data`.
Nous devons les relier ensembles avec `v-model`:

```html
<form class="review-form">
    <h3>Laissez un avis</h3>
    <label for="name">Nom:</label>
    <input id="name" type="text" v-model="name" />

    <label for="review">Avis:</label>
    <textarea id="review" v-model="review"></textarea>

    <label for="rating">Note:</label>
    <select id="rating" v-model.number="rating">
    <option >5</option>
    <option >4</option>
    <option >3</option>
    <option >2</option>
    <option >1</option>
    </select>

    <input type="submit" class="button" value="Envoyer"/>
    </form>
```

Pour envoyer nos données, nous allons créer une méthode:

```js
methods: {
      onSubmit() {
          let productReview = {
              name: this.name,
              review: this.review,
              rating: this.rating
          }
          this.$emit('review-submitted', productReview);

          this.name=''
          this.review=''
          this.rating=null
      }
  }
```

et ajouter un écouteur à `<form>`

```html
<form class="review-form" @submit-prevent="onSubmit">
```

Côté `ProductDisplay.js` ajoutons simplement la balise `<review-form>`:

```html
...
template:
    /*html*/
    `<div class="product-display">
        <div class="product-container">
          <div class="product-image">
            <img v-bind:src="image" alt="chaussettes" />
          </div>
          <div class="product-info">
            <h1>{{ product }}</h1>
            <p v-if="inStock">En stock</p>
            <p v-else>Rupture</p>
            <p>Frais de port: {{shipping}}</p>
            <p>Détail:</p>
            <ul>
              <li v-for="detail in details">{{detail}}</li>
            </ul>
            <p>Existe en:</p>
            <div 
              v-for="(variant, index) in variants" 
              :key="variant.id" 
              @mouseover="updateVariant(index)" 
              class="color-circle" 
              :style="{ backgroundColor: variant.back }">
            </div>
            <button
            class="button"
            :class="{disabledButton: !inStock}"
            :disabled="!inStock"
            @click="addToCart"
          >
            Ajouter au panier
          </button>
          <button
            class="button"
            @click="remFromCart"
          >
            Enlever du panier
          </button>
          </div>
          <!--ICI-->
          <review-form></review-form>

        </div>
      </div>`,
...
```

Il va falloir récupérer l'évènement et l'ajouter à notre vue.

l'écouteur:

```html
    <review-form @review-submitted="addReview"></review-form>
```

Ajoutons la méthode et le tableau qui va receptionner les avis:

```js
//ProductDisplay.js
...
data() {
    return {
      product: "Chaussette",
      selectedVariant: 0,
      details: ["50% coton", "30% laine", "20% polyester"],
      variants: [
        {
          id: 1234,
          color: "bleu",
          back: "#38475F",
          image: "./assets/images/socks_blue.jpg",
          quantity: 50,
        },
        {
          id: 3245,
          color: "vert",
          back: "#4F9869",
          image: "./assets/images/socks_green.jpg",
          quantity: 20,
        },
      ],
      reviews: [],
    };
  },
  methods: {
    addToCart() {
        this.variants[this.selectedVariant].quantity--
      this.$emit("add-to-cart",this.variants[this.selectedVariant].id)
    },
    remFromCart() {
        this.variants[this.selectedVariant].quantity++

      this.$emit("rem-from-cart",this.variants[this.selectedVariant].id)
    },
    updateVariant(index) {
      this.selectedVariant = index;
    },
    addReview(review) {
      this.reviews.push(review)
    }
  },
  ...
```

Pour afficher les avis nous allons créer un composant `ReviewList.js`

```js
app.component('review-list',
{
    props: {
        reviews: {
            type: Array,
            required: true
        }
    },
    template:
    /*html*/
    `<div class="review-container">
    <h3>Avis:</h3>
    <ul>
    <li v-for="(review, index) in reviews" :key="index">
    {{review.name}} a donné {{ '&#9733'.repeat(review.rating) }} 
    <br />
    "{{review.review}}"
    </li>
    </ul>
    </div>`
})
```

Enfin ajoutons cette liste au dessus du formulaire dans `ProductDisplay.js` et sans oublier d'importer notre composant dans `index.html`

```js
<review-list :reviews="reviews"></review-list>
```

## Améliorations

- Ajoutez une condition dans la balise `<review-list>` pour que les avis ne s'affichent que lorsqu'il y en a.
- Ajoutez des conditions dans la méthode `onSubmit` pour vérifier que le tous les champs ont bien été remplis.

[La correction](https://github.com/germainsip/vuecdn/tree/10-form-fin)