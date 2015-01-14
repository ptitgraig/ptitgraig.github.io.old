---
layout: post
title: "Les fondamentaux de Marionette"
date: 2014-01-30 10:45
comments: true
categories: [Dev]
---

<p class="foreword">
Tu as choisi de développer ta dernière super application web avec Backbone. 
Marionette.js simplifie ton application en te fournissant du code robuste et en adressant certaines lacunes de Backbone.
Voici les fondamentaux de Marionette pour te mettre le pied à l'étrier.
</p>
<!-- more -->

## Pourquoi Marionette ?

Pourquoi utiliser Marionette ? Il est en effet tout à fait possible d'écrire ton application Backbone sans Marionette.
Cependant je t'invite à regarder d'un peu plus près la distribution du code dans Backbone afin de voir là où il peut exister de potentielles lacunes.

Distribution du code dans Backbone :

*   Données : ```Model``` (53% du code)
*   Présentation : ```Views``` (9% du code)
*   Architecture : le reste qui fait la jointure entre données et présentation (```Routes``` etc...) (38% du code).

A la vue de ces données, tu remarqueras que seulement 9% du code de Backbone est dédiée à la gestion des Views. C'est peu, mais caractéristique de Backbone.

Je t'invite maintenant à regarder la distribution du code dans Marionette.

*   Data : ```Model``` (0% du code)
*   Présentation : ```Views``` (67% du code)
*   Architecture : le reste (33% du code)

Tu remarques que 67% du code de Marionette est dédiée à la presentation. Ainsi, Marionette a pour principale objectif de combler le manque de gestion des Vues dans Backbone.

## Problème avec les Views dans Backbone

Les problèmes concernant les Views les plus fréquents sont souvent les suivants :

*   la gestion des vues imbriquées
*   la collection de vues
*   les évènements des vues

Que propose donc Marionette ?

## Marionette Render

Marionette vient avec une nouvelle implémentation de ```Render```.
Voici les différentes fonctionnalités du ```Render``` de Marionette.

1.  Marionette Render sérialise model et collection (transforme en JSON)
    
    ~~~
    person.get('prenom');
    // >> 'Grégory'

    view.serializeData(person)
    // {
    //     prenom: 'Grégory'
    //     nom: 'PELLETEY'
    // }
    ~~~
    {:lang="js"}

2.  Marionette Render te permet d'ajouter des datas à la vue avec le méthode ```templateHelpers```. Comment ça marche :

    ~~~
    MyView.prototype.templateHelpers : function(){
        return : this.model.get('prenom') + 
                 this.model.get('nom')
    }
    // {
    //    prenom : 'Grégory'
    //    nom : 'PELLETEY'
    // }  
    ~~~
    {:lang="js"}

3.  Compile le template si besoin

4.  Render le template

5.  Place le HTML dans l'élèment de la Vue.

6.  Retourne une instance de la Vue.


## Marionette Events

Une pattern courante dans Backbone est un Vue qui veut écouter les évènements qui lui sont attachés.
A chaque fois que je ```render``` la Vue ```MyView```, un appel à la méthode ```onRender``` est fait.

~~~
var MyView = Backbone.View.extend({
    initialize: function() {
        this.on('render', this.onRender, this); 
    },
    onRender: function() {
        console.log('rendered!!');
    }
});

// j'appele la méthode render de la vue myView, le callback est déclenché
myView.trigger('render'); 
~~~
{:lang="js"}

Avec Marionette, tu n'as plus à enregistrer l'évènement :

~~~
var MyView = Backbone.View.extend({
    onRender: function() {
        console.log('rendered!!');
    }
});

// j'appele la méthode render de la vue myView
// et aussi le callback qui y est attaché
myView.triggerMethod('render'); 
~~~
{:lang="js"}

## Marionette Region

Une Region est un conteneur persistent pour un ensemble de child views. Ca te permet d'organiser ton code.

~~~
var RootView = Mn.LayoutView.extend({
    el: 'body',

    regions: {
        sidebar : '.sidebar',
        main : 'main',
        footer : 'footer'
    }
});

// afficher une vie dans une partie de la Region
rootView.getRegion('main').show(new MainView()); 
~~~
{:lang="js"}

Cas des Vues imbriquées

~~~
rootView.getRegion('main').show(new MainView());

var MainView = Mn.LayoutView.extend({
    template: mainViewTemplate,

    regions: {
        title: '.title'
    },

    // methode éxécutée avant que la Vue Main soit affichée
    onBeforeShow: function() {
        this.getRegion('title').show(new TitleView());
    }
});
~~~
{:lang="js"}

## Marionette CollectionView

CollectionView fourni un système de liaison de donnée dans un sens (1-way data binding) entre la collection et les vues.

En Backbone, les collections permettent de faire plusieurs choses :

~~~
var users = new UsersCollection(); // créer une collection

users.length; // connaître la taille d'une collection

// extraire un model d'une collection
var james = users.findWhere({ 
    firstname: 'james'
});
~~~
{:lang="js"}

Avec Marionette

~~~
var UsersList = Mn.CollectionView.extend({
    collection: users,
    childView: UserView // le type de vue que tu veux instancier pour chaque model de la vue !!
});

// j'affiche dans une Region ma Collection
myRegion.show(new UsersList()); 

// puisque c'est 1-way-binding
// la vue va se mettre à jour automatiquement dans le navigateur
users.remove(james); 
~~~
{:lang="js"}


## Résumé sur les Views

Au niveau des Views dans Backbone+Marionette, il y a des bonnes choses et aussi des moins bonnes.

Quelques bonnes choses sont :

*   Tu ne te préoccupe pas du cablage des données;
*   Tu ne te préoccupe pas de quand est-ce que tu dois afficher ta View.

D'autres choses sont moins top :

*   Maintenant il y 4 classes de View avec Marionette (Views, CollectionViews, LayoutViews etc..)

## Architecture

Marionette.Object est une classe de base. Par exemple si tu veux créer un objet qui n'est pas une View ni une Collection ni quoi que ce soit de prédéfini dans Backbone, tu peux l'utiliser car il s'intégrera mieux dans le framework (plusieurs objet Marionette proviennent de Marionette.Object)

Backbone.Radio propose un système d'évènement plus robuste.

## Marionette Routers

### Problème avec le Router

La philosophie de Backbone est que c'est le routage qui contrôle le flow de l'application. Une URI représente une action utilisateur.
Un click sur un bouton ou un lien doit avec son équivalence dans l'URL.
Ainsi cet exemple pose un "problème" car on est obligé de déclencher l'évènement au moment de la navigation :

~~~
var router = new Backbone.Router({
    routes : {
        'book/:id': function() {
            // faire des trucs
        },
        'book/:bookId/chapter/:chapterId': function() {
            // faire d'autres trucs
        }
    }
});

router.navigate('book/3'); // par défaut le callback spécifié dans le Router n'est pas exécuté
router.navigate('book/3', {trigger: true}); // déclenche le callback
~~~
{:lang="js"}

### State Router

Toutes les choses suivantes sont faisaible en Backbone mais ne sont pas parfaites.

-   authentification / autorisation
-   before / after callbacks
-   redirections
-   annulation
-   routers imbriquées
-   page 404

Quelques solutions proposées par Marionette :

-   un objet peut être associé à une Route
-   un objet possède deux rôle principales :
        1.  Récupérer les données
        2.  Montrer les Vues
-   faciliter l'ajout de nouvelles actions à l'objet
-   une action est exécutée à chaque changement d'URI

Cette ensemble est nommé le State Router.

### Base Router

Que faire si le State Router ne te convient pas ?

Marionette propose un Base Router. De base, Backbone fait des choix sur la manière de gérer les URIs. Par exemple, pour une Route donnée, une seule méthode va être appelée. Backbone Router analyse aussi les paramètres de la requête HTTP.

Le Base Router de Marionette s'affranchi des ces choix là.

## Conclusion

Pourquoi s'intéresser à Marionette ?
Marionette est en actif développement et possède une vision pour l'avenir. L'équipe principale est assez conséquente ainsi que la communauté. Marionette te facilite le développement Backbone ajoutant des fonctionnalités utiles pour structurer le code, surtout au niveau des Views.

<p class="disclaimer">
    Article écrit à partir de la vidéo <a href="https://www.youtube.com/watch?v=EvQnntaqVdE">Marionette - The Backbone Framework</a>.<br/>
    Une application Backbone + Marionette est disponible sur mon <a href="https://ptitgraig.github.com/first-steps-backbone-Marionette">GitHub</a> pour référence, qui utilise les principes fondamentaux présentés dans cet article.
</p>