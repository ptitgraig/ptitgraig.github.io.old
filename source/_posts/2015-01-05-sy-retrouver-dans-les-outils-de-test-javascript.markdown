---
layout: post
title: "S'y retrouver dans les outils de test Javascript"
date: 2015-01-05 14:21
comments: true
categories: Dev
---

<p class="foreword">
Entre Jasmine, Sinon.js, Karma, QUnit tu ne sais plus où donner de la tête ? Voici un bref récapitulatif des outils pour tester du Javascript.
Cet article sera enrichi au fur et à mesure des mes connaissances sur les sujets.
</p>
<!-- more -->

## Définitions

### Test-Driven Development (TDD)

Le Test Driven Development (TDD) ou en français développement piloté par les tests est une technique de développement de logiciel qui préconise d'écrire les tests unitaires avant d'écrire le code source d'un logiciel [^1].

Le cycle préconisé par TDD comporte 5 étapes :

1. écrire un premier test;
2. vérifier qu'il échoue (car le code qu'il teste n'existe pas), afin de vérifier que le test est valide;
3. écrire juste le code suffisant pour passer le test;
4. vérifier que le test passe;
5. puis refactoriser le code.

Les intérêts :

* facilite la production de code valide en toutes circonstances
* on utilise le programme avant même qu'il existe
* évite des erreurs de conceptions dues à la précipitation avant d'avoir défini les objectifs
* plus grande assurance lors du refactoring, tu sais que les tests ont fonctionné à un moment donné. Tu peux ainsi te permettre des changements radicaux dans le code

Une chose importante est que les demandeurs doivent formaliser la manière dont ils testeront la fonctionnalité. C'est le travail du responsable applicatif de s'en assurer.

### Behavior-Driven Development (BDD)

Behavior Driven Development (ou BDD) est une méthode Agile qui encourage la collaboration entre les développeurs, les responsables qualités, les intervenants non-techniques et les entreprises participant à un projet de logiciel. Il a été conçu en 2003 par Dan North comme une réponse au Test Driven Development. [^2]

Le processus BDD met en avant le langage naturel et les interactions dans le processus de développement logiciel. Les développeurs utilisant le BDD utilisent leur langue maternelle en combinaison avec le langage du domaine Domaine Driven Design pour décrire l'objectif et le bénéfice de leur code. Cela permet aux développeurs de se concentrer sur les raisons pour lesquelles le code doit être créé, plutôt que les détails techniques, et minimise la traduction entre le langage technique dans lequel le code est écrit et le domaine de la langue parlée par les entreprises, les utilisateurs, les intervenants, la gestion de projet etc...

Dans la pratique :

* on utilise des exemples pour décrire le comportement de la demande;
* on automatise ces exemples pour fournir rapidement des commentaires ou tests de non-regression;
* on utilise le mot 'devrait', ce qui contribue à clarifier la résponsabilité et permet la remise en cause de la fonctionnalité de l'application;
* on utilise 'verifier que', ce qui permet de faire la différence entre les résultats provenant du champ d'application du code et d'autres éléments du code;
* on utilise des 'mocks' (fausse fonction) en remplacant des modules de code qui ne sont pas encore écrits.

### Les bibliotèques d'énoncés (assertions library) 

Une assertion est un énoncé considéré comme vrai. Les bibliothèques d'énoncés permettent de formuler les tests de manière compréhensible.
Quelques bibliothèques d'énoncés :
* should.js
* expect.js
* chai.js

### Les frameworks de test

Un framework de test engloble une ou plusieurs bibliothèques d'énoncés, permet de lancer des tests.

A compléter...


## Les outils

### Jasmine

{% img left /images/test-jasmine.png 140 140 %}
Jasmine est un *framework de test* JavaScript developpement orienté comportement (BDD). Il ne dépend d'aucun autre framework JavaScript.
Il ne requiert pas un DOM et possède une syntaxe propre et évidente, ce qui permet d'écrire facilement tes tests.


### Mocha

{% img left /images/test-mocha.png 140 140 %}
Mocha est un *framework de test* JavaScript riche en fonctionnalités, basé sur Node.js et sur le navigateur, qui rend les tests asynchrones simple et agréable. Les tests Mocha se déroulent de manière séquentielle, ce qui permet d'établir un rapporte précis et flexible, tout en faisant concorder les exceptions non interceptées au bon cas de test.


### Karma (anciennement Testacular)

{% img left /images/test-karma.png 140 140 %}
Karma est un *environnement de test*. Le but principal de Karma est d'offrir aux développeurs un environnement de test productif. Plutôt que de demander de mettre en place tout un tas de configurations, Karma te permet d'écire le code est d'obtenir un retour instantané de tes tests. La philosphie de Karma est : une retour rapide sur ton code, c'est ce qui te rend productif et créatif. C'est une opinion.

Karma est essentiellement un outil qui génère un serveur Web qui exécute le code source en se basant sur le code de test, et ce pour chaque navigateurs connectés. Les résultats de chaque test contre sur chaque navigateur sont calculées et affichées sur la ligne de commande de telle sorte que tu puisses voir quels sont les tests qui ont réussis et échoués, et ce sur chaque navigateur.
Tu peux tester le résulat sur un navigateur soit manuellement, en visitant l'URL écoutée par le serveur Karma (habituellement http://localhost:9876/), soit
automatiquement en configurant Karma de sorte qu'il sache quel navigateur démarrer quand Karma est exécuté.

Karma surveille aussi tous les fichiers listés dans le fichier de configuration. Dès qu'un fichier change, Karma déclenche les tests en envoyant un signal 
au serveur de test pour informer tout les navigayeurs connectés d'executer le code de test à nouveau. Chaque navigateur charge ensuite le code source dans une IFrame, exécute les tests, et rapporte les résultats au serveur.
Le serveur collecte les résultats de l'ensemble des navigateurs connectés et te les présente.


### Intern

{% img left /images/test-intern.png 140 140 %}
Intern est *une pile de test complète pour Javascript*, conçue pour t'aider à écrire et exécuter des cas de test, pour applications et biblothèques de scripts, cohérent et de haute qualité. Intern peut être utilisé pour tester n'importe quel code JavaScript. Ses capacités de test fonctionnel peuvent même être utilisé pour tester des applications mobile et web non JavaScript - si tu le veux vraiment.


### sinon.js

Espions de test autonome et mocks pour JavaScript.
Aucune dépendances, fonctionne avec n'importe quel framework JavaScript.
Un espion de test est une fonction qui enregistre les paramètres, retourne la valeur de ```this``` et l'exception levée - si présente.
Un espion de test peut être une fonction anonyme ou peut être encapsuler dans une autre fonction.

~~~
"test should call subscribers on publish": function () {
    var callback = sinon.spy();
    PubSub.subscribe("message", callback);

    PubSub.publishSync("message");

    assertTrue(callback.called);
}
~~~
{:lang="js"}


### should.js

Enoncés (assertions) de style Behavior-Driven-Developemnt (BDD) pour Node.js, indépendant de tout framework de test.

~~~
var should = require('should');

var user = {
    name: 'tj'
  , pets: ['tobi', 'loki', 'jane', 'bandit']
};

user.should.have.property('name', 'tj');
user.should.have.property('pets').with.lengthOf(4);

// Si l'objet a été créé avec Object.create(null)
// alors il n'hérite pas de `Object.prototype`, donc il n'aura pas une getter `.should`
// donc tu peux faire :
should(user).have.property('name', 'tj');

// aussi, tu peux tester de cette manières les `null`
should(null).not.be.ok;

someAsyncTask(foo, function(err, result){
  should.not.exist(err);
  should.exist(result);
  result.bar.should.equal(foo);
});
~~~
{:lang="js"}


### expect.js

Outils d'enoncés minimalistes de style BDD pour Node.JS et pour navigateurs, basé sur should.js

* Cross-browser : fonctionne sur IE6+, Firefox, Safari, Chrome, Opera.
* Compatible avec tous les frameworks de test.
* Prête pour Node.JS (require('expect.js')).
* Autonome. Global seule sans extensions de prototype ou brique (shims)

~~~
expect(window.r).to.be(undefined);
expect({ a: 'b' }).to.eql({ a: 'b' })
expect(5).to.be.a('number');
expect([]).to.be.an('array');
expect(window).not.to.be.an(Image);
~~~
{:lang="js"}

### chai.js

{% img left /images/test-chai.png 140 140 %}
Chai est une bibliothèque d'énoncés BDD / TDD pour node.js et les navigateurs qui peut être couplé à n'importe quel framework de test JavaScript

### qUnit

A venir.

[^1]: http://fr.wikipedia.org/wiki/Test_Driven_Development
[^2]: http://fr.wikipedia.org/wiki/Behavior_Driven_Development

