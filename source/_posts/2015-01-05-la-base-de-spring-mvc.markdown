---
layout: post
title: "La base de Spring MVC"
date: 2015-01-05 12:49
comments: false
categories: Dev
---

<p class="foreword">
Fonctionnement basique du framework JAVA Spring MVC. Très bon pour la culture générale d'un développeur front.
</p>
<!-- more -->

## Fonctionnement

Le framework Spring Web MVC est conçu autour d'une DispatcherServlet qui gère toutes les requêtes et réponses HTTP. Illustration ci-dessous :

{% img /images/spring_dispatcherservlet.png %}

1. Après avoir reçu une requête HTTP, DispatchServlet consulte le HandlerMapping pour appeler le Controller approprié.

2. Le Controller analyse la requête et apelle le la méthode du service appropriée, basé l'utilisation des méthodes POST ou GET. La méthode du service initialisera les données du model sur la base d'une logique business puis retournera ne nom de la vue à la DispatcherServlet

3. La DispatcherServlet s'appuiera sur la ViewResolver pour choisir la bonne vue pour la requête

4. Une fois que la vue est finalisée, la DispatcherServlet passe les données du model à la vue, cette dernière étant finalement affichée sur le navigateur.

Tous les composants mentionnés ci-dessus (HandlerMapping, Controller et ViewResolver) font partis du WebApplicationContext, lequel est une extention du ApplicationContext auquel on a ajouté quelques caractéristiques supplémentaires pour les applications web.

## Spring MVC pour faire du REST


