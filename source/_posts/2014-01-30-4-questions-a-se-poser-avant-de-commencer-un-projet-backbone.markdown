---
layout: post
title: "4 questions à se poser avant de commencer un projet Backbone"
date: 2014-01-30 10:45
comments: true
categories: [Dev]
---

<p class="foreword">
Vous avez choisi de développer votre dernière super application web avec Backbone. Avant de vous lancer tête baissée dans le développement, réfléchissez à ces 4 questions afin de savoir si Backbone est ce qu'il vous faut.
</p>
<!-- more -->
- - -

Question 1 : Comment vais-je structurer les vues et les controlleurs imbriquées ?

Malheureusement il ne semble pas y avoir de bonne pratique dans ce domaine et cela semble bien dépendre des cas d'utilisation.

Question 2 : Comment vais-je tester les vues ?

Backbone ne vient pas avec des outils de tests, il vous faudra les fabriquer vous même. Pour les vues, c'est difficile; si difficile que de nombreuses personnes ne se fatiguent pas avec ça.

Question 3 : Pourquoi mon application a t-elle des fuites de mémoires ?

Dans les Single-Page-Webapp , les objets peuvent demeurer en mémoire un bon moment, plutôt que d'être recréer quand la page est rafraichie. C'est très utile mais vous devez vous assurez que ces objets sont bien utilisez et ne consomme pas inutilement de la mémoire.

Question 4 : Pourquoi est-ce que le rendu de mon application est si lent ?

Avec Backbone, il est vraiment facile de faire de petites mise à jour du DOM pour chaque interaction de l'utilisateur. Dès que vous traitez un large ensemble de données, l'expérience utilisateur est altérée.