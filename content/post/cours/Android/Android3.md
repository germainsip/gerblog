---
title: Voyage entre les Intent
date: 2020-03-27T10:57:32.000Z
draft: false
description: Les intent d'android
categories:
  - Android
tags:
  - Android
  - Intent
thumbnail: img/androidbase.png
lead: Voyage entre les Intent
comments: false
authorbox: true
toc: true
---
## Les Activités et les `Intent`

Nous avons vue que les vues étaient appelées des activités en Android. Afin d'ouvrir une nouvelle activité et transférer des information d'une activité à l'autre nous allons avoir besoin des `Intent` qui vont permettre de faire naviguer des informations d'une vue à l'autre.

{{< youtube I7FUF1ROA5g>}}

---
---

Nous allons faire une simple application qui dans un premier temps ouvrira une deuxième activité au clique sur un bouton et dans un deuxième temps nous verrons comment envoyer un message sur la deuxième activité puis enfin renvoyer une réponse sur la première.

## C'est partie

De la manière que nous l'avons fait créons un projet nommé DeuxActivites.
Dans `activity_main.xml` enlevez le TextView et ajoutez un bouton à qui on donnera le nom `button_main`


```xml
<Button
  android:id="@+id/button_main"
  android:layout_width="wrap_content"
  android:layout_height="wrap_content"
  android:text="@string/button_main"
  android:layout_marginBottom="16dp"
  android:layout_marginRight="16dp"
  android:onClick="launchSecondActivity"
  app:layout_constraintBottom_toBottomOf="parent"
  app:layout_constraintRight_toRightOf="parent"
  />
```

Pensez bien à renvoyer le text dans `strings.xml`. Pour le moment est souligné en rouge car la méthode n'existe pas. A l'aide de l'ampoule rouge créez la méthode dans `MainActivity.java`.

Dans `MainActivity.java` ajoutez `Log.d(LOG_TAG,"clique sur bouton");` dans la méthode `launchSecondActivity()` et ajoutez `private static final String LOG_TAG = MainActivity.class.getSimpleName();` en haut de la classe.

Exécutez votre appli. Si tout se déroule bien au clique sur le bouton vous devriez lire dans l'onglet run en bas de l'IDE

```zsh
D/MainActivity: clique sur bouton
```
> la méthode log est l'équivalent en Java du println que nous utilisons pour faire des impressions en console. La grosse différence est que nous signons nos événements par une String.

## Créons la seconde activité

Pour android, il ya deux façons de faire du lien entre les activités:

- explicite: nous connaissons déjà la classe cible de l'intent.
- implicite: nous ne connaissons pas nécessairement le nom de la cible mais nous avons une action générale à effectuer.

Cliquez droit sur le dossier **app** puis **new -> activity -> Empty Activity** et nommez la nouvelle activité `SecondActivity`.

Dans **AndroidManifest** nous définissons plus précisément notre activité. Ces information seront utilisées pour afficher la navigation en haut dans la barre de navigation.

dans la Manifest remplacez

```xml
<activity android:name=".SecondActivity"></activity>
```

par

```xml
<activity
  android:name=".SecondActivity"
  android:label="Seconde Activité"
  android:parentActivityName=".MainActivity">
</activity>
```

Puis extrayez le label dans `strings.xml`

## Petite mise en page

dans `activity_second.xml` faite la mise en page suivante en ajoutant un TextView

```xml
<TextView
    android:id="@+id/text_header"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginTop="16dp"
    android:layout_marginStart="8dp"
    android:text="Message reçu"
    android:textAppearance="@style/TextAppearance.AppCompat.Medium"
    android:textStyle="bold"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toTopOf="parent"/>
```

## Définissons l'Intent

Enfin dans `MainActivity.java` créez l'**intent** dan la méthode `launchSecondActivity()`

L'`intent` prend deux paramètres, le `context` et la classe de l'activité à charger.

`Intent intent = new Intent(this, SecondActivity.class);`

Il faut ensuite démarrer l'activité: `startActivity(intent);`

Vous pouvez maintenant lancer votre activité.

## Envoyons un message (partie 2)

{{<youtube tOVQ0FjpLbg>}}

---
---

Notre objet d'intention peut transmettre des données à l'activité cible de deux manières: dans le champ de données ou dans les extras d' intention . Les données d'intention sont un URI indiquant les données spécifiques sur lesquelles agir. Si les informations que vous souhaitez transmettre à une activité via une intention ne sont pas un URI, ou si vous avez plusieurs informations à envoyer, vous pouvez mettre ces informations supplémentaires dans les extras à la place.

Les **extras** d' intention sont des paires **clé / valeur** dans un Bundle (paquet). Un `Bundle` est une collection de données, stockées sous forme de paires **clé / valeur**. Pour transmettre des informations d'une activité à une autre, vous mettez des clés et des valeurs dans l'extra de l'intention de l'activité d'envoi, puis vous les récupérez dans l'activité de réception.

Commencez par ajouter un `EditText` à notre premier layout et nommez le `edit_text_message`

> les indications du champ text sont dans l'attribut `android:hint=" ... "`

Définissez la clé "Extra" de l'intent et l'`EditText` en haut de la classe `MainActivity`

```java
public static final String EXTRA_MESSAGE = "deuxActivite.extra.MESSAGE";
private EditText mEditTextMessage;
```
Dans la méthode `onCreate()` on va récupérer notre EditText:


```java
 mEditTextMessage = findViewById(R.id.edit_text_message);
```

Et dans la méthode `launchSecondActivity()` on va créer notre message "extra" et l'ajouter à l'intent


```java
String message = mEditTextMessage.getText().toString();
Intent intent = new Intent(this, SecondActivity.class);
intent.putExtra(EXTRA_MESSAGE,message);
startActivity(intent);
```

## De l'autre côté

Dans `activity_second.xml` ajoutez un `TextView` afin de pouvoir répondre. Nommez le `text_message`

Dans `SecondActivity.java`, dans la méthode onCreate(), récupérons notre intent ainsi que son contenu et mettons le dans le TextView:

```java
Intent intent = getIntent();

String message = intent.getStringExtra(MainActivity.EXTRA_MESSAGE);

TextView textView = findViewById(R.id.text_message);

textView.setText(message);
```

Vous pouvez tester votre appli.

## Le sens retour (Partie 3)

{{<youtube RqjtI-hFNiA>}}

---
---

Nous allons maintenant faire le sens retour. Ajoutons un champ `EditText` et un `Button` à notre second layout (`activity_second.xml`).
Vous pouvez simplement copier coller les deux éléments de la première activité et corriger les id et actions pour obtenir ceci:

```xml
<Button
        android:id="@+id/button_second"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginEnd="16dp"
        android:layout_marginBottom="16dp"
        android:onClick="returnReply"
        android:text="@string/repondre"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintRight_toRightOf="parent" />

    <EditText
        android:id="@+id/edit_text_reponse"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginEnd="8dp"
        android:layout_marginBottom="16dp"
        android:ems="10"
        android:hint="@string/entrez_votre_reponse"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toStartOf="@+id/button_second"
        app:layout_constraintHorizontal_bias="0.0"
        app:layout_constraintStart_toStartOf="parent"
        android:inputType="text" />
```

> **Attention :hand: :** j'ai intégré les textes dans `strings.xml`
> votre strings.xml doit ressembler à :
> ```xml
><resources>
>    <string name="app_name">DeuxActivites</string>
>    <string name="button_main">Envoyer</string>
>    <string name="activity2_name">Seconde Activité</string>
>    <string name="message_recu">Message reçu</string>
>    <string name="entrez_votre_message">Entrez votre message</string>
>    <string name="repondre">Répondre</string>
>    <string name="entrez_votre_reponse">Entrez votre réponse</string>
></resources>
>```
>D'ailleurs ce fichier est essentiel si on veut traduire notre application mais on verra ça plus tard.

Dans notre contrôleur nous allons ajouter un `Intent` et son extra ainsi que l'action à effectuer:


```java
public class SecondActivity extends AppCompatActivity {

    private static final String EXTRA_REPLY = "deuxActivite.extra.REPLY";
    private EditText mReply;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_second);

        // reception du message du main
        Intent intent = getIntent();
        String message = intent.getStringExtra(MainActivity.EXTRA_MESSAGE);
        TextView textView = findViewById(R.id.text_message);
        textView.setText(message);

        mReply = findViewById(R.id.edit_text_reponse);

    }

    public void returnReply(View view) {
        // récupération de la réponse de l'utilisateur
        String reply = mReply.getText().toString();
        // Instanciation d'un nouvel intent
        Intent replyIntent = new Intent();
        replyIntent.putExtra(EXTRA_REPLY,reply);
        // mise à jour de l'état de la réponse
        setResult(RESULT_OK,replyIntent);
        // on termine l'activité 2
        finish();
    }
}
```

> **Attention :hand: :** Vous remarquerez que l'on instancie un **nouvel** `Intent` et que nous **terminons** l'activité avec `finish()`

## Il nous reste à recevoir la réponse

Ajoutez le `Textview` pour la réponse par copié collé et modification adéquate.

Vous devriez obtenir le xml suivant à ajouter dans `activity_main.xml` :


```xml
<TextView
        android:id="@+id/text_header_reply"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:layout_marginStart="8dp"
        android:text="@string/reponse_de_la_2eme_vue"
        android:textAppearance="@style/TextAppearance.AppCompat.Medium"
        android:textStyle="bold"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"/>

    <TextView
        android:id="@+id/text_message_reply"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/reponse_vue2"
        android:layout_marginStart="8dp"
        android:layout_marginEnd="8dp"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@id/text_header_reply"/>
```

> **Remarque:** ne copiez pas mon code et essayer de faire la manipulation vous même.

Modifiez la visibilité des deux `TextView` en ajoutant l'attribut `android:visibility="invisible"` à chacun d'eux. Ainsi ces textes ne s'afficherons que quand on aura fait notre passage par la deuxième vue.

## Dernière chose à faire...

Déclarons et activons tout ça dans le `MainActivity.java`

Les déclarations d'usage maintenant en haut de notre classe:


```java
private TextView mReplyHeadTextView;
private TextView mReplyTextView;

public static final int TEXT_REQUEST = 1;
```

en haut de notre classe et une nouveauté `TEXT_REQUEST` (suspens...)

```java
mReplyHeadTextView = findViewById(R.id.text_header_reply);
mReplyTextView = findViewById(R.id.text_message_reply);
```

Dans la méthode `onCreate()`.

Modifiez la méthode de lancement de l'activité de `startActivity(intent)` en `startActivityForResult(intent,TEXT_REQUEST);`.

Maintenant surchargeons la méthode `onActivityResult` avec nos conditions. C'est elle qui va récupérer notre réponse s'il y en a une et afficher le message.


```java
@Override
    protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == TEXT_REQUEST) {
            if (resultCode == RESULT_OK) {
                String reply = data.getStringExtra(SecondActivity.EXTRA_REPLY);

                mReplyHeadTextView.setVisibility(View.VISIBLE);

                mReplyTextView.setText(reply);
                mReplyTextView.setVisibility(View.VISIBLE);
            }
        }
```

Et c'est tout! Maintenant, si vous lancez l'application vous devez pouvoir faire voyager des messages d'un côté à l'autre.

