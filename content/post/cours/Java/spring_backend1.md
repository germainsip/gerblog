---
title: "Un backend avec Spring part 1"
date: 2020-11-02T16:32:21+01:00
draft: true
description: Création d'un backend avec spring
categories:
  - Java
  - Spring-Boot
tags:
  - Java
  - RestApi
  - SpringBoot
thumbnail: img/springboot.png
lead: Backend avec SpringBoot
comments: false
authorbox: true
toc: true
mathjax: true
---

## Utiliser Spring-boot comme backend

### Création du projet Spring
<!--TODO: introduire le projet spring -->
dépendances:

- Lombok
- Spring Web
- Spring Security
- Spring DataJpa
- MySQL Java Driver
- Java Mail Sender

Après la génération du projet il nous faudra ajouter au `pom.xml` les dépendances suivantes:

```xml
<!-- pour le JWT -->
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt-api</artifactId>
            <version>0.10.5</version>
        </dependency>
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt-impl</artifactId>
            <scope>runtime</scope>
            <version>0.10.5</version>
        </dependency>
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt-jackson</artifactId>
            <scope>runtime</scope>
            <version>0.10.5</version>
        </dependency>
<!-- For Displaying time as Relative Time Ago ("Posted 1 Day ago"),
         as this is a Kotlin library, we also need Kotlin runtime libraries-->
        <dependency>
            <groupId>com.github.marlonlom</groupId>
            <artifactId>timeago</artifactId>
            <version>4.0.1</version>
        </dependency>
        <dependency>
            <groupId>org.jetbrains.kotlin</groupId>
            <artifactId>kotlin-stdlib-jdk8</artifactId>
            <version>${kotlin.version}</version>
        </dependency>
        <dependency>
            <groupId>org.jetbrains.kotlin</groupId>
            <artifactId>kotlin-test</artifactId>
            <version>${kotlin.version}</version>
            <scope>test</scope>
        </dependency>
```

### Configuration de la base de donnée

Dans le fichier **src/main/resources/application.properties** ajoutons les éléments nécessaires à la connection:

```
###### Database Properties  ###########################################
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/faq-spring-afpa?useSSL=false&serverTimezone=UTC&useLegacyDatetimeCode=false
spring.datasource.username=<your-db-username>
spring.datasource.password=<your-db-password>
spring.jpa.hibernate.ddl-auto=update
spring.datasource.initialize=true
spring.jpa.show-sql=true
```

ici la base de donnée sera hébergée par MariaDB mais nous pourrions le faire avec n'importe quel autre base.

Le schema de la base est le suivant:

![mcd](/img/DB-Schema.png)

<!-- TODO: exemple de base à changer -->
### Création des entités correspondantes

Grace à Lombok, nous allons économiser de l'énergie et surtout du code. Les annotations @Data, `@AllArgsConstructor`, et `@NoArgsConstructor` automatisent au moment de la compilation la génération des assesseurs et des constructeurs cf: [site de Lombok](https://projectlombok.org)

Commençons par la table **user** et sa classe entité `User.java` dans le package **model**

- User.java

```java
package org.gerblog.faqspringafpa.model;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.validation.constraints.Email;
import javax.validation.constraints.NotBlank;
import javax.validation.constraints.NotEmpty;
import java.time.Instant;


@Data
@AllArgsConstructor
@NoArgsConstructor
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE)
    private Long userId;
    @NotBlank(message = "Username is required")
    private String username;
    @NotBlank(message= "Password is required")
    private String password;
    @Email
    @NotEmpty(message = "Email is required")
    private String email;
    private Instant created;
    private boolean enabled;

}
```

Les annotations `@Id` et `@GeneratedValue(strategy = SEQUENCE)` identifient l'attribut `userId` comme clé primaire auto incrémentée. Les autres annotations sont lisibles et vont permettre à hibernate de faire la liaison avec la base de donnée.

> Faites bien attention à utiliser les bons imports

Créez de la même manière les Entités `Post`, `Subreddit`, `Vote`, `Comment`, `VerificationToken` et l'énumération `VoteType` ainsi que la classe `NotificationEmail`.

> C'est de la mise en place, vous allez comprendre l'utilité de chacune de ces classes par la suite.

- Post.java

```java
package org.gerblog.faqspringafpa.model;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.springframework.lang.Nullable;

import javax.persistence.*;
import javax.validation.constraints.NotBlank;
import java.time.Instant;

@Data
@Entity
@Builder
@AllArgsConstructor
@NoArgsConstructor
public class Post {
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE)
    private Long postId;
    @NotBlank(message = "Post Name cannot be empty")
    private String postName;
    @Nullable
    private String url;
    @Nullable
    @Lob
    private String description;
    private Integer voteCount;
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "userId", referencedColumnName = "userId")
    private User user;
    private Instant createdDate;
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "id", referencedColumnName = "id")
    private Subreddit subreddit;

}
```

- Subreddit.java

```java
package org.gerblog.faqspringafpa.model;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

import javax.persistence.*;
import javax.validation.constraints.NotBlank;
import java.time.Instant;
import java.util.List;

import static javax.persistence.FetchType.LAZY;
import static javax.persistence.GenerationType.SEQUENCE;

@Data
@AllArgsConstructor
@NoArgsConstructor
@Entity
@Builder
public class Subreddit {
    @Id
    @GeneratedValue(strategy = SEQUENCE)
    private Long Id;
    @NotBlank(message = "Community name is required")
    private String name;
    @NotBlank(message = "Description is required")
    private String description;
    @OneToMany(fetch = LAZY)
    private List<Post> posts;
    private Instant createdDate;
    @ManyToOne(fetch = LAZY)
    private User user;
}
```

- Vote.java

```java
package org.gerblog.faqspringafpa.model;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.jboss.jandex.VoidType;

import javax.persistence.*;
import javax.validation.constraints.NotNull;

@Data
@AllArgsConstructor
@NoArgsConstructor
@Entity
@Builder
public class Vote {
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE)
    private Long voteId;
    private VoteType voteType;
    @NotNull
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "postId",referencedColumnName = "postId")
    private Post post;
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "userId", referencedColumnName = "userId")
    private User user;
}
```

- Comment.java

```java
package org.gerblog.faqspringafpa.model;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

import javax.persistence.*;
import javax.validation.constraints.NotEmpty;

import java.time.Instant;

import static javax.persistence.FetchType.LAZY;
import static javax.persistence.GenerationType.SEQUENCE;

@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
@Entity
public class Comment {
    @Id
    @GeneratedValue(strategy = SEQUENCE)
    private Long id;
    @NotEmpty
    private String text;
    @ManyToOne(fetch = LAZY)
    @JoinColumn(name = "postId",referencedColumnName = "postId")
    private Post post;
    private Instant createdDate;
    @ManyToOne(fetch = LAZY)
    @JoinColumn(name = "userId",referencedColumnName = "userId")
    private User user;
}
```

- VoteType.java

```java
package org.gerblog.faqspringafpa.model;

public enum VoteType {
UPVOTE(1), DOWNVOTE(-1),
    ;

VoteType(int direction){}
}
```

- VerificationToken

```java
package org.gerblog.faqspringafpa.model;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import javax.persistence.*;
import java.time.Instant;

@Data
@AllArgsConstructor
@NoArgsConstructor
@Entity
@Table(name = "token")
public class VerificationToken {
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE)
    private Long id;
    private String token;
    @OneToOne(fetch = FetchType.LAZY)
    private User user;
    private Instant expiryDate;
}

```

- NotificationEmail

```java
package org.gerblog.faqspringafpa.model;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class NotificationEmail {
    private String subject;
    private String recipient;
    private String body;
}
```

### Création des Repository

Les "repo" servent à faire le lien avec la base. Nous allons créer un package `repository` et y créer un repo par entité.

Créez une interface héritant de `JpaRepository` pour chaque entité.
A l'intérieur de celle ci nous définissons la méthode d'accès à nos données.

- UserRepository

```java
package org.gerblog.faqspringafpa.repository;

import org.gerblog.faqspringafpa.model.User;
import org.springframework.data.jpa.repository.JpaRepository;

import java.util.Optional;

public interface UserRepository extends JpaRepository<User,Long> {
    Optional<User> findByUsername(String username);
}
```

- PostRepository

```java
package org.gerblog.faqspringafpa.repository;

import org.gerblog.faqspringafpa.model.Post;
import org.gerblog.faqspringafpa.model.Subreddit;
import org.gerblog.faqspringafpa.model.User;
import org.springframework.data.jpa.repository.JpaRepository;

import java.util.List;

public interface PostRepository extends JpaRepository<Post,Long> {
    List<Post> findAllBySubreddit(Subreddit subreddit);

    List<Post> findByUser(User user);

}
```

- CommentRepository

```java
package org.gerblog.faqspringafpa.repository;

import org.gerblog.faqspringafpa.model.Comment;
import org.gerblog.faqspringafpa.model.Post;
import org.gerblog.faqspringafpa.model.User;
import org.springframework.data.jpa.repository.JpaRepository;

import java.util.List;

public interface CommentRepository extends JpaRepository<Comment,Long> {
    List<Comment> findByPost(Post post);

    List<Comment> findAllByUser(User user);
}
```

- VoteRepository

```java
package org.gerblog.faqspringafpa.repository;

import org.gerblog.faqspringafpa.model.Post;
import org.gerblog.faqspringafpa.model.User;
import org.gerblog.faqspringafpa.model.Vote;
import org.springframework.data.jpa.repository.JpaRepository;

import java.util.Optional;

public interface VoteRepository extends JpaRepository<Vote, Long> {
    Optional<Vote> findTopByPostAndUserOrderByVoteIdDesc(Post post, User currentUser);
}
```

- VerificationTokenRepository

```java
package org.gerblog.faqspringafpa.repository;

import org.gerblog.faqspringafpa.model.VerificationToken;
import org.springframework.data.jpa.repository.JpaRepository;

import java.util.Optional;

public interface VerificationTokenRepository extends JpaRepository<VerificationToken, Long> {
    Optional<VerificationToken> findByToken(String token);
}
```

### Inspection et premier démarrage

Votre dossier `src` doit maintenant ressembler à ça:

```zsh
src
├── main
│   ├── java
│   │   └── org
│   │       └── gerblog
│   │           └── faqspringafpa
│   │               ├── model
│   │               │   ├── Comment.java
│   │               │   ├── NotificationEmail.java
│   │               │   ├── Post.java
│   │               │   ├── Subreddit.java
│   │               │   ├── User.java
│   │               │   ├── VerificationToken.java
│   │               │   ├── Vote.java
│   │               │   └── VoteType.java
│   │               ├── repository
│   │               │   ├── CommentRepository.java
│   │               │   ├── PostRepository.java
│   │               │   ├── UserRepository.java
│   │               │   ├── VerificationTokenRepository.java
│   │               │   └── VoteRepository.java
│   │               └── FaqSpringAfpaApplication.java
│   └── resources
└── test
```

Créez une base de donnée vide portant le nom `faq-spring-afpa` dans votre mariaDB car hibernate va prendre cette base comme point de départ et créer les tables pour nous.

Ensuite lancez le projet directement via la flèche verte d'IntelliJ. (ou en console `./mvnw spring-boot:run
`)

Si tout s'est bien passé vous avez maintenant une base avec des tables et contraintes dans mariaDB:

![shema](/img/faq-spring-afpa.png)


