---
layout: post
title: "Introduction au test unitaire JavaScript"
date: 2015-01-08 17:46
comments: true
categories: Dev
---

<p class="foreword">
Traduction de l'article du Smashing Magazine : Introduction To JavaScript Unit Testing, écrit par Jörn Zaefferer en Juillet 2012.
</p>
<!-- more -->

<p class="disclaimer">
<a href="https://github.com/ptitgraig/first-step-unit-js-tests">Le code étape par étape est disponible sur GitHub.</a></strong>
</p>


Vous savez probablement que le test est une bonne chose, mais le premier obstacle à surmonter en essayant d'écrire des tests unitaires pour du code client est le manque de toutes les unités; le code JavaScript est écrit pour chaque page d'un site Web ou chaque module d'une application et est étroitement mélangés avec la logique back-end et le HTML correspondant. Dans le pire des cas, le code est complètement mélangé avec le langage HTML, comme les gestionnaires d'événements directement dans le markup.

C'est probablement le cas lorsqu'on utilise aucune bibliothèque JavaScript, qui permettrait une abstraction du DOM; l'écriture de gestionnaires d'événements directement dans le markup est beaucoup plus facile d'utilisation que les API DOM pour lier ces événements.
De plus en plus de développeurs choisissent une bibliothèque comme jQuery pour gérer l'abstraction du DOM. Ceci leur permet de déplacer la gestion des ces événements dans un script distinct, soit sur ​​la même page ou dans un fichier JavaScript à part. Toutefois, mettre le code dans des fichiers séparés ne signifie pas qu'il est prêt à être testé unitairement.

Qu'est ce qu'une unité de toute façon? Dans le meilleur des cas, c'est une fonction que vous pouvez gérer de différentes façons - une fonction qui vous donne toujours le même résultat pour une entrée donnée. C'est ce qui rend les tests unitaires assez facile, mais la plupart du temps il faut gérer les effets secondaires, entre autre : la manipulations du DOM. Il est utile de trouver quelles sont ces unités qui permettront la structure de notre code et de construire les tests unitaire en conséquence.

## Construire des tests unitaires

Cela étant dit, commencer avec les tests unitaires est bien plus facile quand on commence de rien. Mais cet article ne parle pas de ce cas là. Cet article est là pour vous aider sur le problème le plus difficile : partir sur du code existant, en tester les parties importantes et éventuellement en corriger les bugs.

Le processus d'extraction de code et de son changement de forme, sans en modifier son comportement, est appelé refactorisation. C'est un excellente méthode pour améliorer la manière dont le code d'un programme est conçu. Et puisque la moindre modification peut potentiellement changer le comportement du programme, il est plus sûr de le faire quand les tests unitaires sont en place.

Cela veut dire que pour ajouter des tests unitaire, vous devez prendre le risque de casser des choses : c'est le problème de l'oeuf et de la poule (qui est venu le premier ?). Donc, avant que vous ayez une bonne couverture de test unitaire, vous devez continuer à tester manuellement pour minimiser le risque de régression.

Assez de théorie ! Passons à la pratique : tester du code JavaScript mélangé au HTML et relié à une page. Ce code a pour but de chercher tous les liens qui possède un attribut ```title```. Ces attributs ```title``` sont utilisés pour afficher, quand quelque chose a été posté, une date relative du genre *il y a 5 jours* :

~~~
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <title>Test unitaire sur date</title>
    <script>
    function prettyDate(time){
        var date = new Date(time || ""),
            diff = ((new Date().getTime() - date.getTime()) / 1000),
            day_diff = Math.floor(diff / 86400);

        if (isNaN(day_diff) || day_diff < 0 || day_diff >= 31) {
            return;
        }

        return day_diff == 0 && (
                diff < 60 && "à l'instant" ||
                diff < 120 && "1 minute" ||
                diff < 3600 && Math.floor( diff / 60 ) + " minutes" ||
                diff < 7200 && "1 heure" ||
                diff < 86400 && Math.floor( diff / 3600 ) + " heures") ||
            day_diff == 1 && "Hier" ||
            day_diff < 7 && day_diff + " days ago" ||
            day_diff < 31 && Math.ceil( day_diff / 7 ) + " semaines";
    }
    window.onload = function(){
        var links = document.getElementsByTagName("a");
        for (var i = 0; i < links.length; i++) {
            if (links[i].title) {
                var date = prettyDate(links[i].title);
                if (date) {
                    links[i].innerHTML = date;
                }
            }
        }
    };
    </script>
</head>
<body>

<ul>
<li class="entry" id="post57">
    <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Curabitur dignissim quis libero eu lobortis. </p>
    <small class="extra">
        Posté il y a <a href="/2008/01/blah/57/" title="2008-01-28T21:24:17Z">January 28th, 2008</a>
        par <a href="/john/">John Resig</a>
    </small>
</li>
<!-- more list items -->
</ul>

</body>
</html>
~~~
{:lang="html"}


Si vous exécuter cet exemple dans une navigateur, vous remarquerez un problème : aucune date n'est remplacée. Le code fonctionne cependant correctement. Il boucle bien sur tous les liens de la page et vérifie, pour chacun, si l'attribut ```title``` est présent. Si il y en a bien un, il passe dans la fonction ```prettyDate```. Si ```prettyDate``` renvoie un résultat, le innerHTML du lien est mis à jour avec ce résultat.

## Rendre le code testable

Le problème est que pour toute date antérieure à 31 jours, ```prettyDate``` renvoie juste ```undefined``` (implicitement, avec ```return```), laissant ainsi tel quel le texte du lien. Donc, pour voir ce qui est censé se produire, on doit en dur une date actuelle :

~~~
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <title>Test unitaire sur date</title>
    <script>
    function prettyDate(now, time){
        var date = new Date(time || ""),
            diff = (((new Date(now)).getTime() - date.getTime()) / 1000),
            day_diff = Math.floor(diff / 86400);

        if (isNaN(day_diff) || day_diff < 0 || day_diff >= 31) {
            return;
        }

        return day_diff == 0 && (
                diff < 60 && "à l'instant" ||
                diff < 120 && "1 minute" ||
                diff < 3600 && Math.floor( diff / 60 ) + " minutes" ||
                diff < 7200 && "1 heure" ||
                diff < 86400 && Math.floor( diff / 3600 ) + " heures") ||
            day_diff == 1 && "Hier" ||
            day_diff < 7 && day_diff + " jours" ||
            day_diff < 31 && Math.ceil( day_diff / 7 ) + " semaines";
    }
    window.onload = function(){
        var links = document.getElementsByTagName("a");
        for (var i = 0; i < links.length; i++) {
            if (links[i].title) {
                var date = prettyDate("2008-01-28T22:25:00Z", links[i].title);
                if (date) {
                    links[i].innerHTML = date;
                }
            }
        }
    };
    </script>
</head>
<body>

<ul>
<li class="entry" id="post57">
    <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Curabitur dignissim quis libero eu lobortis.</p>
    <small class="extra">
        Posté il y a <a href="/2008/01/blah/57/" title="2008-01-28T21:24:17Z">January 28th, 2008</a>
        par <a href="/john/">John Resig</a>
    </small>
</li>
<!-- more list items -->
</ul>

</body>
</html>
~~~
{:lang="html"}

Maintenant, les liens devraient avoir comme valeur : "à l'instant", "il y a 2 heures", "hier" etc... C'est un départ, mais ce n'est pas encore une unité testable. Donc, tout ce que nous pouvons faire, sans changer le code d'avantage, c'est tester les changements qui se sont produits dans DOM. Mais, même si cela fonctionne, le moindre changement dans le HTML pourrait faire échouer le test. Ce qui aurait quand même un très mauvais rapport coût/bénéfice pour un test comme celui-là.

## Refactorisation, étape 0

Au lieu de tester le HTML, refactorisons juste assez le code pour avoir quelque chose qui se test unitairement.

Pour cela, nous avons besoin d'effectuer deux changements : passer la date actuelle en paramètre à la fonction ```prettyDate``` en utilisant ```new Date``` au lieu de l'écrire en dur, puis déplacer la fonction dans un fichier à part afin que nous puissions inclure le code sur une page séparée et effectuer nos tests unitaires.

~~~
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <title>Refactored date examples</title>
    <script src="prettydate.js"></script>
    <script>
    window.onload = function() {
        var links = document.getElementsByTagName("a");
        for ( var i = 0; i < links.length; i++ ) {
            if (links[i].title) {
                var date = prettyDate("2008-01-28T22:25:00Z", links[i].title);
                if (date) {
                    links[i].innerHTML = date;
                }
            }
        }
    };
    </script>
</head>
<body>

<ul>
<li class="entry" id="post57">
    <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Curabitur dignissim quis libero eu lobortis.</p>
    <small class="extra">
        Posted <a href="/2008/01/blah/57/" title="2008-01-28T20:24:17Z">January 28th, 2008</a>
        by <a href="/john/">John Resig</a>
    </small>
</li>
<!-- more list items -->
</ul>

</body>
</html>
~~~
{:lang="html"}

Et voici le contenu de ```prettydate.js``` :

~~~
function prettyDate(now, time){
    var date = new Date(time || ""),
        diff = (((new Date(now)).getTime() - date.getTime()) / 1000),
        day_diff = Math.floor(diff / 86400);

    if (isNaN(day_diff) || day_diff < 0 || day_diff >= 31) {
        return;
    }

    return day_diff == 0 && (
            diff < 60 && "à l'instant" ||
            diff < 120 && "1 minute" ||
            diff < 3600 && Math.floor( diff / 60 ) + " minutes" ||
            diff < 7200 && "1 heure" ||
            diff < 86400 && Math.floor( diff / 3600 ) + " heures") ||
        day_diff == 1 && "Hier" ||
        day_diff < 7 && day_diff + " jours" ||
        day_diff < 31 && Math.ceil( day_diff / 7 ) + " semaines";
}
~~~
{:lang="js"}

Maintenant que nous avons quelque chose à tester, écrivons de vrais tests unitaires :

~~~
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <title>Refactored date examples</title>
    <script src="prettydate.js"></script>
    <script>
    function test(then, expected) {
        results.total++;
        var result = prettyDate("2008-01-28T22:25:00", then);
        if (result !== expected) {
            results.bad++;
            console.log("Attendu " + expected + ", mais obtenu " + result);
        }
    }
    var results = {
        total: 0,
        bad: 0
    };
    test("2008-01-28T22:24:30", "à l'instant");
    test("2008-01-28T22:23:30", "1 minute");
    test("2008-01-28T21:23:30", "1 heure");
    test("2008-01-27T22:23:30", "Hier");
    test("2008-01-26T22:23:30", "2 jours");
    test("2007-01-26T22:23:30", undefined);
    console.log("De " + results.total + " tests, " + results.bad + " ont échoué, "
        + (results.total - results.bad) + " ont réussi.");
    </script>
</head>
<body>
</body>
</html>
~~~
{:lang="html"}

Nous avons un framework de test adapté, utilisant la console pour afficher les résultats. Il n'y a aucune dépendance au DOM, par conséquent, vous pouvez simplement l'exécuter dans un environnement JavaScript hors navigateur, tel que Node.js ou Rhino, en déplaçant le code de la balise script dans un fichier à part.

Si un test échoue, le résultat du test attendu ainsi que le résultat obtenu vont être affichés. Au final, un résumé du nombre de test en échec et réussis est aussi affiché.

Si tous les tests réussissent, comme ils le devraient, vous devriez voir la chose suivante dans la console :

~~~
De 6 tests, 0 ont échoué, 6 ont réussi.
~~~
{:lang="sh"}

Pour voir ce qu'est une assertion erronée, nous pouvons changer quelque chose afin que le test échoue :

~~~
Attendu 2 jours, mais obtenu 2 jour.
De 6 tests, 1 ont échoué, 5 ont réussi.
~~~
{:lang="sh"}

Bien que cette approche adaptée est intéressant en POC (on peut vraiment écrire un exécuteur de test en quelque lignes de code), il est bien plus pratique de d'utiliser un framework de test existant, qui fourni un meilleur affichage des résultats et plus d'infrastructure pour écrire et organiser les tests.

## Qunit : un outil de test JavaScript

Le choix d'un framework est principalement affaire de goût. Pour le reste de cet article, nous utiliseront QUnit (prononcé "q-unit"), car son style de description des tests est proche du framework de test que nous venons de créer.

~~~
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <title>Refactorisation date</title>

    <link rel="stylesheet" href="//code.jquery.com/qunit/qunit-1.16.0.css">
    <script src="//code.jquery.com/qunit/qunit-1.16.0.js"></script>
    <script src="prettydate.js"></script>

    <script>
    test("prettydate basics", function() {
        var now = "2008/01/28 22:25:00";
        equal(prettyDate(now, "2008/01/28 22:24:30"), "à l'instant");
        equal(prettyDate(now, "2008/01/28 22:23:30"), "1 minute");
        equal(prettyDate(now, "2008/01/28 21:23:30"), "1 heure");
        equal(prettyDate(now, "2008/01/27 22:23:30"), "Hier");
        equal(prettyDate(now, "2008/01/26 22:23:30"), "2 jours");
        equal(prettyDate(now, "2007/01/26 22:23:30"), undefined);
    });
    </script>
</head>
<body>
    <div id="qunit"></div>
</body>
</html>
~~~
{:lang="html"}

Trois sections méritent que nous y portions attention. En dehors de l'habituel HTML boilerplate, nous avons trois fichiers inclus : deux fichiers pour QUnit (```qunit-1.16.0.css``` et ```qunit-1.16.0.js```) et notre script précédent ```prettydate.js```

Ensuite, il y a un autre bloc de script avec les tests à exécuter. La fonction de test ```test()```, appelée une seule fois, reçoit une chaîne de caractère comme premier paramètre (qui donne un nom à la suite de tests) et une fonction comme deuxième paramètre (qui va effectivement exécuter le code pour ce test). Le code initialise une variable ```now```, qui est réutilisée plus bas, puis apelle plusieurs fois la fonction ```equal``` avec variété de paramètres. La fonction ```equal``` est une assertion que QUnit fourni parmi tant d'autres. Le premier paramètre est le résultat d'un appel à la fonction prettyDate, avec maintenant la variable ```now``` en premier paramètre et une date en second. Le second paramètre est le resultat qui est attendu. Si les deux paramètres ont la même valeur à la fin, le test va passer, sinon il échoue.

Enfin, on peut voir dans le ```body``` quelques éléments spécifiques à QUnit (```<div id="qunit"></div>```). Ces éléments sont optionnels et permettent seulement d'afficher le résultat des tests.

Voici le résultat :

{% jsfiddle sdnthswp result %}

Avec un test en échec, nous avons :

{% jsfiddle dw0meLxh result %}

Puisque le test contient une assertion en échec, QUnit ne cache pas les résultats du test, et nous pouvons voir immédiatement ce qui s'est mal passé. Nous obtenons à la fois un affichage des résultats attendus et ceux obtenus, et un diff entre les deux. Ce qui est utile pour comparer des chaînes de caractères plus longue. Dans le cas présent, ce qui s'est mal passé est évident.


## Refactorisation, étape 1

Les assertions sont quelque peu incomplètes car nous ne testons pas la variante "n semaines". Avant de l'ajouter, nous devons considérer une refonte du code. Aujourd'hui, on fait appel à ```prettyDate``` pour chaque assertion et on lui passe le paramètre ```now```. Nous pourions facilement refactoriser cela en une fonction d'assertion personnalisée :

~~~
test("prettydate basics", function() {
    function date(then, expected) {
        equal(prettyDate("2008/01/28 22:25:00", then), expected);
    }
    date("2008/01/28 22:24:30", "à l'instant");
    date("2008/01/28 22:23:30", "1 minute");
    date("2008/01/28 21:23:30", "1 heure");
    date("2008/01/27 22:23:30", "Hier");
    date("2008/01/26 22:23:30", "2 jours");
    date("2007/01/26 22:23:30", undefined);
});
~~~
{:lang="js"}

Ici nous avons déplacé l'appel à ```prettyDate``` dans une fonction ```date```, en insérant la variable ```now``` directement dans la fonction. Nous obtenons alors des données pertinentes pour chaque assertion, plus faciles à lire, tout en gardant un niveau d'abstraction assez évident.

## Tester la manipulation du DOM

Maintenant que la fonction ```prettyDate``` est suffisamment bien testée, retournons à notre exemple de départ. En plus de la fonction ```prettyDate```, le code sélectionne quelques éléments du DOM et les mets à jour, tout cela au sein d'un événement ```window.load```. En appliquant les mêmes principes qu'avant, nous devrions être capable de refactoriser ce code et de le tester. Ajouter à cela, nous présenterons un module pour ces deux fonctions qui permettra d'éviter la pollution de la scope globale et qui permettra aussi de donner un nom qui a plus de sens à ces deux fonctions.

~~~
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <title>Refactored date examples</title>
    <link rel="stylesheet" href="//code.jquery.com/qunit/qunit-1.16.0.css">
    <script src="//code.jquery.com/qunit/qunit-1.16.0.js"></script>
    <script src="prettydate2.js"></script>
    <script>
    test("prettydate.format", function() {
        function date(then, expected) {
            equal(prettyDate.format("2008/01/28 22:25:00", then), expected);
        }
        date("2008/01/28 22:24:30", "à l'instant");
        date("2008/01/28 22:23:30", "1 minute");
        date("2008/01/28 21:23:30", "1 heure");
        date("2008/01/27 22:23:30", "Hier");
        date("2008/01/26 22:23:30", "2 jours");
        date("2007/01/26 22:23:30", undefined);
    });

    test("prettyDate.update", function() {
        var links = document.getElementById("qunit-fixture").getElementsByTagName("a");
        equal(links[0].innerHTML, "January 28th, 2008");
        equal(links[2].innerHTML, "January 27th, 2008");
        prettyDate.update("2008-01-28T22:25:00Z");
        equal(links[0].innerHTML, "2 heures");
        equal(links[2].innerHTML, "Hier");
    });

    test("prettyDate.update, un jour plus tard", function() {
        var links = document.getElementById("qunit-fixture").getElementsByTagName("a");
        equal(links[0].innerHTML, "January 28th, 2008");
        equal(links[2].innerHTML, "January 27th, 2008");
        prettyDate.update("2008-01-29T22:25:00Z");
        equal(links[0].innerHTML, "Hier");
        equal(links[2].innerHTML, "2 jours");
    });
    </script>
</head>
<body>
    <div id="qunit"></div>
    <div id="qunit-fixture">
        <ul>
            <li class="entry" id="post57">
                <p>Lorem ipsum</p>
                <small class="extra">
                    Posté il y a <span class="time"><a href="/2008/01/blah/57/" title="2008-01-28T20:24:17Z">January 28th, 2008</a></span>
                    par <span class="author"><a href="/john/">John Resig</a></span>
                </small>
            </li>
            <li class="entry" id="post57">
                <p>Lorem ipsum</p>
                <small class="extra">
                    Posté il y a <span class="time"><a href="/2008/01/blah/57/" title="2008-01-27T22:24:17Z">January 27th, 2008</a></span>
                    par <span class="author"><a href="/john/">John Resig</a></span>
                </small>
            </li>
        </ul>
    </div>
</body>
</html>
~~~
{:lang="html"}

Voici le contenu de ```prettydate2.js``` :

~~~
var prettyDate = {
    format: function(now, time){
        var date = new Date(time || ""),
            diff = (((new Date(now)).getTime() - date.getTime()) / 1000),
            day_diff = Math.floor(diff / 86400);

        if (isNaN(day_diff) || day_diff < 0 || day_diff >= 31) {
            return;
        }

        return day_diff === 0 && (
                diff < 60 && "à l'instant" ||
                diff < 120 && "1 minute" ||
                diff < 3600 && Math.floor( diff / 60 ) + " minutes" ||
                diff < 7200 && "1 heure" ||
                diff < 86400 && Math.floor( diff / 3600 ) + " heures") ||
            day_diff === 1 && "Hier" ||
            day_diff < 7 && day_diff + " jours" ||
            day_diff < 31 && Math.ceil( day_diff / 7 ) + " semaines";
    },

    update: function(now) {
        var links = document.getElementsByTagName("a");
        for ( var i = 0; i < links.length; i++ ) {
            if (links[i].title) {
                var date = prettyDate.format(now, links[i].title);
                if (date) {
                    links[i].innerHTML = date;
                }
            }
        }
    }
};
~~~
{:lang="js"}

La nouvelle fonction ```prettyDate.update``` est un extrait de l'exemple initial mais avec le paramètre ```now``` que nous passons à la fonction ```prettyDate.format```. Le test de base de QUnit pour cette fonction commence par selectionner tous les éléments au sein de l'élément ```#qunit-fixture```. ```<div id="qunit-fixture"></div>``` est un nouvel élément dans le ```body```. Il contient un extrait du markup de notre exemple initial, suffisamment conséquent pour être éprouver par des tests. En le placant dans l'élément ```#qunit-fixture```, nous n'avons pas à nous inquiéter d'éventuels changements de DOM suite à l'execution d'un test précédent, car QUNit va automatiquement ré-initialiser le markup après chaque test.

Regardons au premier test effectué pour ```prettyDate.update```. Après avoir sélectionné les liens, deux assertions vérifient que ceux-ci ont bien leurs valeurs initiales. Après quoi, ```prettyDate.update``` est appelée, en lui passant une date donnée (identique à celle des tests précédents). Ensuite, deux assertions sont exécutées, vérifiant que le innerHTML de ces éléments ont la date au bon format "2 heures" et "Hier".

## Refactorisation, étape 2

Le test suivant, ```prettyDate.update, un jour plus tard``` fait à peu près la même chose, hormis qu'il passe une date différente à ```prettyDate.update``` et ainsi attend un résultat différent pour les deux liens. Voyons si nous pouvons refactoriser ces tests pour supprimer le code dupliqué.

~~~
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <title>Refactored date examples</title>
    <link rel="stylesheet" href="//code.jquery.com/qunit/qunit-1.16.0.css">
    <script src="//code.jquery.com/qunit/qunit-1.16.0.js"></script>
    <script src="prettydate2.js"></script>
    <script>
    test("prettydate.format", function() {
        function date(then, expected) {
            equal(prettyDate.format("2008/01/28 22:25:00", then), expected);
        }
        date("2008/01/28 22:24:30", "à l'instant");
        date("2008/01/28 22:23:30", "1 minute");
        date("2008/01/28 21:23:30", "1 heure");
        date("2008/01/27 22:23:30", "Hier");
        date("2008/01/26 22:23:30", "2 jours");
        date("2007/01/26 22:23:30", undefined);
    });

    function domtest(name, now, first, second) {
        test(name, function() {
            var links = document.getElementById("qunit-fixture").getElementsByTagName("a");
            equal(links[0].innerHTML, "January 28th, 2008");
            equal(links[2].innerHTML, "January 27th, 2008");
            prettyDate.update(now);
            equal(links[0].innerHTML, first);
            equal(links[2].innerHTML, second);
        });
    }
    domtest("prettyDate.update", "2008/01/28 22:25:00", "2 heures", "Hier");
    domtest("prettyDate.update, one day later", "2008/01/29 22:25:00", "Hier", "2 jours");
    </script>
</head>
<body>
    <div id="qunit"></div>
    <div id="qunit-fixture">
        <ul>
            <li class="entry" id="post57">
                <p>blah blah blah…</p>
                <small class="extra">
                    Posted <span class="time"><a href="/2008/01/blah/57/" title="2008/01/28 20:24:17">January 28th, 2008</a></span>
                    by <span class="author"><a href="/john/">John Resig</a></span>
                </small>
            </li>
            <li class="entry" id="post57">
                <p>blah blah blah…</p>
                <small class="extra">
                    Posted <span class="time"><a href="/2008/01/blah/57/" title="2008/01/27 22:24:17">January 27th, 2008</a></span>
                    by <span class="author"><a href="/john/">John Resig</a></span>
                </small>
            </li>
        </ul>
    </div>
</body>
</html>
~~~
{:lang="html"}

Nous avons une nouvelle fonction ```domtest```, qui encapsule la logique des deux tests précédents, avec comme paramètre le nom du test, la date et les deux chaînes de caractères attendues. Elle est donc appelée deux fois.

## Retour au départ

Ces choses en place, retournons à notre exemple de départ et regardons à quoi ça ressemble suite à la refactorisation.

~~~
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <title>Final date examples</title>
    <script src="prettydate2.js"></script>
    <script>
    window.onload = function() {
        prettyDate.update("2008-01-28T22:25:00Z");
    };
    </script>
</head>
<body>

<ul>
<li class="entry" id="post57">
    <p>Lorem ipsum</p>
    <small class="extra">
        Posté il y a <span class="time"><a href="/2008/01/blah/57/" title="2008-01-28T20:24:17Z"><span>January 28th, 2008</span></a></span>
        par <span class="author"><a href="/john/">John Resig</a></span>
    </small>
</li>
<li class="entry" id="post57">
    <p>Lorem ipsum</p>
    <small class="extra">
        Posté il y a <span class="time"><a href="/2008/01/blah/57/" title="2008-01-27T22:24:17Z"><span>January 27th, 2008</span></a></span>
        par <span class="author"><a href="/john/">John Resig</a></span>
    </small>
</li>
<li class="entry" id="post57">
    <p>Lorem ipsum</p>
    <small class="extra">
        Posté il y a <span class="time"><a href="/2008/01/blah/57/" title="2008-01-26T22:24:17Z"><span>January 26th, 2008</span></a></span>
        par <span class="author"><a href="/john/">John Resig</a></span>
    </small>
</li>
<li class="entry" id="post57">
    <p>Lorem ipsum</p>
    <small class="extra">
        Posté il y a <span class="time"><a href="/2008/01/blah/57/" title="2008-01-25T22:24:17Z"><span>January 25th, 2008</span></a></span>
        par <span class="author"><a href="/john/">John Resig</a></span>
    </small>
</li>
<li class="entry" id="post57">
    <p>Lorem ipsum</p>
    <small class="extra">
        Posté il y a <span class="time"><a href="/2008/01/blah/57/" title="2008-01-24T22:24:17Z"><span>January 24th, 2008</span></a></span>
        par <span class="author"><a href="/john/">John Resig</a></span>

    </small>
</li>
<li class="entry" id="post57">
    <p>Lorem ipsum</p>
    <small class="extra">
        Posté il y a <span class="time"><a href="/2008/01/blah/57/" title="2008-01-14T22:24:17Z"><span>January 14th, 2008</span></a></span>
        par <span class="author"><a href="/john/">John Resig</a></span>
    </small>
</li>
<li class="entry" id="post57">
    <p>Lorem ipsum</p>
    <small class="extra">
        Posté il y a <span class="time"><a href="/2008/01/blah/57/" title="2008-01-04T22:24:17Z"><span>January 4th, 2008</span></a></span>
        par <span class="author"><a href="/john/">John Resig</a></span>
    </small>
</li>
<li class="entry" id="post57">
    <p>Lorem ipsum</p>
    <small class="extra">
        Posté il y a <span class="time"><a href="/2008/01/blah/57/" title="2007-12-15T22:24:17Z"><span>December 15th, 2008</span></a></span>
        par <span class="author"><a href="/john/">John Resig</a></span>
    </small>
</li>
</ul>

</body>
</html>
~~~
{:lang="html"}

Dans un exemple non statique, on supprimerait le paramètre de ```prettyDate.update```. Globalement, la refactorisation est une énorme amélioration par rapport à l'exemple initiale. Grâce au module ```prettyDate``` que nous avons conçu, nos pouvons même y ajouter d'avantage de fonctionnalités sans polluer la scope globale.

## Conclusion

Le test JavaScript n'est pas seulement une affaire d'utiliser un exécuteur de test et écrire quelques tests; cela demande habituellement des changements structurel assez lourds quand il s'agit de code qui n'a été testé auparavant que manuellement. Nous avons suivi pas à pas un exemple de comment changer la structure du code d'un module existant pour exécuter des tests dessus, en utilisant un framework de test adapté. Puis nous avons remplacer ce framework par un autre qui possède plus de fonctionnalités et nous donne des retours visuels.
QUnit a beaucoup à offrir : test de code asynchrone tel que les ```timeouts```, AJAX et les événements. Son exécuteur de test visuel nous aide à débugger le code en rendant facile la ré-exécution de tests spécifique et en fournissant des piles de traces pour les assertions qui ont échoués et les exceptions qui ont été attrapées. Pour aller plus loin, lisez le livre QUnit Cookbook.


<p class="disclaimer">
Cet article est traduit de l'anglais à partir d'un article paru sur Smashing Magazine. J'ai donc tâché de rester fidèle à l'opinion de l'auteur. Par moments, les formulations ont du être tournée autrement afin de faciliter la lecture et la compréhension. Pour toute amélioration ou suggestion, n'hésitez pas à me contacter.<br/>
</p>
