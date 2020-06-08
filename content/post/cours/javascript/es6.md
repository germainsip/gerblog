---
title: "Es6"
date: 2020-06-06T12:00:10+02:00
draft: true
description: 'Les bases de la programmation avec javascript ES6'
categories:
  - javascript
tags:
  - javascript
  - ES6
  - bases
lead: 'Les bases de la programmation avec javascript ES6'
comments: false
authorbox: true
toc: true
mathjax: true
---

## Les bases du javascript

Nous allons traiter ici du javascript ES6. Je vous passe l'historique, sachez juste que le javascript moderne s'inspire des langages Java et C mais reste assez différent dans son code.

### Les variables

En javascript ES6 on déclare une variable avec le mot clé `let` (comme dans d'autres langages comme le Swift par exemple). Javascript va identifier pour nous le type de cette variable:


```javascript
let age = 12
let nom = "Paul"

console.log("Le type de age est " + typeof(age))
console.log("Le type de nom est " + typeof(nom))
```

> vous pouvez exécuter ce code dans Visual Studio Code directement si vous installez l'extension **Code Runner**

Le résultat est le suivant:

```cmd
Le type de age est number
Le type de nom est string
```

Dans ce code le `=` est appelé **opérateur** d'affectation (ou assignation). Il permet de remplir la variable. Ensuite cette variable est typée selon la valeur qui lui est affectée.

L'opérateur `=` va aussi nous servir à modifier le contenu de la variable.

```javascript
let a = 6
let b = 12

console.log("Au début a vaut "+a+" et b vaut " + b )

a=b

console.log("Ensuite a vaut "+a+" et b vaut " + b )
```

Essayez ce code et vous verrez que `a` prend la valeur contenu dans `b`

On pourra aussi faire des opérations comme dans tous les autres langages:


```javascript
let a = 6
let b = 12
console.log("a= "+a)
console.log("b= "+b)
console.log("============")
// addition
let addition = a+b
console.log("a+b= "+addition)

//soustraction
let soustraction = a-b
console.log("a-b= " + soustraction)

//multiplication
let multiplication = a*b
console.log("axb= " + multiplication)

//division
let division = a/b
console.log("a/b= " + division)

//On peut aussi faire l'affectation
//et l'opération en même temps
a += b
console.log("a+b= " + a)

//Enfin on peut incrémenter et décrémenter
a++
b--
console.log("a++ " + a)
console.log("b-- " + b)
```

Donne:

```cmd
a= 6
b= 12
============
a+b= 18
a-b= -6
axb= 72
a/b= 0.5
a+b= 18
a++ 19
b-- 11
```

En fait, il existe 3 mots clés pour définir une "variable" `let`, `const` et `var`.

`let` permet de déclarer des variables qui pourront être utilisées dans un bloc. La variable déclarée avec `let`est uniquement disponible dans le bloc qui contient la déclaration.

C'est à dire que la variable déclarée avec `let`n'a qu'une portée restreinte. Si l'on veut que notre variable ait une porté plus étendue il faudra utiliser `var`. Enfin, si la variable ne doit pas être redéfinie ou modifiée on utilise une constante `const`.

### Les opérateurs

Le `+`se comporte différemment s'il s'agit d'un `number` ou une `String`

```javascript
3 + 4 // 7
"3" + "4" // "34"
"Salut " + "Paul" // "Salut Paul"
```

L'addition de chaînes de caractères s'appelle la **concaténation**.

En ce qui concerne les opérations de comparaison, ce sont les mêmes que dans les autres langages. Il faut juste faire une distinction entre `==` et `===`.
`==` vérifie une équivalence alors que `===` vérifie une égalité.

```javascript
"24" == 24  // vrai
"24" === 24 // faux car les deux ne sont pas
            // du même type
```

### Les structures de contrôle

Nous avons à disposition les structures conditionnelles avec `if` et `else` ; lesquels peuvent être chaînés si nécessaire :

```javascript
var nom = "des chatons";
if (nom == "des chiots") {
  nom += " !";
} else if (nom == "des chatons") {
  nom += " !!";
} else {
  nom = " !" + nom;
}
nom == "des chatons !!"
```

JavaScript dispose également de boucles `while` et `do-while`. Les premières permettent de former des boucles basiques ; les secondes permettent de construire des boucles qui seront exécutées au moins une fois :

```javascript
while (true) {
  // une boucle infinie !
}

do {
  var input = getInput();
} while (inputNonValide(input))
```

Les boucles `for` en JavaScript sont les mêmes qu'en C et en Java : elles permettent de fournir les informations de contrôle de la boucle en une seule ligne.

```javascript
for (var i = 0; i < 5; i++) {
  // Sera exécutée cinq fois
}
```

JavaScript permet également d'utiliser deux autres types de boucles : `for...of` :

```javascript
for (let value of array) {
  // utiliser des instructions
  // pour manipuler la valeur value
}
```

et `for...in` :

```javascript
for (let propriété in objet) {
  // utiliser des instructions
  // pour manipuler la propriété
}
```

Les opérateurs `&&` et `||` utilisent une logique de court-circuit, ce qui signifie qu'ils exécuteront leur second opérande ou non selon la valeur du premier. C'est très utile pour vérifier qu'un objet n'est pas égal à `null` avant d'essayer d'accéder à ses attributs :

```javascript
var nom = o && o.getNom();
```

Ou pour définir des valeurs par défaut :

```javascript
var nom = autreNom || "nomParDéfaut";
```

De la même façon, le OU peut être utilisé pour mettre en cache des valeurs :

```javascript
var nom = nomEnCache || (nomEnCache = getNom());
```

JavaScript propose également un opérateur ternaire pour les assignations conditionnelles en une ligne :

```javascript
var permis = (age > 18) ? "oui" : "non";
```

L'instruction `switch` peut être utilisée pour différentes branches de code basées sur un nombre ou une chaîne :

```javascript
switch (action) {
  case 'dessiner':
    dessine();
    break;
  case 'manger':
    mange();
    break;
  default:
    neRienFaire();
}
```

Si vous n'ajoutez pas d'instruction `break`, l'exécution va se poursuivre au niveau suivant. C'est rarement ce qui est désiré, en fait ça vaut même la peine de préciser dans un commentaire si la poursuite au cas suivant est délibérée pour aider au débogage :

```javascript
switch (a) {
  case 1: // identique au cas 2
  case 2:
    mange();
    break;
  default:
    nerienfaire();
}
```

La clause `default` est optionnelle. Vous pouvez placer des expressions à la fois dans la partie `switch` et dans les cas à gérer si vous voulez ; les comparaisons entre les deux se font comme si on avait utilisé l'opérateur `===` :

```javascript
switch (1 + 3):
  case 2 + 2:
    yay();
    break;
  default:
    nArriveJamais();
}
```
