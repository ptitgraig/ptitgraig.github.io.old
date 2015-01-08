---
layout: post
title: "Les pours et contres des frameworks JS"
date: 2015-01-05 18:04
comments: true
categories: Dev
---

<p class="foreword">
Les pours et les contres des framework BackboneJS, AngularJS, EXTJS, EmberJS : pour t'aider à choisir dans cette jungle.
</p>
<!-- more -->

## Backbone.js

Pours :

* Simplicité - seulement 4 composants (Collection, Model, View, Router).

* Très grosse communauté et beaucoup de solutions sur Stackoverflow.

* Couche supérieures codés intelligemment comme Marionnette et Vertebae.

* L'architecture de ton application peut être personnalisée.

* Léger et mis à jour régulièrement. S'entend bien avec Underscore, jQuery/Zepto - des librairies très répendues.

* Fourni un niveau d' abstraction suffisant avec prise de position raisonnable, permettant une adapation au plus proche des besoins du projet.

* Facile à prendre en main, offrant un bon tremplin pour les framework MV* Javascript.

* Compatibilité avec d'anciens navigateurs : IE7, 8 t même 6 (on osera tout de même pas aller jusque là).

Contres :

* La méthode extend copie le contenu de l'objet parent dans une nouvel objet. Bénéficier de l'héritage prototypal aurait été préférable.

* Manque de cas réel d'utilisation dans la documentation

* L'application TODO met le pied à l'étrier mais il y a encore beaucoup à apprendre après pour construire de grosses applications.

* La quantité de didacticiels sur comment faire les choses avec Backbone n'est pas cohérente et décrivrent des choses à différentes périodes de la vie de Backbone. Dand une équipe, des développeurs peuvent coder les choses à la façcon Backbone 0.3 alors que nous en sommes à une version antérieur à 1.

* Quelques reserves sur Backbone.sync qui pose contient dans son code des hypothèses concernant les communications typique HTTP déclenché par un clien. Ca ne s'adapte pas bien à la nature des WebSockets.

* Il n'y a pas, par défaut, de binding de view. Un re-rendu de la view alors qu'une simple propriété change semble être un peu du gâchi.

## Angular.js

Pours : 

* Two-way data binding (système de liaison des données en deux sens) est extremement puissant. On pense donc d'avantage au model et à son état qu'à une série d'événements à déclencher. Le model est la seul source de vérité.

* Performance. AngularJS coûte peu en téléchargement. Son moteur de template utilise les noeuds du DOM au lieu de convertir des String en noeud. Ce qui doit améliorer les performances.

* Si tu cibles les navigateurs récents et que tu ne te soucis pas, ou peu, des anciens navigateurs, tu peux supprimer jQuery de tes dépendances.

* Intuitif, excellente documentation. Système de liaison des données ingénieux : Vue (view) HTML, portées (scope) imbriquées.

* Une extension Chrome, Batarang, qui fourni un accès en direct aux structures de données Angular.

* Minimisation du code de réutilisable (boilerplate code). Permet la réutilisation du code grâce à des composants. Etend la syntaxe HTML de sorte que de nombreuses fonctionnalités complexes finissent par êre aussi simple que la mise en place d'une directive (attribut) dans le code HTML. 

* Très facilement testable grâce au système d'injection de dépendance.

* Ecrire une application complexe dans jQuery qui manipule le DOM : c'est déjà un petit exploit.

Contres :

* On ne peut pas, d'une manière simplement déclarative, spécifier une transition pour un changement d'UI qui vient d'un changement dans le model.

* Courbe d'apprentissage plus raide que Backbone, mais le gain est appréciable. 

* La documentation pourrait être améliorée.


Largement inspiré de l'article de Smashing Magazine : 
[Journey Through The JavaScript MVC Jungle](http://www.smashingmagazine.com/2012/07/27/journey-through-the-javascript-mvc-jungle/)
