---
title: "AndroidRetrofit"
date: 2020-06-16T14:25:13+02:00
draft: true
---
## Consommons des API

[Retrofit2](https://square.github.io/retrofit/) est une librairie Java qui va se charger pour nous de toute la partie connection et récupération des Json que le serveur nous mettra à disposition.

## Mise en place

Nous utiliserons l'API publique [JsonPlaceholder](https://jsonplaceholder.typicode.com) dont je parle aussi dans l'article similaire avec JavaFX.

Commencez par créer un projet Android et ajoutez les dépendances suivantes:



```gradle
dependencies {
    // autres dépendances
    implementation 'com.squareup.retrofit2:retrofit:2.9.0'
    implementation 'com.squareup.retrofit2:converter-gson:2.9.0'
}
```

> vous adapterez la version!!! et vous synchroniserez bien gradle.

## Le POJO

Comme pour JavaFX on utilise un POJO (objet java correspondant à l'objet Json). Ici, nous allons récupérer une liste d'articles (posts en anglais)qui ressemblent à ça en json:


```json
{
    "userId": 1,
    "id": 1,
    "title": "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
    "body": "quia et suscipit\nsuscipit recusandae consequuntur expedita et cum\nreprehenderit molestiae ut ut quas totam\nnostrum rerum est autem sunt rem eveniet architecto"
  }
```

la traduction en java:

```java
public class Post {
    private int userId;
    private int id;
    private String title;

    @SerializedName("body")
    private String text;

    public int getUserId() {
        return userId;
    }

    public int getId() {
        return id;
    }

    public String getTitle() {
        return title;
    }

    public String getText() {
        return text;
    }
}
```

> on peut remarquer que nous pouvons changer le nom des membres et indiquer la correspondance grace au annotations. Le membre **body** dans le json sera **text** dans java.

## Un première simple requête

Je vous laisse faire une `MainActivity` et une `activity_main` où nous aurons une simple `NestedScrollView` avec le text de nos objets.


```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="8dp"
    tools:context=".MainActivity">
<androidx.core.widget.NestedScrollView
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <TextView
        android:id="@+id/text_result"
        android:textColor="#000"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.core.widget.NestedScrollView>
</androidx.constraintlayout.widget.ConstraintLayout>
```

Créons maintenant l'interface dont a besoin **Retrofit**.

Je la nomme `ApiService.java` mais vous pouvez la nommer comme vous voulez.


```java
public interface ApiService {
    @GET("posts")
    Call<List<Post>> getPosts();
}
```

Ici on n'indique que la méthode (il s'agit d'une interface) et la requète dans l'annotation `@GET`. C'est **Retrofit2** qui va se charger d'écrire le nécessaire pour récupérer la liste json et la transformer en liste d'objet `post`. Le Call est spécifique à **Retrofit2** faites bien attention aux imports.

On est prêt, c'est parti! Dans le `MainActivity`on va ajouter tout d'abord le `TextView` pour l'affichage et on va instancier `Retrofit`.


```java
.
.
private TextView textResult;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        textResult = findViewById(R.id.text_result);
        Retrofit retrofit = new Retrofit.Builder()
                .baseUrl("https://jsonplaceholder.typicode.com/")
                .addConverterFactory(GsonConverterFactory.create())
                .build();
.
.
```

### Pause explications

On instancie Retrofit avec new `Retrofit.Builder()` qui va, comme son nom l'indique, construire notre outils. Vous devez indiquer la racine de l'API avec un `/` à la fin. Retrofit ajoutera ce dont il a besoin à la suite. Par exemple, ici on va constituer l'adresse "https://jsonplaceholder.typicode.com/posts" grace à la méthode `getPosts()` que l'on a écrit dans l'interface. On ajoute le convertisseur Gson pour convertir le json en java et on `build()`.

Maintenant on demande à Retrofit de créer pour nous le service:

```java
ApiService apiService = retrofit.create(ApiService.class);
Call<List<Post>> call = apiService.getPosts();
```

Et nous allons faire appel à la méthode `Call` et notre service avec la méthode `getPosts()`.

```java
call.enqueue(new Callback<List<Post>>() {
            private static final String TAG = "TEST_AFFICHAGE";

            @Override
            public void onResponse(Call<List<Post>> call, Response<List<Post>> response) {
                if (!response.isSuccessful()) {
                    textResult.setText("Oups! le code de réponse est " + response.code());
                    return;
                }

                List<Post> posts = response.body();

                for (Post post : posts) {
                    String content = "";
                    content += "ID: " + post.getId() + "\n";
                    content += "User ID: " + post.getUserId() + "\n";
                    content += "Title: " + post.getTitle() + "\n";
                    content += "Text: " + post.getText() + "\n\n";
                    Log.d(TAG, "onResponse: "+content);
                    textResult.append(content);
                }
            }

            @Override
            public void onFailure(Call<List<Post>> call, Throwable t) {
                textResult.setText(t.getMessage());
            }
        });
    }
```

L'utilisation de `.enqueue(new Callback...` permet de faire l'action en arrière plan car nous ne pouvons pas exécuter la requête dans le même processus que celui de l'affichage. Sinon l'appli crasherait.

> Profitez de l'autocompletion en ne tapant que `new Callba...` IntelliJ vous écrira les deux méthodes `onResponse` et `onFailure` (réussite, échec).

On regarde aussi avec `response.isSuccessful` si on a bien une réponse 200 du serveur.

Si on est bon, je décompose la liste de posts en texte et je l'envoie dans le TextView avec `append()`

![screen1](/img/android/retrofitandrooid1.png)

## Manipulons l'URL de l'api


