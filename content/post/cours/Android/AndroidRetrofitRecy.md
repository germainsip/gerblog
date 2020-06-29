---
title: "AndroidRetrofitRecy"
date: 2020-06-17T11:10:49+02:00
draft: true
---

## Retrofit et un Recycler

Nous allons créer une application utilisant Retrofit, une API et le RecyclerView

## Mise en place de la base de donnée

Commençons par la base de données.


```sql
-- retro.users definition

CREATE TABLE `users` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `email` varchar(200) NOT NULL,
  `password` text NOT NULL,
  `name` varchar(500) NOT NULL,
  `school` varchar(100) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8mb4;
```

## créons l'api avec SLIM

// install slim

`composer create-project slim/slim-skeleton retroapi`

