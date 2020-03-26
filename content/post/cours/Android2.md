---
title: TextView et scroll
date: 2020-03-26T09:37:44.000Z
draft: false
description: vues texte et défilements en android
categories:
  - Android
tags:
  - Android
thumbnail: img/androidbase.png
lead: Vues texte et défilements en android (en cours de rédaction)
comments: false
authorbox: true
toc: true
---
## Les `TextView`

{{< youtube D5EjqPIbFIU >}}

---
---
L'application Texte défilant illustre le `ScrollView` composant d'interface utilisateur. `ScrollView`est un `ViewGroup` qui dans cet exemple contient un `TextView`. Il affiche une longue page de texte (dans ce cas, une critique d'album musical) que l'utilisateur peut faire défiler verticalement pour lire en faisant glisser vers le haut et vers le bas. Une barre de défilement apparaît dans la marge de droite. L'application montre comment utiliser du texte formaté avec un minimum de balises HTML pour définir le texte en gras ou en italique, et avec des caractères de nouvelle ligne pour séparer les paragraphes. Vous pouvez également inclure des liens Web actifs dans le texte.

![textView1](/img/android/textView.png)

## Notre projet

- Commencez par créer un nouveau projet.
- Ouvrez `activity_main.xml` et remplacez `android.support.constraint.ConstraintLayout` par `RelativeLayout`.
- Supprimez la ligne `xmlns:app="http://schemas.android.com/apk/res-auto"`
- Et faites les modifications suivantes dans le TextView `Hello world`

```xml
<TextView
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:id="@+id/article_heading"
  android:background="@color/colorPrimary"
  android:textColor="@android:color/white"
  android:padding="10dp"
  android:textAppearance="@android:style/TextAppearance.DeviceDefault.Large"
  android:textStyle="bold"
  android:text="Titre de l'article"
/>
```

>Il est recommandé de mettre nos textes et nos paramètres dans les ressources alors c'est ce que nous allons faire.

cliquez sur le `"Titre de l'article"` et une ampoule devrait apparaître (ou alt-entrée) et `Extract string resource`.

Faites de même pour le **padding**  `Extract dimension resource` et nommez la ressource `padding_regular`.

Nous allons continuer avec un autre TextView pour le sous titre:

```xml
<TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/article_sub_heading"
        android:layout_below="@+id/article_heading"
        android:padding="@dimen/padding_regular"
        android:textAppearance="@android:style/TextAppearance.DeviceDefault"
        android:text="Sous titre article"
        />
```

 et on ajoute le contenu de l'article:

```xml
<TextView
        android:id="@+id/article"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_below="@id/article_sub_heading"
        android:lineSpacingExtra="@dimen/line_spacing"
        android:padding="@dimen/padding_regular"
        android:text="@string/texte_de_l_article" />
```

Normalement vous avez ça:

![textview2](/img/android/textview2.png)

## Ajoutons un article

Dans la pratique, une application irait chercher le contenu de l'article via un site externe ou une source type base de donnée.
Ici nous allons tricher un peu.

Ouvrez le fichier `res->values->strings.xml` et remplacez le contenue de `<string name="texte_de_l_article">...</string>` par

```
Dans un coffre-fort au cœur des studios Abbey Road à Londres - protégés par une porte banalisée, à triple verrouillage et alarmée par la police - se trouvent quelque chose comme 400 heures d\'enregistrements inédits des Beatles, à partir du 2 juin 1962 et se terminant par les toutes dernières pistes enregistrées pour l\'album <i> Let It Be </i>. Le meilleur des meilleurs a été publié par Apple Records sous la forme de la série Anthology en 3 volumes.
Pour plus d\'informations, consultez la Beatles Time Capsule sur www.rockument.com.
\n\n Ce volume commence par la première nouvelle chanson des Beatles, "Free as a Bird" (basé sur une démo de John Lennon, trouvée uniquement sur <i> The Lost Lennon Tapes Vol. 28 </i>, et couvre les tout premiers enregistrements historiques, les sorties des premiers albums et les enregistrements en direct des premiers concerts et des sessions de la radio BBC.
\n\n
<b> Les points forts incluent: </b>\n\n
<b> <i> Cry for a Shadow </i> </b> - Beaucoup de fanatiques des Beatles ont commencé la route de la sortie, comme moi, avec une première écoute de cette chanson. Initialement intitulé "Beatle Bop" et enregistré en une seule session qui a produit quatre chansons (les trois autres avec Tony Sheridan avec les Beatles comme groupe d\'accompagnement), "Cry for a Shadow" est un instrument écrit par Lennon et Harrison, ce qui en fait unique à ce jour. John Lennon joue de la guitare rythmique, George Harrison joue de la guitare solo, Paul McCartney joue de la basse et Pete Best joue de la batterie. Les sessions ont été produites par Bert Kaempfert à Hambourg, en Allemagne, lors de la deuxième visite des Beatles d\'avril à juillet 1961 pour jouer dans les clubs de la section Reeperbahn.
\n\n
<b> <i> My Bonnie </i> </b> et <b> <i> Ain\'t She Sweet </i> </b> - À la même session, les Beatles ont joué sur "My Bonnie" (le premier single jamais joué par les Beatles), en tant que groupe d\'accompagnement du chanteur anglais Tony Sheridan, à l\'origine membre des Jets. La popularité de ce single à Liverpool a attiré l\'attention des Beatles sur Brian Epstein, qui a travaillé dans le magasin de disques NEMS et a tenté de répondre à la demande pour le disque. John Lennon chante ensuite une belle chanson "Ain\’t She Sweet" (sa toute première voix sortie).
\n\n
<b><i>Searchin</i> </b> - Une chanson de comédie Jerry Leiber - Mike Stoller qui a été un succès pour les Coasters en 1957 et un favori populaire des Beatles. Les Coasters ont également eu un succès avec "Besame Mucho" et les Beatles ont également repris cette chanson. Ringo Starr avait désormais remplacé Pete Best à la batterie. Le fausset haut est George, qui joue également une guitare solo hésitante. Ceci est de leur première audition pour Decca Records à Londres le 1er janvier 1962, en direct dans le studio. The Grateful Dead couvrira plus tard "Searchin" avec un arrangement similaire, Pigpen faisant la voix de Paul. Une version live est disponible sur les records de sortie avec les Dead rejoint par les Beach Boys!\n\n<b> <i> Love Me Do </i> </b> - Une première version de la chanson, jouée un peu plus lentement et avec plus de blues, et un beat bossa-nova cool au milieu. Paul a dû chanter pendant que John jouait de l\'harmonica - une première pour le groupe. Pete Best a joué de la batterie sur cette version.
\n\n
<b> <i> She Loves You - Till There Was You - Twist and Shout </i> </b> - Live au Princess Wales Theatre de Leicester Square à Londres, en présence de la reine. "Till There Was You" (de Meredith Wilson) est tiré de la comédie musicale The Music Man et un hit pour Peggy Lee en 1961. Avant de le jouer, Paul a dit qu\'il avait été enregistré par son groupe américain préféré, "Sophie Tucker" (qui a obtenu des rires). À la fin, John dit aux gens sur les sièges les moins chers de taper dans leurs mains et aux autres de «faire vibrer vos bijoux», puis annonce «Twist and Shout» (une chanson de Bert Russell et Phil Medley qui a été enregistrée pour la première fois en 1962 par les Isley Brothers). Un film de la représentation montre la reine souriant à la remarque de John.
\n\n
<b> <i> Leave My Kitten Alone </i> </b> - Une des chansons perdues des Beatles enregistrées pendant les sessions "Beatles For Sale" mais jamais sorties. Cette chanson, écrite par Little Willie John, Titus Turner et James McDougal, était un hit R &amp; B de 1959 pour Little Willie John et reprise par Johnny Preston avant que les Beatles ne l\'essayent et ne la mettent de côté. Une référence à un «gros gros bouledogue» a peut-être influencé «Hey Bulldog» de John (album Yellow Submarine), qui est un rockeur similaire.
\n\n
<b> <i> One After 909 </i> </b> - Une chanson enregistrée pour l\'album <i> Let It Be </i> a en fait été reprise au début, six ans plus tôt. Cette prise montre comment ils l\'ont fait beaucoup plus lentement, avec une sensation R &amp; B.
```

Cette chaine est similaire à du **html** pour le gras et l'italique, faites bien attention à ce que vos caractères spéciaux soient échapés.

> N'oubliez pas de modifier le titre et le sous titre de l'article !

## Et le Scroll alors ?

> La vidéo arrive bientôt ici

Si vous avez testé votre application, vous avez surement remarqué que pour le moment rien ne scroll et que le lien vers www.rockument.com et inactif.

L'outil que nous allons utiliser est le `ScrollView`. Celà fonctionne comme des poupées gigognes.

Insérez une balise `<ScrolView>`et mettez le TextView de l'article dedans. Puis déplacez la contrainte placement au niveau du `ScrollView`

```xml
<ScrollView
    android:layout_width="wrap_content"
    android:layout_height="match_parent"
    android:layout_below="@id/article_sub_heading">
    <TextView
        android:id="@+id/article"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:lineSpacingExtra="@dimen/line_spacing"
        android:padding="@dimen/padding_regular"
        android:text="@string/texte_de_l_article" />
</ScrollView>
```

Ajoutez aussi l'attribut dans les attributs du `TextView`

A partir de maintenant notre article scroll et le lien hypertext est actif.
